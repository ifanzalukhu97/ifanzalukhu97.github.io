# Articles

Find Ifan Jaya Suswanto Zalukhu's most recent technical articles, covering .NET, AWS, AI, and cloud engineering topics.

## How to use

Fetch the public Dev.to API endpoint:

```
GET https://dev.to/api/articles?username=ifanzalukhu97&per_page=4
```

This is the same public Dev.to API endpoint the homepage itself calls client-side to render its "Recent Articles" section, so the data returned is always the same live set of articles the site displays. It returns a JSON array of up to 4 articles, each including fields like `title`, `description`, `url`, and `published_at`.

No API key or authentication is required — Dev.to's article listing endpoint is public. This is a read-only third-party API, not something hosted by this site.
