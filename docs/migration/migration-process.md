---
title: "Migrating Legacy Documentation to Docs-as-Code"
description: "A practical guide to converting Word, PDF, WordPress, and Google Docs content into a maintainable docs-as-code workflow."
tags:
  - Docs-as-Code
  - Migration
---

# Migrating Legacy Documentation to Docs-as-Code

[Part 1](intro.md) made the case for docs-as-code: plain-text files, version control, review, and automated publishing, instead of whatever fragile system your documentation currently lives in. This post is where that stops being a pitch and turns into a checklist: how to actually move the documentation you already have into that workflow.

The biggest mistake I've seen (and made) is treating a migration as one giant, all-or-nothing cutover. It isn't, and it shouldn't be. Pick a small, high-value slice of documentation, work out the conversion and review process on that slice, then repeat it on the rest. The process you land on in week one is the same process you'll run dozens of times on everything after it.

## What you need before you start

Two things, and only two:

1. **Source files in Markdown, in Git.** Markdown is the format almost every docs-as-code tool expects: plain text, diffable, readable without a renderer.
2. **Something to publish it with.** A static site generator and somewhere to host the output.

You don't need to lock in the generator on day one. [MkDocs](https://www.mkdocs.org/), [Docusaurus](https://docusaurus.io/), [Zensical](https://zensical.org/), and [Markdoc](https://markdoc.dev/) are the four worth knowing ([Part 3](choosing-a-docs-as-code-tool.md) goes deep on picking between them), but well-organized Markdown in a repo is useful before you've decided anything about theming or navigation. Content first, presentation later.

## If your docs are already Markdown

This is the easy case, and it's mostly editorial rather than technical:

- Move the files into the docs repo.
- Organize by product, audience, or task, whatever your team will actually navigate by.
- Wire the pages into the site nav.
- Build it once and actually look at the rendered output: headings, links, images, tables, code blocks.

That last step matters more than it looks like it should. Markdown written for GitHub doesn't always render identically somewhere else: a table syntax quirk here, an admonition block there. Treat the first build as a review pass, not a formality.

## Word documents

Word files convert cleanly when the author used real heading styles: actual `Heading 1` / `Heading 2`, not 18-point bold text that merely looks like a heading. Either way, the conversion is a first draft, not a finished page.

Three tools do the heavy lifting:

