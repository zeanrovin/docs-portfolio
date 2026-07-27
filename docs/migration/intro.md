---
title: "Migrating Legacy Documentation to Docs-as-Code"
description: "A practical guide to converting Word, PDF, WordPress, and Google Docs content into a maintainable docs-as-code workflow."
tags:
  - Docs-as-Code
  - Migration
---
# Why Your Documentation Is Failing Your Engineering Team (And What to Do About It)

If you've ever opened a Word document to update a product guide, only to find three different versions saved across two shared drives and someone's desktop, you already know the problem this post is about.

Documentation is one of those things that works fine when a team is small and a product is simple. But as products grow, teams scale, and engineers change, legacy documentation systems start to break in ways that are slow, frustrating, and surprisingly expensive to fix. I know this because I spent years working inside those systems before migrating 31 products off of them.

Here's what I learned about why the most common documentation formats fail, and why docs-as-code is worth the switch.

---

## Offline Files: The Corruption and Collaboration Problem

Word documents, PDFs, and locally saved files feel safe because they're tangible. You can see them, open them, email them. But in a modern engineering environment, that tangibility is actually the problem.

Offline files live on individual machines or shared drives, which means there's no single source of truth. Someone updates the API guide on their laptop. Someone else emails a revised version to the team. A third person downloads the old one from the shared drive without realizing it's outdated. By the time a new engineer joins and reads the documentation, they might be reading something three versions behind reality.

Corruption is a real and underappreciated risk too. A file saved on a drive that fails, a Word document that won't open after a software update, a PDF that loses its formatting after being passed through too many hands: none of that is an edge case. It happens regularly, and when it does, there's often no recovery path.

I lived this firsthand. Back when I was a software engineer, our way of tracking documentation changes was a table pinned to the bottom of the document itself, something like:

| Version | Date | Comments |
|---------|------|----------|
| 0.02 | [Date] | Revision 1 |
| 0.01 | [Date] | Initial Draft |

Every revision meant adding a new row and bumping the version number, and that would continue until every contributor signed off on a "final" version. It worked, in the sense that it gave you *some* record of what changed and when. But it was manual, easy to forget to update, and offered none of the guarantees real version control gives you: no diffs, no attribution beyond a name typed into a cell, and no way to see what actually changed between 0.01 and 0.02 short of reading the whole document again.

The deeper issue is that offline files weren't designed for collaboration at scale. You can track changes in Word, but you can't run a proper review process, enforce standards, or merge two people's edits with any confidence. For a team of two, it's manageable. For a team of twenty, it's chaos.

---

## WordPress: Built for Content, Not for Code

