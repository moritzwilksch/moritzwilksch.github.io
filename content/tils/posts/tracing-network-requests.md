---
author: ["moritz"]
title: "Basic tracing of network requests"
date: "2026-01-17"
description: "Trace network requests without a proxy"
summary: "We can use `strace` to trace network requests without setting up a proxy."
tags: ["bash", "shell", "tracing", "devtools"]
---

## Tools trying to install stuff

The other day I tried to set up `opencode`, but it crashed on startup. After a bit of digging, I found it was making network requests that don’t work in my environment. I’m not a fan of that behavior, but I still wanted to give opencode a spin.
To debug it, we need to see exactly which requests it’s making.

### Option 1: Set up a proxy

You could use `mitmproxy` and configure `opencode` to talk to a local proxy. `mitmproxy` is great software, but it means fiddling with your environment’s network config.

### Option 2: Just use `strace`

Turns out we can just use `strace`:

```bash
strace -f -e trace=network -s 10000 -o network_trace.log opencode
```

Done. This gives you a pretty readable log, and it shows what’s going on: `opencode` tries to install extra NPM packages on startup.