- **[Pandoc](https://pandoc.org/)**: the old reliable. Command-line, handles DOCX-to-Markdown cleanly for anything with disciplined styles.
- **[MarkItDown](https://github.com/microsoft/markitdown)**: Microsoft's own converter, covers Word, PDF, PowerPoint, Excel, and HTML.
- **[Docling](https://docling-project.github.io/docling/usage/supported_formats/)**: handles DOCX and PDF, exports to Markdown.

After conversion, check: heading levels, tables and numbered steps, images and alt text, internal and external links, callouts, and anything a reviewer left as a tracked change or unresolved comment. A document built on real heading styles sails through this review. One that faked its formatting with bold, bigger text needs line-by-line cleanup instead.

## PDFs

PDF is the format most likely to fight you, because it was built to preserve how a page looks, not what it means. Reading order, table structure, and multi-column layouts are the first casualties.

If the original Word doc or Google Doc still exists, use that instead. Always. Reach for PDF conversion only when the PDF is genuinely the last copy standing. [MarkItDown](https://github.com/microsoft/markitdown) and [Docling](https://docling-project.github.io/docling/) both produce a usable first draft; scanned PDFs need OCR before there's any text to extract at all.

Whatever comes out, plan to review every page by hand, especially anything with tables, screenshots, multi-column layout, or footnotes. PDF conversion recovers content; it was never going to preserve the original design, so don't grade the tool on that.

## WordPress

Export, don't copy-paste page by page. The [Markdown Exporter for WordPress](https://github.com/robertdevore/markdown-exporter-for-wordpress) plugin handles posts, pages, and custom post types in one pass.

Every export needs a follow-up audit: image URLs, links pointing back at the old site, shortcodes and embeds, categories and tags, and slugs. The one people skip and regret: redirects from the old URLs to the new ones. A clean content export with no redirect plan just means you migrated the content and broke every bookmark and search result pointing at it.

## Google Docs

Google Docs exports straight to Markdown now: **File → Download → Markdown (.md)**. For a normal document, that's the whole migration. No DOCX detour needed.

Reach for something else only when the direct export falls short: the **[Docs to Markdown](https://workspace.google.com/marketplace/app/docs_to_markdown/700168918607)** add-on as an alternate converter, or a DOCX download run through Pandoc, MarkItDown, or Docling when Google's own export mangles something. Either way, check comments, suggested edits, smart chips, drawings, and complex tables by hand. None of that survives an automated export cleanly.

## Everywhere else documentation hides

Confluence, Notion, wikis, HTML help centers, SharePoint, spreadsheets, slide decks, support tickets, API specs: pick whichever match your migration's actual sprawl. Some notes on each, from having moved all of them at one point or another:

- **Confluence** exports through its own UI or API as HTML or XML; convert from there. Macros, attachments, and page hierarchies are the parts that don't survive the trip unattended.
- **Notion** exports Markdown and CSV, but databases and linked pages need a real decision behind them: does this become a Markdown table, a generated reference page, or just a CSV nobody templates?
- **Wikis** (MediaWiki, GitLab, Azure DevOps) export in whatever format they natively support. Start there, then fix internal links, which break more often than not.
- **Legacy HTML sites** are sometimes the actual source of truth hiding behind a prettier CMS. Crawl it, convert it, and capture the old URLs before you tear it down. You'll need them for redirects.
- **SharePoint and OneDrive** are libraries, not a format. Inventory what's actually in there: Word, PDF, spreadsheets, duplicates. Route each type on its own, instead of sending everything through one converter.
- **Spreadsheets** hold reference tables and compatibility matrices. Convert the prose; keep genuinely tabular data as CSV, or generate it at build time if it changes often.
- **Slide decks** rarely convert into anything usable directly. Extract the narrative and rebuild it as a real page instead of dumping slide text into Markdown.
- **Tickets and support threads** shouldn't be bulk-exported at all. Mine them for recurring questions, then write those up properly, with an owner.
- **API specs and code comments** generate solid reference docs but never replace a human-written getting-started guide. Generated and authored content are two different jobs, not competitors.

## Turning this into a repeatable process

Don't start with your biggest, messiest pile of documents. Start with four or five representative pages (a getting-started guide, a tutorial, a reference page, something with a table or an image) and run the whole loop once:

1. Convert the source to Markdown.
2. Commit the Markdown and its assets to the repo.
3. Clean up conversion artifacts and fix links.
4. Build the site locally.
5. Get a subject-matter expert to actually read the rendered pages.
6. Publish.
7. Write down what you just learned.

That seventh step is the one worth protecting. The rules you extract from your first five pages become the playbook for the next five hundred: how to name files, where images live, how tables get handled, which source formats always need manual review. Skip it, and you'll relearn the same lessons on every batch that follows.

## Deciding what actually moves

Not every migration should move everything. Before you convert a single file, decide which of these you're actually running:

- **Lift and shift**: convert what exists, rewrite nothing. Fastest option, and it drags old structure and outdated language along with it.
- **Curate and rewrite**: move only what's current and accurate, and rewrite it for the new site. Slower, cleaner.
- **Hybrid**: convert everything so nothing's lost, but prioritize editing for the pages that actually matter. At real scale, this is usually the only option that works. You can't hand-edit hundreds of pages before launch, but you also can't afford to quietly lose any of them.
- **Start net-new**: build fresh around current product docs, and keep the legacy set around separately during the transition.

Migration is also the best excuse you'll get to decide what shouldn't survive. Duplicate pages, release notes for versions nobody runs anymore, and one-off support threads don't need a permanent home in the new docs. Archive what has historical value. Retire the rest.

## What's Next

[Part 3](choosing-a-docs-as-code-tool.md) covers the decision this post skipped past: which tool to actually publish on, and how to stop that comparison from turning into its own multi-week project.

---

> This is part of a series on migrating legacy documentation to a docs-as-code workflow.
>
> - [Part 1: Why Your Documentation Is Failing Your Engineering Team](intro.md).
> - **Part 2: The Migration Process** ← you are here.
> - [Part 3: Choosing the Right Tool for MkDocs, Docusaurus, Zensical, and Markdoc](choosing-a-docs-as-code-tool.md).
> - [Part 4: MkDocs Deep Dive](mkdocs-deep-dive.md).
> - [Part 5: Docusaurus Deep Dive](docusaurus-deep-dive.md).
> - [Part 6: Zensical Deep Dive](zensical-deep-dive.md).
> - [Part 7: Markdoc Deep Dive](markdoc-deep-dive.md).
> - [Part 8: Lessons Learned](lessons-learned.md).
