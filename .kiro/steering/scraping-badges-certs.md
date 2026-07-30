---
inclusion: manual
---

# Scraping Badges & Certifications — Lessons & Best Practices

Panduan ini berisi pelajaran dari pengalaman scraping platform badge/certification milik ifanzalukhu97. Tujuannya agar AI langsung tahu cara paling efisien untuk setiap platform tanpa trial-and-error yang menyebabkan timeout.

## General Principles

1. **Jangan langsung pakai Chrome DevTools untuk semua platform.** Beberapa platform render server-side dan `web_fetch` jauh lebih cepat.
2. **Set timeout expectations.** Beberapa platform butuh 10-15 detik untuk render JS. Gunakan `wait_for` dengan text yang spesifik.
3. **Prioritaskan `evaluate_script` over `take_snapshot`** untuk extract data terstruktur — snapshot bisa sangat besar dan lambat di halaman dengan banyak elemen.
4. **Jangan scrape halaman yang butuh login.** LinkedIn, beberapa halaman Dicoding — skip dan rely on GitHub README sebagai source of truth.

## Platform-Specific Playbook

### 1. GitHub README (Source of Truth)

**Method:** `web_fetch` (cepat, selalu berhasil)  
**URL:** `https://github.com/ifanzalukhu97`  
**Tips:**
- Fetch raw README langsung: `https://raw.githubusercontent.com/ifanzalukhu97/ifanzalukhu97/main/README.md`
- Ini akan memberikan semua links dan info yang di-declare oleh user
- Tidak perlu Chrome DevTools

### 2. Google Cloud Skills Boost

**Method:** `web_fetch` dulu, fallback ke Chrome DevTools  
**URL:** `https://www.skills.google/public_profiles/ed12b36a-c4cb-466b-bfb8-83f5fd0b74d1`  
**Data yang dicari:** League, points, total badges  
**Tips:**
- Halaman ini kadang render partial via server-side — `web_fetch` mode `selective` dengan searchPhrase "badge" atau "point" sering cukup
- Jika `web_fetch` kosong/incomplete, baru pakai Chrome DevTools
- Jika pakai Chrome DevTools: `wait_for` text "badge" atau "Diamond" sebelum snapshot
- Data biasanya ada dalam elemen statistik di bagian atas profil

### 3. Credly

**Method:** Chrome DevTools MCP (wajib — fully JS-rendered)  
**URL:** `https://www.credly.com/users/ifanzalukhu97`  
**Data yang dicari:** Total badges, daftar badge names, issuers  
**Tips:**
- Halaman ini 100% JS-rendered, `web_fetch` akan kosong
- **Timeout trap:** Halaman bisa lambat load. Gunakan `wait_for` dengan text "badge" dan timeout 15000ms
- Setelah page load, LANGSUNG pakai `evaluate_script` untuk count badges:
  ```javascript
  () => {
    const badges = document.querySelectorAll('[data-testid="badge-card"]');
    if (badges.length === 0) {
      // Fallback selector
      const items = document.querySelectorAll('.cr-public-earned-badge-grid-item');
      return { count: items.length, selector: 'grid-item' };
    }
    return { count: badges.length, selector: 'badge-card' };
  }
  ```
- Jika badge grid belum muncul, scroll down dulu atau tunggu lazy load
- **Pagination:** Credly paginates badges. Check jika ada "Show more" button atau infinite scroll
- **Fallback:** Jika timeout berulang, gunakan ChatGPT search sebagai alternatif

### 4. AWS Skill Builder

**Method:** Chrome DevTools MCP  
**URL:** `https://skillsprofile.skillbuilder.aws/user/ifanzalukhu97/certification-badges`  
**Data yang dicari:** Badge names, dates earned  
**Tips:**
- Halaman ini butuh JS rendering
- `wait_for` text "Earned" atau nama badge yang sudah diketahui (e.g. "SimuLearn")
- Halaman relatif cepat load (5-8 detik)
- Extract dengan `evaluate_script`:
  ```javascript
  () => {
    const badges = document.querySelectorAll('.badge-card, [class*="badge"]');
    return Array.from(badges).map(b => ({
      name: b.querySelector('h3, [class*="title"]')?.textContent?.trim(),
      date: b.querySelector('[class*="date"], time')?.textContent?.trim()
    }));
  }
  ```
- Jika selector tidak match, ambil snapshot dulu untuk lihat struktur aktual

### 5. Microsoft Learn

