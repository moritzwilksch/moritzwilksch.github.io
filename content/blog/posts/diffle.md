---
author: ["moritz", "pavelzw"]
title: "diffle: a diff viewer for human code review"
date: "2026-10-07"
description: "Launching diffle, a local diff viewer built for reviewing agent-generated code."
summary: "diffle is a local, browser-based diff viewer with Vim-style navigation, language server context, and comments you can hand back to your coding agent."
tags: ["diffle", "code-review", "agents", "devtools", "git"]
---

_My friend [Pavel](https://pavel.pink) and I built diffle together, so "we" in this post means the two of us._

Coding agents have gotten good at producing a lot of code. In our experience (September 2026), their design choices and the code's long-term maintainability don't always meet the mark. We think developers should still be in the driver's seat when writing high-stakes software. With coding agents, that can mean reviewing the generated interfaces and how components work together before merging a diff.

That changes where we spend our time. We used to spend most of it in IDEs we'd spent countless hours customizing to make writing code feel smooth. Today, we generate most of our code through agent interfaces such as Claude Code or Codex. These work well for _generating_ code, but most don't offer a good interface for _reviewing_ it.

Claude and Codex recently added features for viewing and commenting on diffs. That's better than reviewing code in a chat window.

<!-- TODO: screenshots of Claude and Codex diff viewers -->

But reviewing code also takes context and an interface that makes it easy to move through the changes. IDEs have long been good at this. We just don't want to launch a full IDE every time we want to review a diff.

That's why we built diffle, a diff viewer designed around human code review.

## Why another diff viewer?

"Wait, doesn't this already exist?" Yes, there are plenty of diff viewers. Some popular options include:

**Terminal interfaces (TUIs)**

[hunk](https://github.com/modem-dev/hunk), [tuicr](https://github.com/agavra/tuicr), [lumen](https://github.com/jnsahaj/lumen), [diffnav](https://github.com/dlvhdr/diffnav), [revdiff](https://github.com/umputun/revdiff)

**Web viewers**

[difit](https://github.com/yoshiko-pg/difit), [codiff](https://github.com/nkzw-tech/codiff), [critique](https://github.com/remorses/critique), [diffx](https://github.com/wong2/diffx)

**Git clients**

[GitHub Desktop](https://github.com/desktop/desktop), [GitButler](https://github.com/gitbutlerapp/gitbutler), [Sublime Merge](https://www.sublimemerge.com/), [GitKraken](https://gitkraken.com/git-client), github.com PR view (if it isn't down again)

We look for three things in a diff tool:

1. An interface built for reviewing code, much as IDEs are built for writing and debugging it.
2. Context that helps us understand and judge changes, such as type information from a language server and navigation between commits or revisions.
3. Comments tied to specific parts of the code, so we can give a coding agent feedback and iterate together.

Of these tools, [difit](https://github.com/yoshiko-pg/difit) comes closest to what we need. It's lightweight, runs locally, and checks a lot of boxes. We also like TUIs for their keyboard-focused workflow, but a modern web browser provides more flexibility to design an interface around human code review.
But difit gives reviewers limited context, much like GitHub's pull request view.
Then we came across [diffshub.com](https://diffshub.com) by [The Pierre Computer Company](https://pierre.computer/) and thought to ourselves: What if our local diff viewer felt just as snappy and polished?

## Enter: `diffle`

We built our first local diff viewer with [Diffs](https://diffs.com/), the open-source components from [The Pierre Computer Company](https://pierre.computer/). We used it for our own work from day one and quickly added the features we wanted, starting with Vim-like keyboard navigation and LSP support.

Run `diffle main` in a repository and your browser opens on the changes your branch introduced. You can also review uncommitted work, a single commit, or a GitHub pull request. For a branch with several commits, you can step through them individually and return to the full comparison.

Once the diff is open, you can move between lines, hunks, and files with Vim-style shortcuts, filter the file tree, and mark files as viewed.

But reading the changed lines is only part of a review. To judge a new function call, we often need to see what it calls and who else uses it. diffle uses language servers installed on your machine to bring that context into the diff. Hover a symbol for its signature and documentation, jump to its definition, or find its references. You can peek at each reference with the surrounding code before deciding where to go next.

<!-- TODO: LSP demo -->

When you find something to change, leave a comment on a line, a block, or the whole file. Press `yy` to copy all your comments as one Markdown prompt, with the file paths, line ranges, and quoted code. Paste it into your coding agent, and it gets each comment with the code it refers to. No more "pls fix this one nullable arg in the run_something method in the domain layer".

<!-- TODO: demo of commenting and copying the prompt -->

You can try it in any Git repository:

```sh
npx @moritzwilksch/diffle main...HEAD
# or:
pixi exec diffle main...HEAD
```

The code is on [GitHub](https://github.com/moritzwilksch/diffle), with more demos and installation options at [diffle.app](https://diffle.app).
