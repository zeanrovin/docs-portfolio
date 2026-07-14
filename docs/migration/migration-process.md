---
title: "Migrating Legacy Documentation to Docs-as-Code"
description: "A practical guide to converting Word, PDF, WordPress, and Google Docs content into a maintainable docs-as-code workflow."
---

# Migrating Legacy Documentation to Docs-as-Code

In the [first post](#), I introduced docs-as-code: managing documentation with the same practices used for software—plain-text files, version control, review, automated checks, and publishing.

This post is about the first practical step: moving the documentation you already have into that workflow.

The good news is that a migration does not have to be a giant, one-time project. Start with a small collection of high-value pages, establish a conversion and review process, then repeat it across the rest of the documentation.

## What you need to begin

There are two essentials:

1. **Documentation source files**, normally written in Markdown and stored in a Git repository.
2. **A publishing path**, usually a documentation site generator and a hosting service.

Markdown (`.md`) is the common format for docs-as-code. It is readable in a plain-text editor, works well with version control, and is supported by most documentation tools.

For publishing, common choices include:

- [Docusaurus](https://docusaurus.io/), a React-based documentation-site generator.
- [MkDocs](https://www.mkdocs.org/), a simple static-site generator designed for project documentation.
- [Zensical](https://zensical.org/), a modern documentation-site generator with a familiar MkDocs-style workflow.
- [Markdoc](https://markdoc.dev/), a Markdown-based authoring framework for structured, component-rich content. Markdoc is not a complete publishing platform on its own; it is typically used within a web application or documentation stack.

The site generator is important, but it does not need to be your first decision. A migration can begin with well-organized Markdown in a repository. Publishing, navigation, theming, and automation can evolve once the content is in a maintainable format.

## Markdown files: the easy starting point

If your existing documentation is already in Markdown, the migration is mostly editorial:

- Move files into the documentation repository.
- Organize them by product, audience, or task.
- Add the pages to the site navigation.
- Check headings, links, images, tables, and code blocks in the rendered site.

Markdown is not completely universal. A page written for GitHub may use features that render differently in Docusaurus, MkDocs, or another platform. Treat the first build as a review step, not merely a publish step.

## Word documents (`.docx`)

Word documents usually convert well when they use consistent built-in styles for headings, paragraphs, lists, and tables. The conversion produces a useful first draft; it is not a substitute for an editorial review.

Useful tools include:

- **[Pandoc](https://pandoc.org/)** — a mature command-line conversion tool. It supports DOCX input and Markdown output, making it a strong option for structured Word documents.
- **[MarkItDown](https://github.com/microsoft/markitdown)** — a converter from Microsoft that supports Word, PDF, PowerPoint, Excel, HTML, and other formats.
- **[Docling](https://docling-project.github.io/docling/usage/supported_formats/)** — a document-conversion toolkit that can process DOCX and PDF files and export Markdown.

After conversion, review the output for:

- Heading levels and page structure
- Tables, lists, and numbered procedures
- Images, captions, and alt text
- Internal and external links
- Notes, warnings, and callouts
- Comments, tracked changes, and embedded files
- Formatting that Markdown cannot represent

The quality of the source document matters. A Word file that uses real heading styles will convert more reliably than one where headings are simply large, bold text.

## PDF files

PDF is usually the most difficult source format. It was designed to preserve a visual layout, not to preserve the underlying document structure. Headings, reading order, tables, image descriptions, and multi-column layouts can be lost or misinterpreted during conversion.

If the original Word document, Google Doc, or source file is available, use that instead of the PDF.

When PDF is the only source, tools such as [MarkItDown](https://github.com/microsoft/markitdown) and [Docling](https://docling-project.github.io/docling/) can create a Markdown draft. Scanned PDFs also require optical character recognition (OCR) before the text can be extracted reliably.

Plan a full review of every converted PDF page, particularly when it contains:

- Tables or multi-column content
- Diagrams, screenshots, and figures
- Forms and complex layouts
- Footnotes, headers, or page numbers
- Scanned text

Use PDF conversion to recover the content and structure it for the web. Do not expect it to preserve the original page design.

## WordPress pages

WordPress content can be exported to Markdown instead of copied page by page. The [Markdown Exporter for WordPress](https://github.com/robertdevore/markdown-exporter-for-wordpress) plugin is one useful option: it exports posts, pages, and custom post types as Markdown.

An export should be followed by a content audit. Check:

- Image files and image URLs
- Links pointing to the old WordPress site
- Shortcodes, embeds, and custom blocks
- Categories, tags, and other metadata
- Page titles and URL slugs
- Redirects from the old site to the new documentation site

Redirect planning is essential for public documentation. A successful content export is only half the migration; visitors and search engines still need to reach the new pages.

## Google Docs

Google Docs can now export directly to Markdown. In Google Docs, select **File → Download → Markdown (.md)**. Google added native Markdown import and export to make it easier to move documents into Markdown-based workflows.

For a standard document, direct Markdown export is the best first option: it avoids an unnecessary DOCX conversion step.

Use a different path when the output needs more control:

- Try the **[Docs to Markdown](https://workspace.google.com/marketplace/app/docs_to_markdown/700168918607)** add-on if you need an alternative direct conversion method.
- Download as **Microsoft Word (`.docx`)** and use Pandoc, MarkItDown, or Docling when the direct Markdown export does not produce usable results.

Regardless of the route, check Google Docs-specific content carefully. Comments, suggested edits, smart chips, drawings, embedded files, and complex tables may need to be handled manually.

## Other documentation sources worth planning for

Documentation rarely lives in one place. Alongside Word, PDF, WordPress, and Google Docs, migrations commonly include the following sources.

### Confluence

Confluence is a frequent home for internal documentation. Export content through Confluence's built-in export options or its API, then convert the exported HTML or XML into Markdown. Review macros, attachments, permissions, page hierarchies, and links—these platform-specific features rarely transfer cleanly.

### Notion

Notion can export pages as Markdown and CSV. This is a useful starting point, but databases, linked pages, embeds, and synced blocks need special attention. Decide whether a Notion database should become a Markdown table, a generated reference page, a CSV file, or something else entirely.

### Wikis and knowledge bases

MediaWiki, GitLab Wiki, Azure DevOps Wiki, and similar tools may export Markdown, HTML, or XML. Start by identifying the native export format, then preserve the page hierarchy and repair internal links as the content moves into the new site.

### Existing HTML documentation sites

Legacy help centers, product websites, and intranets are often the real source of truth. Export or crawl the HTML, convert the content to Markdown, and rebuild the information architecture for the new site. Capture the old URLs early so that you can create redirects when the migration goes live.

### SharePoint and OneDrive

SharePoint and OneDrive are usually document libraries rather than a single documentation format. Begin with an inventory: identify which files are Word documents, PDFs, spreadsheets, slide decks, or duplicates. Then send each type through the appropriate conversion path instead of applying one tool to everything.

### Spreadsheets

Spreadsheets are common sources for configuration references, compatibility matrices, and operational runbooks. Convert explanatory prose into Markdown. Keep genuinely tabular data as CSV or generate the table as part of the documentation build, especially if it changes frequently.

### Presentation decks

Slides can contain valuable product knowledge, but they are rarely usable documentation without rewriting. Extract the narrative, diagrams, and key decisions, then turn them into task-oriented guides, concepts, or reference pages. Do not treat a slide-by-slide conversion as a finished documentation experience.

### Tickets, support cases, and email threads

Jira, ServiceNow, Zendesk, GitHub Issues, and support conversations often contain the answers users need. They should be curated rather than bulk-exported. Look for recurring questions and durable solutions, then rewrite them as clear, maintained documentation with an owner.

### API descriptions and code comments

OpenAPI or Swagger definitions, Postman collections, and code comments can generate useful API reference documentation. They do not replace human-written onboarding guides, tutorials, explanations, or troubleshooting content. Treat generated reference and authored documentation as complementary parts of the same site.

## Build a repeatable migration process

Avoid starting with the largest, messiest set of documents. Begin with a small representative sample—a getting-started guide, a tutorial, a reference page, and a page with a table or image.

Then repeat this process:

1. Export or convert the original source to Markdown.
2. Store the Markdown files and related assets in the documentation repository.
3. Clean up the converted content and fix links.
4. Build the documentation site locally.
5. Review the rendered pages with subject-matter experts.
6. Publish the approved pages.
7. Record the conversion and cleanup rules that worked.

Those rules become your migration playbook. They might specify how to name files, where to store images, how to handle tables, how to write redirects, and which source formats need manual review.

The objective is not a perfect one-click conversion. It is a maintainable source of truth: documentation that can be reviewed, versioned, tested, and published continuously.

## Choose a migration path

Not every documentation migration should follow the same path. Before converting files, decide what should happen to the content you already have.

- **Lift and shift:** Convert the existing documentation with minimal rewriting. This is the fastest option, but it may bring old structure and outdated language into the new site.

- **Curate and rewrite:** Move only the content that is current, useful, and accurate. Rewrite it for the new documentation structure, then archive or retire the rest.

- **Hybrid migration:** Convert everything to preserve it, but prioritize a smaller set of high-value pages for editing and improvement. This is often the most practical approach for large documentation sets.

- **Start net-new:** Build the new docs-as-code site around current product documentation, while keeping legacy content available separately for reference during the transition.

Migration is also a chance to decide what not to keep. Duplicate pages, obsolete release notes, unsupported product versions, and one-off support conversations do not need to become permanent documentation. Archive them when they have historical value; retire them when they do not.

The goal is not to move every file. It is to create a documentation set that people can find, trust, and maintain.

## Next up

Once the content is in Markdown, the next step is to organize it into a documentation repository and turn it into a site that people can navigate and trust.