**Method:** Chrome DevTools MCP  
**URL:** `https://learn.microsoft.com/en-us/users/ifanzalukhu97`  
**Data yang dicari:** Level, XP, badges count, trophies count  
**Tips:**
- Page load cukup cepat tapi stats ada di section tertentu
- `wait_for` text "XP" atau "Level"
- Stats biasanya dalam format: "Level X · XXX,XXX XP"
- Badges dan trophies ada di tab/section terpisah
- **Efficient approach:** Langsung `evaluate_script` setelah wait:
  ```javascript
  () => {
    const text = document.body.innerText;
    const levelMatch = text.match(/Level\s+(\d+)/);
    const xpMatch = text.match(/([\d,]+)\s*XP/);
    const badgeMatch = text.match(/(\d+)\s*badge/i);
    const trophyMatch = text.match(/(\d+)\s*troph/i);
    return {
      level: levelMatch?.[1],
      xp: xpMatch?.[1],
      badges: badgeMatch?.[1],
      trophies: trophyMatch?.[1]
    };
  }
  ```
- Ini jauh lebih cepat daripada take_snapshot lalu parse manual

### 6. Dicoding

**Method:** `web_fetch` dulu  
**URL:** `https://www.dicoding.com/users/ifanzalukhu97/academies`  
**Tips:**
- Profile page kadang accessible via web_fetch
- Jika kosong, Dicoding sering butuh login untuk lihat detail academies
- **Jangan waste time** retry berulang kalau blocked — catat saja "not publicly accessible"
- Fallback: check GitHub README untuk self-reported Dicoding achievements

### 7. Platforms yang SKIP (tidak perlu scrape deeply)

- **LinkedIn** — Butuh login, akan redirect ke login page
- **HackerRank** — Stats biasanya minimal, web_fetch cukup untuk basic profile
- **daily.dev** — DevCard embed URL sudah fix, tidak berubah
- **Dev.to / Medium** — Follower count bisa dari web_fetch jika perlu

## Common Timeout Scenarios & Solutions

| Scenario | Penyebab | Solusi |
|----------|----------|--------|
| Credly blank page | JS belum selesai render | Tambah `wait_for` timeout ke 15000ms, tunggu text "badge" |
| AWS Skill Builder loading forever | CDN lambat | Retry 1x, lalu fallback ke ChatGPT search |
| Microsoft Learn partial content | Tab badges belum di-click | Navigate langsung ke URL dengan tab parameter jika ada |
| Google Skills Boost empty | Cloudflare challenge | Coba `web_fetch` dulu (bypass JS challenge), baru Chrome DevTools |
| take_snapshot terlalu besar | Halaman dengan 100+ elemen | Skip snapshot, langsung `evaluate_script` dengan selector spesifik |

## Optimal Execution Order

Untuk minimasi total waktu:

```
1. web_fetch GitHub README (instant, <2 detik)
2. web_fetch Google Cloud Skills Boost (coba dulu, <3 detik)
3. Chrome DevTools: Credly (paling lambat, start duluan)
   └── Sementara Credly loading...
4. Chrome DevTools: AWS Skill Builder (buka di tab baru)
5. Chrome DevTools: Microsoft Learn (buka di tab baru)
6. web_fetch Dicoding (quick check)
7. Consolidate semua data
```

**Pro tip:** Buka multiple tabs di Chrome DevTools untuk parallel loading. Gunakan `new_page` untuk buka tab baru tanpa menutup yang sedang loading.

## Selector Cheat Sheet

Selectors yang sudah terbukti bekerja (update jika berubah):

```
Platform: Credly
- Badge grid: '.cr-public-earned-badge-grid-item' atau '[data-testid="badge-card"]'
- Badge name: '.cr-standard-grid-item-content__title' atau 'h3'
- Badge count: Hitung jumlah grid items

Platform: AWS Skill Builder  
- Badge container: '.badge-card' atau '[class*="certification"]'
- Badge title: 'h3' atau '[class*="title"]'
- Date: '[class*="date"]' atau 'time'

Platform: Microsoft Learn
- Stats section: '[class*="profile-stats"]' atau '.achievement-section'
- Level/XP: Regex dari body text lebih reliable daripada selector

Platform: Google Cloud Skills Boost
- Points: Biasanya dalam heading atau stat card
- League name: Text content search "Diamond", "Platinum", dll
```

## When All Else Fails

Jika sebuah platform terus timeout atau blocked:

1. **Jangan retry lebih dari 2x** — waste of time
2. **Gunakan ChatGPT search** via `https://chatgpt.com/?q=ifanzalukhu97+[platform]+badges`
3. **Cross-reference dengan GitHub README** — user biasanya update README dengan stats terbaru
4. **Report** platform mana yang gagal di-scrape dan kenapa — user bisa manual verify
