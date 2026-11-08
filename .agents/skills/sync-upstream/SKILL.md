---
name: sync-upstream
description: Absorb Matt's upstream changes into this fork: report every upstream commit since the last sync, wait for your approval, then re-apply the approved changes by hand.
disable-model-invocation: true
---

# Sync Upstream

This fork follows upstream by re-applying Matt's commits as local edits: read each diff, then edit the local copy. The local copies are edited in place, so a merge would fight every deliberate divergence.

## 1. Read the record

[`.agents/upstream.md`](../../upstream.md) holds the last absorbed upstream commit and the skills this fork deleted on purpose. Both are authoritative. If the file is missing, stop and say so.

## 2. Fetch

When `git remote get-url upstream` fails, add the remote:

```
git remote add upstream https://github.com/mattpocock/skills
```

Then fetch:

```
git fetch upstream
```

A failed fetch ends the run: report the failure and its output.

## 3. Collect the commits

Take `git log <absorbed>..upstream/main`, oldest first. When it is empty, say there is nothing new and stop. A commit that touches no in-scope path stays out of the report.

In scope: paths that exist in both trees under `skills/engineering/`, `skills/productivity/`, `.agents/` and `docs/agents/`. Out of scope: everything else, including this fork's own `README.md`, `README-en.md`, `WORKFLOW.md`, `AGENTS.md` and `GLOSSARY.md`.

A path on the deleted list is out of scope. When an upstream commit touches one, the report names it as skipped, so you can see what this fork is missing.

A new skill is a directory under `skills/engineering/` or `skills/productivity/` with a `SKILL.md` that is absent locally and absent from the deleted list. New skills are in scope.

A skill directory that exists at `<absorbed>` but not at `upstream/main`, and still exists locally, was deleted upstream. List it, and change nothing.

For each in-scope path, classify it against the local copy:

- untouched locally since `<absorbed>`: the upstream change applies cleanly;
- edited locally: compare both sides, and list the overlapping hunks;
- deleted locally: skip it, and name it in the report.

The step ends when every in-scope path in the range is classified.

## 4. Write the report

Write `.agents/upstream-report-<YYYY-MM-DD>.md`, with the local date of the run, and overwrite a same-day file. Write it in Chinese; keep commit titles, paths and code exactly as they are.

```markdown
# 上游同步：`<absorbed>` 至 `upstream/main`（N 个提交）

## 提交

### `<短号>` <标题>

- `路径`：上游改动，本地未动。
- `路径`：上游改动，本地也改过，见「撞车」。
- `路径`：已跳过（本仓库删除了该 skill）。

## 撞车

### `路径`

- 上游：第 12 至 18 行，<一句说明>。
- 本地：第 9 至 14 行，<一句说明>。
- 重叠：第 12 至 14 行。

## 上游已删除

- `skills/...`：上游删掉了，本地还在。
```

Then say in one line how many commits the report covers and where it is, and stop. Nothing is edited before approval.

When the user asked only for the report, stop here.

Approval is a batch: everything in the report is approved unless you name commits or paths to skip. Wait for that reply, and do not infer it.

## 5. Apply

Compare three states per file: upstream at `<absorbed>`, upstream at `upstream/main`, and the local file.

- Untouched locally: write the upstream version.
- Edited locally, changes do not overlap: keep every local hunk, apply every upstream hunk.
- Edited locally, changes overlap: stop at the overlap, show both sides, and ask; leave the file alone until you decide.

Skills land without their `agents/` adapter files. This fork does not ship them.

A new skill lands from upstream, then gets fitted to this repo:

- add it to `README.md` and `README-en.md`, under the right User-invoked or Model-invoked list;
- absorb upstream's `ask-matt` change for it, and add the entry yourself when the upstream commit did not touch `ask-matt`;
- add it to `WORKFLOW.md` section 4.

The step ends when every approved path is written, and every overlap is either decided or listed as open.

## 6. Verify

Check the touched files:

- `README.md` and `README-en.md` list the same skills in the same order, and every link resolves.
- `ask-matt` names every new skill.
- `WORKFLOW.md` section 4 lists every skill.
- Every touched `SKILL.md` has a `name` matching its directory.
- No em-dash in touched prose.

The step ends when every line above passes on the touched files.

## 7. Record and commit

Set the absorbed commit in `.agents/upstream.md` to the `upstream/main` tip read this run.

Commit only the paths this run touched:

```
git add <paths>
git commit -m "chore: absorb upstream through <short-sha>"
```

Leave the report untracked. The user deletes it.

The step ends when the record and the commit agree.

## Refuse

- A merge or a cherry-pick, at any step.
- Any read or write under `skills/non-matt/`.
- A silent overwrite of a file that was edited locally.
- Continuing after a failed fetch.
