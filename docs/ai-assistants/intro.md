---
title: "AI Assistants and Your Documentation"
description: "How MkDocs, Docusaurus, and other docs-as-code sites can expose their content to Claude, Cursor, and Copilot through an MCP server — and why that's different from just adding an llms.txt file."
tags:
  - Docs-as-Code
  - AI Assistants
  - MCP
---
# Why Your Documentation Is Invisible to AI Assistants (And What to Do About It)

An engineer on your team opens Cursor and asks it how to paginate results from your internal Orders API. Cursor answers instantly and confidently: pass `page` and `per_page` as query parameters.

That's the old pagination scheme. Your team deprecated it eight months ago in favor of cursor-based pagination — `cursor` and `limit` — specifically because `page`/`per_page` fell apart once a table passed a few million rows. The migration guide explaining the change, with a code sample and a deprecation timeline, has been sitting on your docs site since the week the change shipped. Cursor never saw it, because nothing ever pointed it there.

The engineer ships the old parameters, the request 400s in staging, and twenty minutes gets spent debugging something the documentation already answered. Multiply that across a team, and across every fast-moving internal tool with a docs site nobody thought to connect to anything.

Here's the thesis: documentation and AI assistants currently live in two separate worlds, the same way documentation and code used to before docs-as-code closed that gap. The fix follows the same logic — stop treating them as separate systems that occasionally get copied into each other, and wire them together directly.

I've spent the last few years doing exactly that on the code side: moving documentation out of Word docs and WordPress and into Git, next to the code it describes. At the same time, I've been pushing Claude and Copilot adoption on my own team. This post is about the seam between those two efforts — the one that's still wide open at most companies, including, until recently, mine.

---

## Copy-Paste Context: The Manual Problem

The default way most people give an AI assistant access to documentation right now is manual. Someone copies a doc page into a ChatGPT tab. Someone else drags a PDF export into a Claude conversation. It works, in the narrow sense that the model can now answer questions about that one page, for that one conversation.

It doesn't work at team scale. Five engineers asking about the same API end up pasting the same page five separate times, because there's no shared context — each conversation starts from zero. Copies go stale the moment the docs change, because nobody's chat history gets updated when a page is edited. And there's a quieter cost: half-current snapshots of internal documentation now live scattered across people's chat histories, outside version control, outside any review process, with no owner.

There's no feedback loop. If the docs change on Tuesday, every AI conversation that pasted in Monday's version keeps answering from Monday's version indefinitely — nothing tells it otherwise.

## Static Snapshots: The llms.txt Ceiling

The next step up is `llms.txt` — a plain-text file, usually at the root of a docs site, that gives an AI assistant a structured, token-efficient summary of your content in one shot. Instead of an engineer manually pasting pages, the assistant reads a single file that links out to (or inlines) the important ones.

It's a genuine improvement, and it's cheap: a few hours to generate and maintain, works with any assistant that can fetch a URL, and requires no server, no infrastructure, no ongoing maintenance beyond keeping the file in sync with your nav. For loading context once at the start of a session, it's a reasonable default.

But it's still a snapshot, not a source. Three limits show up quickly:

- **It goes stale the moment your docs change**, in exactly the way copy-pasted context does — just with one central file instead of scattered chat histories. Nothing regenerates it automatically unless you build that yourself.
- **It doesn't scale down.** A large doc set means a large `llms.txt`, and now the assistant is loading an entire corpus into context to answer a question about one page. That's expensive in tokens and it's the opposite of scoped retrieval.
- **It's one-shot.** The assistant reads it once and works from what it got. It can't come back five minutes later and ask a follow-up query against your current docs — there's no query interface, just a file.

`llms.txt` answers "give me an overview of your docs." It doesn't answer "what does this look like right now."

## Web Search / Training Data: The Hallucination Problem

