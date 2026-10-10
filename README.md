# ai-visibility-mcp

A free, hosted MCP server that shows what AI assistants can read on a business website, and whether an AI answer cites that website. No account, no API key.

It gives your assistant (Claude, Cursor, VS Code or any other MCP client) two read-only tools:

- **Inspect a website's facts.** Reads up to five public pages and returns what can be extracted from them without a browser: sections, tables, headings, links and the structured business data (schema.org) the site declares.
- **Check an AI answer.** Paste an answer from ChatGPT, Perplexity, Gemini or another assistant, plus the business URL. You get back whether the answer links to the website, only names the brand, or leaves it out.

The server returns evidence and your assistant does the analysis, so every finding points back to a page or answer you can check yourself.

```
https://api.surfacedby.com/api/v1/mcp/public/
```

Transport: Streamable HTTP. Authentication: none.

## Install

### Claude Code

```bash
claude mcp add --transport http surfacedby-ai-visibility https://api.surfacedby.com/api/v1/mcp/public/
```

### Claude (claude.ai and desktop)

Go to Customize > Connectors, click **+ Add**, then **Add custom connector**. Name it `SurfacedBy AI Visibility`, paste the URL above, and choose **No sign in**. Turn it on in a chat from **+ > Connectors**.

On Team and Enterprise plans an Owner adds it under Organization settings > Connectors first. The Free plan allows one custom connector.

### Cursor

Add to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "surfacedby-ai-visibility": {
      "url": "https://api.surfacedby.com/api/v1/mcp/public/"
    }
  }
}
```

### VS Code

Add to `.vscode/mcp.json`:

```json
{
  "servers": {
    "surfacedby-ai-visibility": {
      "type": "http",
      "url": "https://api.surfacedby.com/api/v1/mcp/public/"
    }
  }
}
```

### Other clients

Any client that supports remote Streamable HTTP servers can connect with the URL above. For a client that only speaks stdio, use a bridge such as `mcp-remote`:

```json
{
  "mcpServers": {
    "surfacedby-ai-visibility": {
      "command": "npx",
      "args": ["mcp-remote", "https://api.surfacedby.com/api/v1/mcp/public/"]
    }
  }
}
```

### ChatGPT

A ChatGPT app built on this server is in review. Status and details: [surfacedby.com/tools/chatgpt-ai-visibility](https://surfacedby.com/tools/chatgpt-ai-visibility).

## Try it

```
I run yourbrand.com. When people ask AI for the best tool in our category,
what could stop it from recommending us, and what should we fix first?
```

```
Here is what ChatGPT said when I asked about my business: "<paste the answer>".
My site is https://yourbrand.com. Does it actually cite us, or only name us?
Is anything in it wrong compared with our own pages?
```

```
Compare the pricing and feature pages of competitor-a.com and competitor-b.com
for a small business choosing between them. Use only what their pages say,
and list what is unknown.
```

```
Check https://yourbrand.com/pricing. Which facts on it can an AI system read,
and what is missing or unclear?
```

## Tools

### `surfacedby_inspect_page_evidence`

| Input | Type | Notes |
|---|---|---|
| `urls` | array of strings | 1 to 5 public HTTP(S) URLs. Bare domains are accepted. |

Returns, per URL, the readable content an AI system can extract: title, description, headings, sections, tables, internal and external links and declared structured data, or a clear reason the page was unavailable (crawler access restricted by robots.txt, a bot challenge, an unsupported format, an error). When several pages from the same site are inspected together, it also lists business facts their structured data states differently, such as two phone numbers or two addresses for the same business.

Read-only, idempotent, fetches the open web.

### `surfacedby_analyze_answer_evidence`

| Input | Type | Notes |
|---|---|---|
| `answer` | string | The AI answer text, up to 64,000 characters. Markdown links are read. |
| `business_url` | string | The business website to look for. |
| `brand_name` | string, optional | Helps detect mentions where the name differs from the domain. |
| `language` | string, optional | Language of the answer. |

Returns the brand's presence in that one answer (`cited`, `named` or `not_observed`), the citations visible in the answer, and which of them point to the business.

Read-only, idempotent, makes no network requests.

## What it does not do

- It does not measure AI visibility. One answer is one observation, and a page inspection is not a ranking or a score.
- It does not run JavaScript, log in, or get past bot challenges. A page that needs those comes back as unavailable or partial rather than guessed.
- It does not change any website or publish anything.

If you need visibility measured over time (which prompts mention you on ChatGPT, Perplexity, Gemini and other AI platforms, which competitors appear instead, and which sources they cite), that is what [SurfacedBy](https://surfacedby.com) does. You can run a one-off check with the [free AI visibility checker](https://surfacedby.com/tools/ai-visibility-checker).

## Limits and fair use

- Up to 5 URLs per inspection call.
- The server is shared and free, with daily and monthly request budgets and a daily cap per destination website. When a budget is used up, the tools return a clear error until it resets.
- Fetches use the user agent `SurfacedByEvidence/1.0 (+https://surfacedby.com/tools/chatgpt-ai-visibility)`, respect robots.txt rules for it, and follow only validated public redirects.

## Privacy

Page snapshots are cached for up to 24 hours so repeat checks are fast, then deleted. Answers you submit are analyzed in the request and not stored. Usage is recorded as daily totals, plus a hashed per-site count that resets every day to stop one website being hit too often. No prompts, answers or user identifiers are kept. Calls are never linked to SurfacedBy accounts or website visitors.

Privacy policy: [surfacedby.com/privacy](https://surfacedby.com/privacy). Terms: [surfacedby.com/terms](https://surfacedby.com/terms).

## Support

Open an issue in this repository or contact us at [surfacedby.com/contact](https://surfacedby.com/contact).

## About

Built by [Ali Khallad](https://github.com/bomsn) at [SurfacedBy](https://surfacedby.com), which tracks how AI platforms mention, cite and recommend brands.

This repository holds the documentation and registry metadata for the hosted server. The server itself runs as part of the SurfacedBy platform.

## License

MIT for the contents of this repository.
