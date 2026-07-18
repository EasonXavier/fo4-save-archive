# Codex Project Instructions

## Purpose

This repository archives Fallout 4 save checkpoints and their narrative context. Treat save integrity and factual task-state accuracy as more important than convenience.

## Source of truth

Use only the following as evidence for a checkpoint:

1. the save file supplied by the user;
2. screenshots supplied by the user;
3. explicit statements supplied by the user;
4. existing metadata already committed for an earlier checkpoint.

Do not infer an unshown quest state from general Fallout 4 knowledge. Mark uncertain fields as `unknown` or `待确认`.

## Branch rules

- `main` contains the shared timeline before a final faction route is locked.
- Early or mid-game faction quests may remain on `main` while all major endings are still available.
- Create a `story/*` branch only after an irreversible faction split, permanent hostility, explicit lockout warning, or deliberate commitment to a faction ending.
- Standard faction branches are:
  - `story/brotherhood`
  - `story/railroad`
  - `story/institute`
  - `story/minutemen`
- Do not merge a completed faction storyline back into `main`.
- Never force-push or rewrite published checkpoint history.

Before creating or updating a checkpoint, run:

```powershell
git status --short --branch
git branch --show-current
git pull --ff-only
```

If the current branch does not match the checkpoint's story state, stop and explain the mismatch.

## Checkpoint rules

Each save must be placed in a new directory:

```text
checkpoints/YYYY-MM-DD-short-description/
```

A checkpoint should contain:

```text
save/<original filename>.fos
progress.md
manifest.json
screenshots/        # optional
environment/        # optional
```

Never overwrite, rename, edit, normalize, compress, or otherwise modify a supplied `.fos` file unless the user explicitly asks for a separate copy. Preserve the original filename.

Do not delete older save files because a newer checkpoint exists.

## `manifest.json`

Generate valid UTF-8 JSON with at least:

```json
{
  "schemaVersion": 1,
  "checkpointId": "YYYY-MM-DD-short-description",
  "title": "Human-readable title",
  "branch": "main",
  "createdAt": "ISO-8601 timestamp",
  "save": {
    "fileName": "original-save-file.fos",
    "sizeBytes": 0,
    "sha256": "lowercase SHA-256"
  },
  "game": {
    "location": "unknown",
    "playTime": "unknown",
    "inGameDate": "unknown",
    "currentCompanion": "unknown"
  },
  "restoreTested": false
}
```

Compute the hash locally. On PowerShell:

```powershell
(Get-FileHash -Algorithm SHA256 -LiteralPath '<file>').Hash.ToLowerInvariant()
```

Do not invent file size, hash, timestamps, task stages, Form IDs, or numerical game data.

## `progress.md`

Use these sections unless a section clearly does not apply:

```markdown
# Checkpoint title

## Basic information
## Current main quest
## Faction state
## Active quests
## Completed since previous checkpoint
## Companions and important NPCs
## DLC and settlements
## Mod and runtime environment
## Irreversible-state assessment
## Restore instructions
## Unknown or unverified information
```

For each faction, explicitly state:

- current known quest state;
- hostile or non-hostile;
- whether its questline remains available;
- whether an irreversible branch has been entered.

Use `unknown` when evidence is insufficient.

## Fallout 4 narrative rules

- Distinguish a quest being available, active, completed, failed, or merely absent from one screenshot.
- Do not treat helping a faction with an early quest as locking its ending.
- Explicitly flag known irreversible conditions only when they are supported by the user's current progress.
- Separate factual checkpoint metadata from route recommendations.
- Do not add spoilers that are unnecessary for identifying the checkpoint.

## Public-repository privacy

This repository is public. Before committing, inspect text files, configuration files, logs, and screenshots for:

- email addresses;
- account names not intended for publication;
- access tokens, cookies, API keys, passwords, recovery codes, or other credentials;
- private messages, unrelated desktop content, notifications, or personal documents;
- authentication headers, subscription URLs, private keys, or secrets embedded in logs and configuration files.

Absolute filesystem paths are permitted. This includes Windows usernames and local directory structures exposed as part of paths. The repository owner has explicitly accepted that disclosure risk.

Do not redact, rewrite, omit, or reject a file solely because it contains an absolute path or a Windows username inside that path.

Never commit `.env`, authentication files, Git credentials, browser session exports, private keys, or crash dumps that contain credentials or unrelated sensitive data. A crash dump containing only accepted local paths is not prohibited solely for that reason, but should still be excluded unless it is relevant to restoring the checkpoint.

## Git workflow

For a normal checkpoint on the current branch:

```powershell
git add -- <checkpoint directory>
git diff --cached --stat
git diff --cached -- progress.md manifest.json
git commit -m "checkpoint: <short description>"
git push origin HEAD
```

Binary `.fos` files will not have a readable diff. Confirm their path, size, and SHA-256 before commit.

For an irreversible faction split from `main`:

```powershell
git switch main
git pull --ff-only
git switch -c story/<faction>
git push -u origin story/<faction>
```

Do not create all faction branches in advance. Create each branch at the actual split point.

## Validation before finishing

Always report:

- current branch;
- checkpoint directory;
- save filename;
- file size;
- SHA-256;
- files created or changed;
- commit hash, if committed;
- push result;
- any uncertain task-state fields.

Do not claim a push succeeded unless `git push` completed successfully.