Without a direct line to your actual documentation, an AI assistant falls back on two things: whatever's in its training data, and whatever it can find with web search. For a fast-moving internal tool — or any tool that isn't well represented on the public web — neither is grounded in what your docs actually say.

That's what happened in the pagination example above. Cursor wasn't lying. It was pattern-matching against the most common REST API convention it had seen — `page`/`per_page` shows up constantly in training data — and applying it to an API where that convention no longer holds. No web search would have caught it either, since the migration guide isn't public, and if it were, a general web search ranks it against every other API doc that still uses `page`/`per_page`.

This is the actual cost, and it's not "the AI doesn't know something." An honest "I don't know, I can't find documentation for that" is cheap — the engineer goes and checks the docs themselves, same as before AI assistants existed. A confident, specific, plausible, wrong answer is expensive, because it doesn't look like a gap. It looks like an answer. The engineer ships it, and the documentation that would have caught the mistake never entered the conversation.

## What an MCP Server Actually Solves

MCP — the Model Context Protocol — gives an AI assistant a way to query a live system at request time instead of working from whatever it already has loaded. Point one at your docs site, and the assistant stops guessing from training data and starts asking your documentation directly, the same way it might query a database or call an API.

![Diagram contrasting three static context sources — copy-paste, an llms.txt snapshot, and training data or web search — which all feed an AI assistant a stale answer, against an MCP server, which lets the assistant query the live docs site directly and stay in sync with every deploy](../img/ai-assistants/mcp-server-solves.svg)

- **Live queries, not snapshots** — the assistant asks your docs a question at request time and gets today's answer, not whatever was true when a file was last regenerated.
- **Scoped retrieval** — it pulls the specific page relevant to the question, not the entire doc set, which keeps answers grounded and keeps context windows small.
- **Stays current automatically** — the same deploy pipeline that publishes your docs updates what the MCP server serves. There's no separate "regenerate the snapshot" step to forget.
- **Zero-friction for the engineer** — they stay in Cursor, Claude Code, or Copilot Chat instead of tab-switching to search your docs site and copying an answer back.
- **Complements `llms.txt`, doesn't replace it** — `llms.txt` is still the right tool for loading a one-time overview at the start of a session. MCP is for the ongoing, query-by-query grounding after that. Worth saying explicitly, because it's easy to read this post as "pick one" when the real answer is "use both, for different moments."

The common thread with docs-as-code is direct: docs-as-code closed the gap between documentation and code by putting them in the same repo, the same review process, the same deploy. An MCP server closes the gap between documentation and the tools engineers actually work in, the same way — by putting them on the same live pipeline instead of leaving a manual step between them.

## What's Next

Part 2 is a hands-on build: an actual MCP server wired on top of the Zensical scaffold from the [deep-dive series](../migration/zensical-deep-dive.md) — the same tool this site itself runs on — tested against Claude Desktop and Cursor with real queries against the real docs on this site, screenshots included. Same approach as Parts 4 through 7 of the migration series: a running instance, not a diagram.

It's not the first attempt at this. Two existing projects are worth knowing about going in: [`docusaurus-plugin-mcp-server`](https://github.com/scalvert/docusaurus-plugin-mcp-server), which publishes a Docusaurus site as an MCP server with configurable search indexing, and the [MkDocs Search MCP server](https://www.npmjs.com/package/@serverless-dna/mkdocs-mcp), which leans on the Lunr.js search index MkDocs sites already build for their own search bar. Neither targets Zensical, which is exactly the gap Part 2 fills — and it's a fast-moving space, GitBook shipped auto-generated MCP servers for every published site in September 2025, so treat any specific tool names here as a snapshot of July 2026, not a permanent state of the art.

---

> This is a follow-up to the [docs-as-code migration series](../migration/intro.md).
>
> - **Part 1: Why Your Documentation Is Invisible to AI Assistants** ← you are here.
> - Part 2: Building an MCP Server for a Docs-as-Code Site (hands-on) — coming soon.
