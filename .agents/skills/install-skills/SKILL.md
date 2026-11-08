---
name: install-skills
description: Mirror this repo's skills into the global agents folder.
disable-model-invocation: true
---

# Install Skills

Mirror every skill under `skills/` into `C:\Users\echo\.agents\skills`, so that folder always equals this repo. A directory under `skills/` holding a `SKILL.md` is installable. Skills under `.agents/skills/` work only inside this repo and never leave it.

## 1. Mirror each skill

Create the destination root when it is missing. For each installable skill, make `C:\Users\echo\.agents\skills\<name>` identical to `skills/<bucket>/<name>`:

- create the directory when it is missing;
- copy every file the repo has, overwriting a different copy;
- delete every file the destination has that the repo does not.

Copy inside the skill directory only, and do not follow junctions or symlinks.

The step ends when every installable skill matches its source byte for byte; confirm it by comparing each file's hash on both sides.

## 2. Remove stale skills

Delete a top-level directory under the destination when both hold:

- it contains a `SKILL.md`;
- its name is not an installable skill.

Leave every other entry alone, including `synced` and any directory without a top-level `SKILL.md`.

The step ends when every skill directory under the destination belongs to an installable skill.

## 3. Report

Name each skill that changed, split into installed, updated, and removed, and give the unchanged count. Say when nothing changed.

There is no approval step: the mirror is deterministic. There is no commit: the destination sits outside this repo.

## Refuse

- Writing outside `C:\Users\echo\.agents\skills`.
- Deleting a directory that has no top-level `SKILL.md`.
