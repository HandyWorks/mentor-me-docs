# From Mac Studio to Mentor Me — My Local-First AI Dev Stack

*The story of the machine, the tools, and the dead ends — written the way it
actually happened.*

## The machine

In late October 2026 I bought a **Mac Studio** — Apple **M5 Max**, **128 GB of
unified memory** — with one job in mind: run serious AI inference locally and
build iOS apps natively, on the same box.

The 128 GB was the decision that mattered. It's what lets a 27B-class model sit
in memory next to Xcode, a booted simulator, and a live PostgreSQL database
without anything paging to disk.

## Xcode

First order of business after setup: **Xcode 27.2** with the command-line tools
and the iOS simulator runtime. Nothing exotic — the standard Apple toolchain,
just fresh.

## The experiment: Ollama, then LM Studio

Before I settled on anything, I ran the two most common local model servers and
did the usual local-inference troubleshooting with both — model size,
quantization, context windows, and how each fit the way I actually wanted to
work:

- **Ollama** — I pulled the Qwen 2.5 family (including `qwen2.5-coder`) for
  general use, an embedding model, and eventually **`qwen3.8:27b`** itself.
- **LM Studio** — I went bigger for a while, pulling **Qwen2.5-VL-72B at
  Q4_K_M** to see what a large vision-language model could do on this
  hardware.

Both worked as inference servers. Both gave me a chat window. And that was the
limit: a model that can *explain* a failed build is not a model that can *fix*
it. I wanted something that would run `xcodebuild`, read the error, edit the
file, rerun the tests, and commit the result.

## What I settled on: Hermes + Qwen 3.8 27B

- **Hermes** — an open agent runtime that lives on the machine. It has a
  terminal, file and browser access, its own memory and skills that persist
  across sessions, and it drives real tools instead of just generating text.
- **Qwen 3.8 27B** at Q4_K_M precision, served locally through **llama.cpp**
  over an OpenAI-compatible endpoint on `localhost`.

The result is a stack where **everything stays on the Studio** — the code, the
conversations, the model weights, the inference. No API keys, no data leaving
the machine, and no per-token bill for an agent that works for hours at a
time.

## The stack at a glance

| Layer | Choice |
| --- | --- |
| Hardware | Mac Studio — Apple M5 Max, 128 GB unified memory |
| OS | macOS 27 |
| IDE / build | Xcode 27.2 (SwiftUI, simulator, unit tests) |
| Agent | Hermes (persistent memory, skills, tool access) |
| Model | Qwen 3.8 27B (Q4_K_M), local llama.cpp server |
| Also tried | Ollama (qwen2.5, qwen2.5-coder, qwen3.8:27b) · LM Studio (Qwen2.5-VL-72B) |
| What it builds | [Mentor Me](ios-app/project-overview.md) — SwiftUI app, FastAPI + PostgreSQL backend, GitHub Actions CI |

## Where it's taking the project

This setup is what Mentor Me is being built with: the agent handles the day-to-day —
builds, tests, the git/CI flow, and keeping the documentation (this repo) in
step with the code. The next chapters are the Android port and the website,
documented here as they happen.
