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

Now, for the question that led me to run this experiment: How efficient are different models at solving this task? I tested `gemini-3-flash-preview`, `gemini-3-pro-preview`, and `gpt-5.2` in their default configurations, with 10 rollouts per model. I averaged token usage, number of turns until completion, number of tool calls per turn, and cost.

| **Model**                | **Tokens (In / Cached / Out)** | **Avg. Turns** | **Tool Calls / Turn** | **Avg. Duration** | **Avg. Cost** |
| ------------------------ | ------------------------------ | -------------- | --------------------- | ----------------- | ------------- |
| `gemini-3-flash-preview` | 99,182 / 5,823 / 6,028         | 8.7            | 1.26                  | 86.1s             | $0.068        |
| `gemini-3-pro-preview`   | 42,666 / 0 / 5,179             | 6.4            | 1.27                  | 94.5s             | $0.147        |
| `gpt-5.2`                | 30,331 / 18,265 / 801          | 5.6            | 1.53                  | 55.5s             | $0.068        |

## Per-token pricing can be misleading

A few interesting observations:

1. The model with the lowest per-token price, `gemini-3-flash-preview`, ends up costing us exactly the same as `gpt-5.2` for this task. That is counterintuitive if you look only at pricing ($0.5/$3 vs. $1.75/$14 per million input/output tokens). The smaller model struggles to issue multiple tool calls in a single turn, requires more turns to finish, and consumes more than 3x the input tokens compared to the smarter, more efficient model.
2. OpenAI's `gpt-5.2` makes more tool calls while using fewer output tokens, which puts it on par with Flash in cost and even faster in end-to-end latency.
3. Google's implicit prompt caching is severely broken. It sometimes triggers at random, then fails completely during a full rollout. This happens in both AI Studio and the VertexAI API and seems to be widespread yet unfixed.

# Conclusion

For agentic, tool-heavy workloads, per-token pricing is a poor proxy for cost or latency. Models that plan better, batch tool calls, and converge faster can outperform 'cheaper' models on both dimensions.
