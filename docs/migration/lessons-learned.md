---
title: "Docs-as-Code Migration: Lessons Learned"
description: "Closing out the series — what actually mattered after migrating 31 products to docs-as-code, and what four tool deep dives confirmed or changed about that."
---

# Docs-as-Code Migration: Lessons Learned

This is Part 8, the last post in this series. [Part 1](intro.md) opened with a claim: that offline files, WordPress, and Google Docs all eventually break down as documentation scales, and that docs-as-code fixes the underlying problem rather than papering over it. Parts 2 and 3 walked through how to actually do that — converting content, then choosing a tool. Parts 4 through 7 went one level deeper, scaffolding MkDocs, Docusaurus, Zensical, and Markdoc from zero to see exactly what each one gives you before you've customized anything.

This post is where all of that gets reconciled against the thing that actually happened: migrating 31 products off WordPress and onto a Markdown and MkDocs workflow. Some of what I expected going in held up. Some of it didn't.

---

## What held up in practice

**The review process was the real win, not the file format.** Markdown was never the point — pull requests were. Once documentation changes went through the same review workflow as code, quality went up for a boring reason: someone else looked at it before it shipped. That single change mattered more than any theme, plugin, or navigation feature covered in Parts 4 through 7.

**Deployment automation paid for itself almost immediately.** Not waiting on a CMS to publish, not coordinating a manual release step — CI building and deploying docs on every merge removed a whole category of "did that actually go out?" questions.

**The barrier-to-contribution drop was real, but not instant.** Engineers who already lived in Git picked up the workflow in a day. Non-technical contributors — support, PM, sometimes leadership — needed more onboarding than I budgeted for. Docs-as-code shifts the highest-friction step from "your document editor" to "your Git workflow," and that trade is worth making, but it isn't free.

## What the tool comparisons confirmed

Writing Parts 4 through 7 as hands-on scaffolds rather than researched summaries surfaced a few things a comparison table alone wouldn't have:

- **MkDocs's bareness is the point, not a gap.** A stock `mkdocs new` project has one page and no theme opinions at all. That's exactly right for a tool meant to be a foundation for [Material for MkDocs](mkdocs-deep-dive.md) rather than a finished product.
- **Docusaurus commits you to Node/React the moment you scaffold.** That's a fair trade if your team already lives there and wants a blog and docs in one build. It's a real cost if it doesn't.
- **Zensical is the closest thing to "MkDocs with the good defaults pre-applied."** Shipping a GitHub Pages deploy workflow in the default scaffold — something neither MkDocs nor Docusaurus does — says a lot about which parts of "getting started" it considers non-optional.
- **Markdoc is the odd one out, deliberately.** It doesn't compete with the other three; it's a content layer, not a site generator. Building the parse-AST-render pipeline by hand in [Part 7](markdoc-deep-dive.md) made that distinction concrete instead of abstract.

None of this changed the recommendation from [Part 3](choosing-a-docs-as-code-tool.md). It did make me trust that recommendation more, because it was based on running each tool instead of reading about it.

## Advice if you're starting this yourself

- **Run a proof of concept before you migrate anything.** Convert five to ten representative pages in your top two candidate tools and compare the actual authoring and review experience, not the marketing page.
- **Don't lift and shift everything.** [Part 2](migration-process.md) covers this in more detail, but it's worth repeating: migration is a filter, not a move operation. Duplicate pages, stale release notes, and dead product versions don't deserve a new home.
- **Invest in the review workflow before the theme.** A plain, unstyled MkDocs site with real PR review beats a beautifully themed site where anyone can push directly to `main`.
- **Budget real time for the unglamorous parts.** Redirects, broken image paths after a folder reorganization (this series ran into exactly that), and contributor onboarding all take longer than the migration script itself.

## Where the series ends and reality begins

Every tool in this series — MkDocs, Docusaurus, Zensical, Markdoc — is a reasonable choice under the right conditions, and every one of them will disappoint you if you expect the tool to fix a process problem. The migration that actually worked wasn't the one with the best-looking site. It was the one where documentation became something the team maintained the same way it maintained code: reviewed, versioned, and owned.

## The series, start to finish

> - [Part 1: Why Your Documentation Is Failing Your Engineering Team](intro.md)
> - [Part 2: The Migration Process](migration-process.md)
> - [Part 3: Choosing the Right Tool — MkDocs vs Docusaurus vs Zensical vs Markdoc](choosing-a-docs-as-code-tool.md)
> - [Part 4: MkDocs Deep Dive](mkdocs-deep-dive.md)
> - [Part 5: Docusaurus Deep Dive](docusaurus-deep-dive.md)
> - [Part 6: Zensical Deep Dive](zensical-deep-dive.md)
> - [Part 7: Markdoc Deep Dive](markdoc-deep-dive.md)
> - **Part 8: Lessons Learned** ← you are here.
