---
author: ["moritz"]
title: "Larger LLMs can be cheaper for agentic tasks"
date: "2026-02-03"
description: "Larger models can outperform cheaper ones on both cost and latency for tool-heavy agentic tasks."
summary: "In a tool-using fact-checking task, larger models converged faster and matched or beat cheaper models on total cost."
tags: ["llms", "agents", "tool-use", "benchmarks", "cost", "latency"]
---

I'm a big fan of the Google Gemini Flash models. They're smart enough for most use cases, and the latency and cost are hard to beat. Still, a recent experiment caught me off guard: For agentic tasks, where models must plan, call tools, and iterate, the larger, smarter model can end up both faster and cheaper than the supposedly cost-optimized option.

# The Experiment

Consider the task of fact-checking claims in a slide against an authoritative source document. In this experiment, the slide contains quantitative statements about a publicly traded company, and the source is the company's [10-K filing](https://s2.q4cdn.com/470004039/files/doc_financials/2025/ar/_10-K-2025-As-Filed.pdf).

{{< figure src="/images/blog/larger-llms-cheaper-for-agentic-tasks/slide-aapl.jpeg" alt="Slide with claims about a company's financials" class="img-80" >}}

# Methodology

## Using a Code Execution Agent

We could design a system specifically for this task and provide it with tools optimally tailored for reading and searching PDF documents. For this exercise, I think it's more interesting to keep the system as generic as possible, so I'm only giving the model four generic tools:

1. `bash`: Execute a bash command
2. `read_file`: Read text or images
3. `write_file`: Create files on disk
4. `grep`: Basically ripgrep

# Results

## A typical trajectory

Turns out, this works! A typical trajectory for the agent solving the task looks like this:

1. Reads the image
2. Writes a small Python script that extracts the text layer from our source PDF
3. Repeatedly `grep`s through the text for relevant terms
4. Finds the income statement
5. Correctly concludes that the FY2025 revenue number is correct, the YoY growth rate is wrong, and the FY2025 net income is incorrect because it has been confused with the FY2024 value.

## Efficiency and Token Usage

Now, for the question that led me to run this experiment: How efficient are different models at solving this task? I tested `gemini-2.5-flash`, `gemini-3-flash-preview`, `gemini-2.5-pro`, `gemini-3-pro-preview`, `gpt-5.2-pre-feb` (before OpenAI announced 40% inference speed improvement), `gpt-5.2`, and `kimi-k2.5` in their default configurations, with 10 rollouts per model. I averaged token usage, number of turns until completion, number of tool calls per turn, and cost.

| **Model**                | **Tokens (In / Cached / Out)** | **Avg. Turns** | **Tool Calls / Turn** | **Avg. Duration** | **Avg. Cost** |
| ------------------------ | ------------------------------ | -------------- | --------------------- | ----------------- | ------------- |
| `gemini-2.5-flash`       | 91,591 / 9,791 / 4,467         | 10.9           | 0.89                  | 54.7s             | $0.039        |
| `gemini-3-flash-preview` | 99,182 / 5,823 / 6,028         | 8.7            | 1.26                  | 86.1s             | $0.068        |
| `gemini-2.5-pro`         | 46,146 / 0 / 3,294             | 10.7           | 1.21                  | 69.2s             | $0.091        |
| `gemini-3-pro-preview`   | 42,666 / 0 / 5,179             | 6.4            | 1.27                  | 94.5s             | $0.147        |
| `gpt-5.2-pre-feb`        | 30,331 / 18,265 / 801          | 5.6            | 1.53                  | 55.5s             | $0.068        |
| `gpt-5.2`                | 24,082 / 16,345 / 784          | 5.1            | 1.56                  | 62.7s             | $0.056        |
| `kimi-k2.5`              | 71,053 / 39,117 / 2,149        | 8.9            | 1.16                  | 132.8s            | $0.073        |

## Per-token pricing can be misleading

A few interesting observations:

1. The current cheapest per-token model, `gemini-3-flash-preview`, does not deliver the lowest total cost. Despite good per-token pricing, it ends up matching `gpt-5.2-pre-feb` at $0.068 and costing more than the February update to `gpt-5.2` at $0.056. The gap comes from higher input token usage, more turns, and lower tool calling efficiency per turn.
2. OpenAI's `gpt-5.2` variants are the most efficient overall. They finish in the fewest turns, make the most tool calls per turn, and manage to cache most input tokens, which results in both lower total cost and faster end-to-end latency than Gemini Flash.
3. Gemini Pro models use fewer input tokens, but lack prompt caching and have longer runtimes, which makes them the most expensive options in this benchmark.
4. `kimi-k2.5` caching works quite well despite using OpenRouter which proxies requests to multiple providers. Still, it has by far the longest execution time.
5. Google's implicit prompt caching is severely broken. It sometimes triggers at random, then fails completely during a full rollout. This happens in both AI Studio and the VertexAI API and seems to be widespread yet unfixed.

# Conclusion

For agentic, tool-heavy workloads, per-token pricing is a poor proxy for cost or latency. Models that plan better, batch tool calls, and converge faster can outperform 'cheaper' models on both dimensions.
