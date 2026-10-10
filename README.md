# ai-visibility-mcp

Find out what AI assistants understand about a business, and whether their answers recommend it. Free and hosted, with no account or API key.

Connect it to Claude, Cursor, VS Code or any MCP client, ask for an AI visibility audit of your website, and your assistant works through a complete, ordered audit:

- **Plan the audit.** A step-by-step checklist: read the site, check crawler access, build the buyer questions, find the real competitors and the third-party sources AI leans on, weigh the evidence, then deliver prioritized, evidence-linked fixes.
- **Audit website pages the way AI reads them.** Products, prices, policies, comparison tables and the structured business data in the page code, from up to five pages at once, plus each page's readiness findings (JavaScript-only rendering, noindex, canonical, schema, answer structure, sources, date and author) with the documentation behind each one.
- **Check AI crawler access.** Which AI crawlers your robots.txt allows, restricts or blocks, what that means for ChatGPT, Perplexity, Gemini, Claude and Google AI Mode, whether a security challenge answers ordinary requests, and the exact fix.
- **Check whether an AI answer cites or names you.** Paste an answer from any assistant. See whether it cites your site, only names you, or leaves you out, what kind of source each citation is, and the likely reason when you are not cited.
- **See where AI platforms get their answers.** The mix of sources each AI platform cites and recent shifts, from SurfacedBy's published [AI Search Index](https://surfacedby.com/ai-search-index), with links to the research behind each finding.

Your assistant does the thinking and SurfacedBy supplies the evidence, so every recommendation links back to a page or published source you can check.

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
Audit the AI visibility of yourbrand.com. We sell to small accounting firms in the UK.
```

```
Can ChatGPT and Perplexity crawl yourbrand.com? If not, what exactly should we change?
```

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

All tools are read-only, free, and need no account.

### `surfacedby_plan_ai_visibility_audit`

| Input | Type | Notes |
|---|---|---|
| `business_url` | string | The business website. Bare domains are accepted. |
| `goal` | string, optional | `full_audit` (default), `compare_competitors`, `check_answers` or `fix_crawler_access`. |
| `market` | string, optional | Country, region or city the business serves. |
| `language` | string, optional | Language of the business and its buyers. |
| `competitors` | array of strings, optional | Up to 5 competitors the user already knows. |

Returns the ordered checklist for that goal, the tools for each step, the quality bar for the result, the rules to follow, the published research findings that apply, and the remaining request budget. Makes no request to the website.

### `surfacedby_inspect_page_evidence`

| Input | Type | Notes |
|---|---|---|
| `urls` | array of strings | 1 to 5 public HTTP(S) URLs. Bare domains are accepted. |

Returns, per URL, the readable content an AI system can extract: title, description, headings, sections, tables, internal and external links and declared structured data, plus the page type, source type and readiness findings, or a clear reason the page was unavailable (crawler access restricted by robots.txt, a bot challenge, an unsupported format, an error). When several pages from the same site are inspected together, it also lists business facts their structured data states differently. Each result carries the remaining request budget and the next step of the audit.

Read-only, idempotent, fetches the open web.

### `surfacedby_check_ai_crawler_access`

| Input | Type | Notes |
|---|---|---|
| `domain` | string | A domain or any URL on the site. |

Returns access for each AI crawler (allowed, restricted or blocked, with the robots.txt rule responsible), split by purpose: training crawlers, whose blocks cost no citations, and search-index and retrieval crawlers, whose blocks do. Adds a row per AI platform with the search index it reads and where to submit pages, whether a security challenge answered an ordinary request, homepage noindex, llms.txt and Content-Signal, and copy-ready fixes. It reads the site's public files and one ordinary request, never impersonates a crawler, and cannot see firewall rules keyed to crawlers' own IP addresses.

Read-only, idempotent, fetches the open web (about three requests).

### `surfacedby_analyze_answer_evidence`

| Input | Type | Notes |
|---|---|---|
| `answer` | string | The AI answer text, up to 64,000 characters. Markdown links are read. |
| `business_url` | string | The business website to look for. |
| `brand_name` | string, optional | Helps detect mentions where the name differs from the domain. |
| `language` | string, optional | Language of the answer. |
| `platform` | string, optional | `chatgpt`, `perplexity`, `gemini`, `claude`, `ai_mode`, `grok`, `deepseek` or `other`. |
| `competitor_domains` | array of strings, optional | Up to 10 competitor domains, so their pages are labelled as competitors. |

Returns the brand's presence in that one answer (`cited`, `named` or `not_observed`), every visible citation with its source type (review site, best-of list, forum, competitor, your site and others), which source types dominate, and, when the business is not cited, the likely reason and fix. Google and Gemini redirect links are read to the pages they point to.

Read-only, idempotent. Makes no network request unless the answer cites through a Google or Gemini redirect link.

### `surfacedby_get_ai_source_landscape`

| Input | Type | Notes |
|---|---|---|
| `platform` | string, optional | Limit the result to one AI platform: `chatgpt`, `perplexity`, `gemini`, `claude`, `ai_mode`, `grok` or `deepseek`. When the index has no figures for that platform yet, the result says so and lists the platforms it does cover. |
| `topic` | string, optional | Select research findings on one topic, such as `reddit`, `crawlers` or `competitors`. |

Returns, per AI platform, the share of citations by source category, recent shifts in specific sources and how its answers cite, from SurfacedBy's published AI Search Index, with the data date and a link to the public page, plus research findings with their samples, dates and post links. These are pooled rates across many brands, not measurements of any one business.

Read-only, idempotent.

## Prompts and resources

Clients that show MCP prompts get four ready-made commands: **Full AI visibility audit**, **Compare me with competitors**, **Check how AI answers describe us** and **Fix my AI crawler access**. Each takes the business URL and optional competitors and market.

Two resources are available to clients that read them: `surfacedby://method/ai-visibility-audit` (the full audit method) and `surfacedby://research/findings` (the published research findings, each with its sample, date and link).

## Track it over time

This server works on the pages and answers in your conversation, and its source data is pooled across many brands. To track how ChatGPT, Perplexity, Gemini and other AI platforms mention your brand over time, which competitors they recommend instead, and which sources they trust, use [SurfacedBy](https://surfacedby.com). Or run a one-off check with the [free AI visibility checker](https://surfacedby.com/tools/ai-visibility-checker).

Pages that need JavaScript, a login or a bot challenge to load are reported as unavailable, never guessed.

## Limits and fair use

- Up to 5 URLs per inspection call. A full audit typically uses about 50 requests across the sites it reads; pages already read in the last 24 hours cost nothing.
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
