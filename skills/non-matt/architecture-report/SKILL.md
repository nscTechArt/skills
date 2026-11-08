---
name: architecture-report
description: Render the candidates from an architecture review in the conversation as a visual HTML report.
disable-model-invocation: true
---

# Architecture Report

Turn the candidates already listed in the conversation (typically from `/improve-codebase-architecture`) into one self-contained HTML page with a before/after diagram per candidate.

## Process

### 1. Take the candidates

Use the most recent candidate list in the conversation. If there is none, ask the user to paste one.

The candidates' conclusions (problem, solution, strength, order, top recommendation) are given; render them as they stand. For each diagram, read the files under the candidate's **Files** so the structure you draw is the real one.

### 2. Write the report

Read [HTML-REPORT.md](HTML-REPORT.md) before writing, then write the page to the OS temp directory so nothing lands in the repo. Resolve the temp dir from `$TMPDIR`, falling back to `/tmp` (or `%TEMP%` on Windows), and write to `<tmpdir>/architecture-report-<timestamp>.html` so each run gets a fresh file.

### 3. Open it

Open the file for the user (`xdg-open <path>` on Linux, `open <path>` on macOS, `start <path>` on Windows) and tell them the absolute path.

The report is complete when every candidate has a card in the same order with the same strength, the top recommendation matches the conversation, every Before node is a module found in the candidate's Files, and every After node comes from its Solution.