WordPress powers a huge percentage of the web, and for marketing content, blog posts, and landing pages it does a fine job. But using WordPress as a documentation platform (which more engineering teams do than you'd think) creates a specific kind of friction that compounds over time.

Speaking from experience: editing a single typo in a WordPress documentation page should take thirty seconds. In practice, you wait for the page to load, wait for the editor to initialize, make your change, wait for the preview to render, then wait for the publish to go through. If your WordPress instance is loaded with plugins, custom themes, and years of accumulated content, that process can take several minutes. For one typo.

Part of that slowdown is self-inflicted. A stock WordPress install can't do most of what a real documentation site needs: versioning, search, code syntax highlighting, table of contents, access control. So you install plugins to get there. Each one on its own seems reasonable. But after you've layered in enough plugins to get a genuinely sophisticated documentation site, the site gradually slows down loading all those artifacts on every page request, and it's even worse in the editor, where every one of those plugins is also hooking into the admin screen you're trying to write in. What started as a thirty-second edit turns into minutes of waiting on a dashboard that's straining under its own extensions.

Now multiply that by a documentation system covering dozens of products, hundreds of pages, and a team of engineers who need to update content regularly. The friction adds up fast, and people stop updating documentation because the process is too painful. Outdated documentation is usually a tooling problem, not a discipline one.

WordPress also doesn't integrate naturally with the way engineers work. There's no Git workflow, no pull request process, no way to tie a documentation change to a code change in the same commit. Documentation and code live in separate worlds, which means they inevitably drift apart.

Strip away the plugin-specific pain and a shorter list of structural gaps remains:

- **No content reuse**: you can't easily reuse a snippet or chunk of text across multiple manuals
- **Poor versioning**: tracking changes across different software releases is hard
- **No multi-format export**: it doesn't cleanly produce PDF manuals or offline help files
- **Security risk**: every extra plugin widens the attack surface

---

## Google Docs: Collaborative but Generic

Google Docs is a genuine improvement over offline files in one important way: it's cloud-based and collaborative. Multiple people can edit at once, comments are visible to everyone, and version history gives you a basic record of what changed and when.

But Google Docs is a general-purpose word processor, not a documentation system. And that distinction matters more than it seems.

The version history in Google Docs tracks changes, but it doesn't enforce a review process. Anyone with edit access can change anything at any time, with no approval required. For documentation that engineers depend on to build correct integrations, that lack of structure is a real risk.

Formatting is another limitation. Google Docs gives you basic heading styles and text formatting, but producing consistent, professional-looking documentation across a large set of pages requires constant manual effort. There's no way to enforce a template, and what looks clean in one document often looks inconsistent in another. It also lacks advanced technical features like automated cross-referencing for figures and tables, or dynamic API schema rendering. A real docs toolchain handles all of that natively.

Maintenance decay is the other quiet failure mode. A standalone Google Doc feels organized on the day you create it, but give it a year and a growing team, and it easily turns into a fragmented, outdated duplicate scattered across half a dozen Drive folders: one "final" copy, one "final_v2," one someone forked to make a quick edit and never merged back.

I'm not alone in this assessment. A [recent thread on r/technicalwriting](https://www.reddit.com/r/technicalwriting/comments/1shtt9d/utilizing_google_docs_for_documentation/) asking how to make Google Docs work for SOPs and work instructions drew some pointed responses from other technical writers. "The rudimentary aspects of Google Docs are the only aspects of Google Docs. It is a rudimentary tool," one commenter put it. Another was even more direct: "Google Docs is just MS Word with fewer features." And one writer confirmed the cross-referencing gap from firsthand experience, noting there's "no auto-numbering of tables, figures, etc., unless you use a third party extension or write an AppScript." That's exactly the kind of manual patchwork a real docs toolchain shouldn't require.

And like WordPress, Google Docs doesn't connect to your codebase. When a new API endpoint ships, there's no natural process for updating the corresponding documentation at the same time. The two stay out of sync until someone notices, which might be a new engineer on their first day, or a customer who filed a support ticket.

---

## What Docs-as-Code Actually Solves

Docs-as-code treats documentation the way engineers treat code: written in plain text files, stored in Git, reviewed through pull requests, and deployed automatically when changes are merged.

That single shift changes almost everything about how documentation gets maintained:

- **Version control is built in**: every change is tracked, attributed, and reversible
- **Review processes are enforced**: documentation changes go through the same pull request workflow as code changes
- **Deployment is automated**: no manual publishing steps, no waiting for a CMS to load
- **Documentation lives next to the code it describes**: when code changes, the documentation can change in the same commit
- **The barrier to contribution drops**: engineers who already live in Git can update documentation without learning a new tool
- **A real history replaces the manual changelog table**: `git log` and `git blame` give you exactly what changed, when, and by whom, instead of a hand-maintained version table someone has to remember to update at the bottom of a document
- **Content can be reused instead of retyped**: a shared warning, setup step, or config snippet gets written once and included wherever it's needed, instead of copy-pasted into every manual that happens to need it
- **There's no plugin stack to keep feeding**: the published site is static output, not a live application straining under a dozen extensions just to do things a documentation tool should do natively

None of this is free, and the honest tradeoff is the one I still run into on every migration: non-technical contributors don't pick this up at the pace engineers do. You can teach someone the mechanics of a pull request in an afternoon, but getting a PM or support lead comfortable enough to actually use one without hand-holding takes months, not days. Docs-as-code doesn't remove that cost. It just moves it, from "maintaining the documentation" to "onboarding the people who write it." That trade is worth making.

The biggest adjustment, by far, is Git itself. Branches, commits, and pull requests are second nature to an engineer and completely foreign to almost everyone else. The first "how do I undo this" question usually shows up within the first week. CSS and theme customization is the second: it's a real advantage for whoever owns the site, but the moment a non-technical contributor wants to change how something looks rather than what it says, they're stuck waiting on whoever can touch the config instead of just clicking a format button. And the third is the one people underestimate going in: writing in plain Markdown means giving up the instant visual feedback of a *What You See Is What You Get* editor. You're typing syntax and trusting it renders correctly, instead of seeing the finished page as you type, and for former Word and Google Docs users, losing that feedback loop is a bigger psychological hurdle than it sounds like on paper.

---

## What's Next

In the next post in this series, I'll walk through the actual migration process: how to audit your existing documentation, decide what to keep, restructure your information architecture, and get your team contributing in Git without losing anyone along the way.

## Deep Dives on Each Tool

Parts 4 through 7 of this series each scaffold a fresh project for one tool, run it locally, and walk through it screen by screen. Real running instances, not marketing screenshots, each with a public companion repo you can clone and run yourself:

- **[MkDocs Deep Dive](mkdocs-deep-dive.md)**: what a stock `mkdocs new` project gives you before you add a theme or plugins.
- **[Docusaurus Deep Dive](docusaurus-deep-dive.md)**: the `classic` template's docs, blog, and navigation, untouched.
- **[Zensical Deep Dive](zensical-deep-dive.md)**: what this site itself runs on, and what its scaffold ships with by default.
- **[Markdoc Deep Dive](markdoc-deep-dive.md)**: parsing and rendering Markdown by hand, with no framework in the way.

> This is part of a series on migrating legacy documentation to a docs-as-code workflow.
>
> - **Part 1: Why Your Documentation Is Failing Your Engineering Team** ← you are here.
> - [Part 2: The Migration Process](migration-process.md).
> - [Part 3: Choosing the Right Tool for MkDocs, Docusaurus, Zensical, and Markdoc](choosing-a-docs-as-code-tool.md).
> - [Part 4: MkDocs Deep Dive](mkdocs-deep-dive.md).
> - [Part 5: Docusaurus Deep Dive](docusaurus-deep-dive.md).
> - [Part 6: Zensical Deep Dive](zensical-deep-dive.md).
> - [Part 7: Markdoc Deep Dive](markdoc-deep-dive.md).
> - [Part 8: Lessons Learned](lessons-learned.md).