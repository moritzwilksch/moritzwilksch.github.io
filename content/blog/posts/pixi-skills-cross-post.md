---
author: ["moritz", "pavel"]
title: "Pixi Skills (cross-post)"
date: "2026-02-13"
description: "A short pointer to Pavel's post about Pixi skills."
summary: "Cross-post: read Pavel's write-up on Pixi skills."
tags: ["pixi", "skills", "cross-post"]
---

Anthropic's skills standardize how you augment your coding agent's context with workflow- or tool-specific instructions. They're just simple Markdown files, so they're easy to read and write. However, managing them at scale gets messy fast: no versioning, Markdown sprawl in git, and real supply chain risk.

My friend Pavel and I built some tooling that packages skills as conda packages and manages them with pixi. That gives you lockfiles, version pinning, and the same supply chain security you already rely on, basically for free. Check out the blog post:

[Pixi Skills](https://pavel.pink/blog/pixi-skills)
