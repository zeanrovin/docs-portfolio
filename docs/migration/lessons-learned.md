---
title: "Docs-as-Code Migration: Lessons Learned"
description: "Closing out the series: what actually mattered after migrating 31 products to docs-as-code, and what four tool deep dives confirmed or changed about that."
tags:
  - Docs-as-Code
  - Migration
---

# Docs-as-Code Migration: Lessons Learned

This is Part 8, the last post in this series. [Part 1](intro.md) opened with a claim: that offline files, WordPress, and Google Docs all eventually break down as documentation scales, and that docs-as-code fixes the underlying problem rather than papering over it. Parts 2 and 3 walked through how to actually do that: converting content, then choosing a tool. Parts 4 through 7 went one level deeper, scaffolding MkDocs, Docusaurus, Zensical, and Markdoc from zero to see exactly what each one gives you before you've customized anything.

This post is where all of that gets reconciled against the thing that actually happened: migrating 31 products off WordPress and onto a Markdown and MkDocs workflow. Some of what I expected going in held up. Some of it didn't.

---

## What held up in practice

**The review process was the real win, not the file format.** Markdown was never the point. Pull requests were. Once documentation changes went through the same review workflow as code, quality went up for a boring reason: someone else looked at it before it shipped. That single change mattered more than any theme, plugin, or navigation feature covered in Parts 4 through 7.

**Deployment automation paid for itself almost immediately.** Not waiting on a CMS to publish, not coordinating a manual release step: CI building and deploying docs on every merge removed a whole category of "did that actually go out?" questions.

**The barrier-to-contribution drop was real, but not instant.** Engineers who already lived in Git picked up the workflow in a day. Non-technical contributors (support, PM, sometimes leadership) needed more onboarding than I budgeted for. Docs-as-code shifts the highest-friction step from "your document editor" to "your Git workflow," and that trade is worth making, but it isn't free.

**Page-to-page performance held up at real scale.** With 31 products and hundreds of pages, I expected navigation to get sluggish somewhere in there. It didn't. Because MkDocs builds the whole thing down to static files, moving between pages is instant: no CMS round-trip, no spinner. The built-in Previous/Next links in the footer turned out to matter more than I expected too; they nudge people to read documentation in the order it was written instead of dead-ending on a page and bouncing back to search.

**Search stopped being an afterthought.** Before migrating, we tried most of the search plugins available for WordPress, and none of them came close to MkDocs's built-in search. It indexes pages with an actual relevance hierarchy: a match in a heading outranks one buried three paragraphs into the body, so the top result is usually the right one, not just the first one alphabetically.

**Customization turned out to be close to unlimited.** MkDocs, Docusaurus, and Zensical all ship strong themes to start from (Markdoc's the deliberate exception: it gives you nothing). But that's a starting point, not a ceiling. Because the output is plain HTML and CSS, you can restyle almost anything, colors, typography, layout, to match your brand, without fighting a page builder's opinions about what a "block" is allowed to look like.

**It just reads as more modern.** This one's harder to quantify but was one of the first things people noticed. Material for MkDocs and Docusaurus both default to clean typography, dark mode out of the box, and a layout that's responsive by default rather than bolted on with a separate mobile theme. Compare that to the WordPress documentation themes a lot of engineering teams end up on, which often haven't been meaningfully touched since they were installed and still carry the visual fingerprint of a much older CMS era. Readers pick up on that difference even when they can't articulate why. A site that looks maintained reads as maintained, and one that looks stale invites the same assumption about the accuracy of what's written on it.

## What the tool comparisons confirmed

Writing Parts 4 through 7 as hands-on scaffolds rather than researched summaries surfaced a few things a comparison table alone wouldn't have:

- **MkDocs's bareness is the point, not a gap.** A stock `mkdocs new` project has one page and no theme opinions at all. That's exactly right for a tool meant to be a foundation for [Material for MkDocs](mkdocs-deep-dive.md) rather than a finished product.
- **Docusaurus commits you to Node/React the moment you scaffold.** That's a fair trade if your team already lives there and wants a blog and docs in one build. It's a real cost if it doesn't.
- **Zensical is the closest thing to "MkDocs with the good defaults pre-applied."** Shipping a GitHub Pages deploy workflow in the default scaffold (something neither MkDocs nor Docusaurus does) says a lot about which parts of "getting started" it considers non-optional.
- **Markdoc is the odd one out, deliberately.** It doesn't compete with the other three; it's a content layer, not a site generator. Building the parse-AST-render pipeline by hand in [Part 7](markdoc-deep-dive.md) made that distinction concrete instead of abstract.

None of this changed the recommendation from [Part 3](choosing-a-docs-as-code-tool.md). It did make me trust that recommendation more, because it was based on running each tool instead of reading about it.

## Advice if you're starting this yourself

- **Run a proof of concept before you migrate anything.** Convert five to ten representative pages in your top two candidate tools and compare the actual authoring and review experience, not the marketing page.
- **Don't lift and shift everything.** [Part 2](migration-process.md) covers this in more detail, but it's worth repeating: migration is a filter, not a move operation. Duplicate pages, stale release notes, and dead product versions don't deserve a new home.
- **Invest in the review workflow before the theme.** A plain, unstyled MkDocs site with real PR review beats a beautifully themed site where anyone can push directly to `main`.
- **Budget real time for the unglamorous parts.** Redirects, broken image paths after a folder reorganization (this series ran into exactly that), and contributor onboarding all take longer than the migration script itself.

## A follow-up worth reading

If you're building any of this today, there's a seam this series doesn't cover: what happens when an AI assistant like Claude or Cursor tries to answer a question about your docs and can't actually reach them. That's the subject of a short follow-up series, starting with [Why Your Documentation Is Invisible to AI Assistants](../ai-assistants/intro.md).

## Where the series ends and reality begins

None of the four tools in this series deserves the credit for what actually changed. Put MkDocs, Docusaurus, Zensical, and Markdoc's stock output side by side and the differences are surface-level: a nav bar here, a built-in search box there. What changed across 31 products was smaller than any of that and far less visual: someone now has to open a pull request to edit a sentence, and someone else has to approve it before it ships. That's the entire mechanism. The theme, the search index, the CSS: all of it was worth doing, and none of it was the fix.

## The series, start to finish

> - [Part 1: Why Your Documentation Is Failing Your Engineering Team](intro.md)
> - [Part 2: The Migration Process](migration-process.md)
> - [Part 3: Choosing the Right Tool for MkDocs, Docusaurus, Zensical, and Markdoc](choosing-a-docs-as-code-tool.md)
> - [Part 4: MkDocs Deep Dive](mkdocs-deep-dive.md)
> - [Part 5: Docusaurus Deep Dive](docusaurus-deep-dive.md)
> - [Part 6: Zensical Deep Dive](zensical-deep-dive.md)
> - [Part 7: Markdoc Deep Dive](markdoc-deep-dive.md)
> - **Part 8: Lessons Learned** ← you are here.
