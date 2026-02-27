# BotBrowser

Token-efficient web content extraction for LLM agents. Strips the bloat, keeps the content, saves your tokens.

## The Problem

A typical web page is **50,000+ tokens**. The useful content? Maybe **2,000–5,000 tokens**. That's 90–95% waste — scripts, styles, ads, navigation, footers, tracking pixels.

BotBrowser extracts just the content and returns clean markdown, optimized for LLM consumption.

## Install

### JavaScript / TypeScript

```bash
npm install botbrowser
```

### Python

```bash
pip install botbrowser
```

## Quick Start

### JavaScript / TypeScript

```typescript
import { extract } from 'botbrowser';

const result = await extract('https://example.com/article');

console.log(result.content);                       // clean markdown
console.log(result.metadata.tokenSavingsPercent);   // e.g. 94
console.log(result.title);                          // page title
console.log(result.links);                          // extracted links
```

### Python

```python
from botbrowser import extract

result = extract("https://example.com/article")

print(result.content)                          # clean markdown
print(result.metadata.token_savings_percent)   # e.g. 94
print(result.title)                            # page title
print(result.links)                            # extracted links
```

## Output

Both packages return the same structure:

```json
{
  "url": "https://example.com/article",
  "title": "Article Title",
  "description": "Meta description of the page",
  "content": "# Article Title\n\nClean markdown content...",
  "textContent": "Plain text version of the content...",
  "links": [
    { "text": "Related Article", "href": "https://example.com/related" }
  ],
  "metadata": {
    "rawTokenEstimate": 52000,
    "cleanTokenEstimate": 3200,
    "tokenSavingsPercent": 94,
    "wordCount": 1250,
    "fetchedAt": "2026-02-26T10:30:00.000Z"
  }
}
```

## Options

```typescript
// JavaScript
const result = await extract({
  url: 'https://example.com',
  format: 'text',          // "markdown" (default) or "text"
  timeout: 10000,          // request timeout in ms (default: 15000)
  includeLinks: false,     // extract links (default: true)
});
```

```python
# Python
result = extract(
    "https://example.com",
    format="text",          # "markdown" (default) or "text"
    timeout=10000,          # request timeout in ms (default: 15000)
    include_links=False,    # extract links (default: True)
)
```

## Optional: REST API Server

For language-agnostic access or shared infrastructure, run the self-hostable REST API:

```bash
# With Docker
docker compose up

# Or directly
cd js && pnpm install && pnpm build && pnpm dev
```

Then call it from any language:

```bash
curl -X POST http://localhost:3000/extract \
  -H 'Content-Type: application/json' \
  -d '{"url": "https://example.com"}'
```

### Python Client Mode

If you're running the REST API server, you can use the Python client:

```python
from botbrowser import BotBrowserClient

client = BotBrowserClient("http://localhost:3000")
result = client.extract("https://example.com")
```

## How It Works

1. **Fetch** — Smart HTTP fetching with user-agent rotation, redirect handling, timeouts
2. **Extract** — Identifies main content using Mozilla Readability (JS) / Trafilatura (Python)
3. **Clean** — Strips scripts, styles, ads, nav, footers, cookie banners, hidden elements
4. **Convert** — Produces clean Markdown preserving structure (headings, lists, links, tables)
5. **Report** — Returns token estimates so you can see the savings

## Development

```bash
# JS — build and test
cd js && pnpm install && pnpm build && pnpm test

# Python — install and test
cd python && pip install -e ".[dev]" && pytest tests/ -v

# Run the API server locally
cd js && pnpm dev
```

## Publishing

Packages are published automatically via GitHub Actions when a release is created:

- **npm**: `botbrowser` (core) and `@botbrowser/server`
- **PyPI**: `botbrowser`

### Setup required:

**For npm:** Add `NPM_TOKEN` secret to your GitHub repo settings (get from npmjs.com > Access Tokens).

**For PyPI:** Configure [trusted publishing](https://docs.pypi.org/trusted-publishers/) in PyPI for your GitHub repo, or add `PYPI_API_TOKEN` secret.

## License

MIT
