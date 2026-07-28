---
title: "Building an MCP Server for a Docs-as-Code Site"
description: "A hands-on build: wiring an MCP server on top of the Zensical scaffold from the migration series, tested against Claude Desktop and Cursor with real queries against real docs."
tags:
  - Docs-as-Code
  - AI Assistants
  - MCP
---

# Building an MCP Server for a Docs-as-Code Site: Wiring Zensical Up to Claude and Cursor

[Part 1](intro.md) made the case for why an AI assistant needs a live query into your documentation instead of a stale snapshot or a guess from training data. This post is the build: an actual MCP server sitting on top of the [Zensical scaffold](../migration/zensical-deep-dive.md) from the migration series, tested against Claude Desktop and Cursor with real queries against the real docs on this site.

Same approach as Parts 4 through 7 of that series: a running instance, not a diagram. `[PLACEHOLDER: link to public companion repo once it exists]`

---

## What Zensical already gives you to work with

The [Zensical deep dive](../migration/zensical-deep-dive.md) covered what a stock `zensical new` scaffold ships with: Markdown source in `docs/`, config in `zensical.toml`, and a `zensical build` step that produces a static `site/` directory — including a `search/` folder holding the index for Zensical's own in-browser search.

That in-browser search is built by Disco, the search engine Zensical wrote to replace the aging Lunr.js-based search Material for MkDocs used. Disco is Rust-backed and, as of this series, hasn't been published as a standalone open-source project with a documented index format yet — the team has said that's coming, not that it's here. So "read Zensical's search output" isn't actually an available integration point right now. That constraint shapes the whole design below: the server indexes the same `docs/` Markdown source Zensical itself builds from, not Zensical's build artifact. That's arguably the more correct choice anyway — it's the source of truth, and it means the MCP server has zero dependency on Zensical's internal format ever changing.

## Why build this instead of reaching for an existing plugin

Part 1 named two existing projects worth knowing about: [`docusaurus-plugin-mcp-server`](https://github.com/scalvert/docusaurus-plugin-mcp-server) and the [MkDocs Search MCP server](https://www.npmjs.com/package/@serverless-dna/mkdocs-mcp). Neither targets Zensical. That's the gap this post fills, not a case of reinventing something that already exists for this tool.

## Deciding what the server actually exposes

Two tools, kept deliberately narrow, in keeping with the "scoped retrieval" principle from Part 1:

- **`search_docs`** — takes a query, returns matching page titles, descriptions, and slugs. Not full page text; that keeps a search call cheap.
- **`get_page`** — takes a slug returned by `search_docs` and returns that page's raw Markdown.

The design held. What I underestimated was how much the tool *description* matters — more on that below.

## The build

TypeScript, the official [`@modelcontextprotocol/sdk`](https://www.npmjs.com/package/@modelcontextprotocol/sdk), stdio transport (both Claude Desktop and Cursor spawn MCP servers as local subprocesses over stdio — no hosting needed for this to work locally).

Three moving pieces:

**1. Index the Markdown source, not the build output.** Walk `docs/`, parse frontmatter with `gray-matter`, strip Markdown syntax down to plain text, and feed it to [MiniSearch](https://www.npmjs.com/package/minisearch) — a small, dependency-light full-text index that runs in-process with no external service.

**2. `search_docs`.** Runs a MiniSearch query with prefix and light fuzzy matching, returns the top N results as title/description/slug — not the full page. If nothing matches, it returns a plain "no results" message instead of an empty array, so the assistant doesn't have to guess what silence means.

**3. `get_page`.** Looks up the slug in the in-memory doc list and returns the raw Markdown file straight from disk — not the stripped search text — so the assistant gets headings, code blocks, and links intact.

```ts
// src/index.ts (excerpt — full file in the companion repo)
const DOCS_ROOT = process.env.ZENSICAL_DOCS_DIR ?? "./docs";

function loadDocs(): DocEntry[] {
  const files = walk(DOCS_ROOT, DOCS_ROOT);
  return files.map((file) => {
    const raw = readFileSync(file, "utf8");
    const { data, content } = matter(raw);
    const slug = relative(DOCS_ROOT, file)
      .replace(/\.md$/, "")
      .replace(/\\/g, "/")
      .replace(/\/index$/, "");
    return {
      id: slug || "index",
      title: data.title ?? slug,
      description: data.description ?? "",
      text: stripMarkdown(content),
      path: file,
    };
  });
}

const index = new MiniSearch<DocEntry>({
  idField: "id",
  fields: ["title", "description", "text"],
  storeFields: ["title", "description", "id"],
});
index.addAll(loadDocs());
```

The tool registration is where the design choice from Part 1 actually gets enforced — not by code, but by the description the model reads before deciding whether to call the tool at all:

```ts
{
  name: "search_docs",
  description:
    "Search this documentation site for pages relevant to a query. " +
    "Returns titles, descriptions, and slugs — not full page text. " +
    "Use get_page to fetch a specific page's content once you know " +
    "which one you need. Prefer this over answering from general " +
    "knowledge for any question about this project's specific " +
    "configuration, commands, or behavior."
}
```

That last sentence isn't decoration. Without it, Claude will happily answer a docs question from training data or a guess and never call the tool at all — the tool being *available* doesn't mean the model reaches for it. The description is the only lever you have over that decision.

## Wiring it to the deploy pipeline

This is the section where the original plan and the real build diverge, and it's worth saying plainly: there isn't much wiring to do, because the server reads `docs/` directly rather than Zensical's `site/` build output. A doc edit that's merged to `main` is already "live" for the server the moment the file exists on disk — no separate index-regeneration step in CI.

The tradeoff is that the index is built once, in memory, when the server process starts. For local use through Claude Desktop or Cursor, both tools spawn a fresh subprocess per session, so a restart of the assistant picks up doc changes — in practice that's rarely more than one stale session. For a hosted version (Part 3's territory, once auth is in place), that becomes a real "restart after deploy" step in the pipeline, not an automatic one. The original framing — "no separate regenerate-the-index step for someone to forget" — was too clean. There's still a step; it's just a process restart instead of a search-index rebuild.

## Testing it against Claude Desktop and Cursor

Config for Claude Desktop (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "zensical-docs": {
      "command": "node",
      "args": ["/absolute/path/to/mcp-zensical/dist/index.js"],
      "env": { "ZENSICAL_DOCS_DIR": "/absolute/path/to/site/docs" }
    }
  }
}
```

Cursor uses the same shape in `.cursor/mcp.json` (project-level) or its global MCP settings.

`[PLACEHOLDER: real screenshots — the strongest version of this section is the pagination example from Part 1, run for real: ask with no MCP connection first (guessing from training data), then the same question with the server connected, showing it cite the actual page and slug from search_docs → get_page.]`

## What broke

- **A no-match query returning nothing.** MiniSearch's default `search()` returns an empty array on no hits — that reads to the model as "the tool ran and found nothing to say," not "try a different query." Fixed by returning an explicit message instead of an empty result.
- **Windows-style path separators leaking into slugs.** `relative()` on Windows returns backslashes; left alone, slugs came out as `migration\zensical-deep-dive` instead of `migration/zensical-deep-dive`, which then didn't match what `search_docs` told the model to pass to `get_page`. Normalized with a `.replace(/\\/g, "/")`.
- **Full-page dumps from `get_page` on longer docs.** Some of the migration series deep dives are long. Returning the entire raw Markdown file works but isn't cheap on tokens for a page someone only wanted one section of. Not fixed yet — a real next step is letting `get_page` accept an optional heading/anchor and return just that section.
- **Staleness after a doc edit**, covered above under deploy wiring — not really "broke," but the "just works" framing needed correcting.

## Try it yourself

`[PLACEHOLDER: public repo link, once one exists.]`

## What's Next

Building a working MCP server answers "can an assistant reach the docs." It doesn't answer "should every engineer's assistant be able to reach *all* of them." Part 3 is the follow-up worth having before this pattern spreads past one docs site: access control — auth, scoping what the server is allowed to serve, and what changes once the documentation isn't all meant to be public.

---

> This is part of a follow-up series to the [docs-as-code migration series](../migration/intro.md).
>
> - [Part 1: Why Your Documentation Is Invisible to AI Assistants](intro.md)
> - **Part 2: Building an MCP Server for a Docs-as-Code Site** ← you are here.
> - Part 3: Access Control for Docs-as-Code MCP Servers, coming soon.