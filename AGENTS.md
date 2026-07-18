# Codex Project Instructions

## Purpose

This repository archives Fallout 4 save checkpoints and their narrative context. Treat save integrity and factual task-state accuracy as more important than convenience.

When F4SE is used, treat the matching `.fos` and `.f4se` files as one logical save pair.

## Source of truth

Use only the following as evidence for a checkpoint:

1. the save files supplied by the user;
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
git lfs install
git lfs env
```

If the current branch does not match the checkpoint's story state, stop and explain the mismatch.

## Incoming staging directory

The local staging directory is:

```text
incoming-saves/
```

Actual save payloads in this directory are ignored by Git.

- Read files from `incoming-saves`, but do not commit them directly from that directory.
- Copy files into a new checkpoint directory.
- Do not move, rename, modify, overwrite, or delete the staging originals.
- Do not remove an incoming file after a successful checkpoint unless the user explicitly requests it.

## Checkpoint rules

Each save must be placed in a new directory:

```text
checkpoints/YYYY-MM-DD-short-description/
```

A checkpoint should contain:

```text
save/<original basename>.fos
save/<original basename>.f4se   # required when the matching co-save exists
progress.md
manifest.json
screenshots/                    # optional
environment/                    # optional
```

### Save-pair requirements

- `.fos` is the primary Fallout 4 save and is always required.
- `.f4se` is the F4SE co-save used by F4SE plugins for additional serialized state.
- The two files must have exactly the same basename before the extension.
- If a matching `.f4se` exists, archive it with the `.fos`.
- Never pair a `.fos` with a similarly named but non-matching `.f4se`.
- Never generate an empty or replacement `.f4se`.
- Never overwrite, rename, edit, normalize, compress, or otherwise modify either supplied file.
- Preserve both original filenames.
- Do not delete older save files because a newer checkpoint exists.

If the user is known to use F4SE but the exact matching `.f4se` is missing:

1. stop before commit;
2. report the missing co-save;
3. do not substitute another file;
4. wait for explicit authorization to archive the `.fos` alone;
5. if authorization is given, record `f4seCoSave.status` as `missing`.

If it is explicitly confirmed that F4SE was not used for the save, record `f4seCoSave.status` as `not-applicable`.

If there is insufficient evidence, use `unknown`.

## Git LFS requirements

Both save extensions must be managed by Git LFS through `.gitattributes`:

```text
*.fos
*.FOS
*.f4se
*.F4SE
```

Before commit, verify the attributes for the actual checkpoint files:

```powershell
git check-attr filter -- '<checkpoint .fos path>' '<checkpoint .f4se path>'
git lfs status
```

The `filter` result must be `lfs` for each present binary save file. If not, stop before commit and report the problem.

## `manifest.json`

Generate valid UTF-8 JSON using schema version 2 or later, with at least:

```json
{
  "schemaVersion": 2,
  "checkpointId": "YYYY-MM-DD-short-description",
  "title": "Human-readable title",
  "branch": "main",
  "createdAt": "ISO-8601 timestamp",
  "save": {
    "fos": {
      "fileName": "original-save-file.fos",
      "sizeBytes": 0,
      "sha256": "lowercase SHA-256"
    },
    "f4seCoSave": {
      "status": "present",
      "fileName": "original-save-file.f4se",
      "sizeBytes": 0,
      "sha256": "lowercase SHA-256"
    }
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

Allowed `f4seCoSave.status` values:

- `present`
- `missing`
- `not-applicable`
- `unknown`

When status is `present`, `fileName`, `sizeBytes`, and `sha256` are required.

Compute hashes locally. On PowerShell:

```powershell
(Get-FileHash -Algorithm SHA256 -LiteralPath '<file>').Hash.ToLowerInvariant()
```

Compute and verify the file size and SHA-256 separately for every present `.fos` and `.f4se` file.

Do not invent file size, hash, timestamps, task stages, Form IDs, or numerical game data.

## `progress.md`

Use these sections unless a section clearly does not apply:

```markdown
# Checkpoint title

## Basic information
## Save-pair integrity
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

The `Save-pair integrity` section should state:

- `.fos` filename;
- `.f4se` filename or status;
- whether basenames match;
- whether both hashes were verified;
- whether the save was loaded and tested after archival.

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
git diff --cached -- <checkpoint progress.md> <checkpoint manifest.json>
git lfs status
git commit -m "checkpoint: <short description>"
git push origin HEAD
```

Binary `.fos` and `.f4se` files will not have readable diffs. Confirm their paths, matching basenames, sizes, SHA-256 values, and Git LFS status before commit.

For an irreversible faction split from `main`:

```powershell
git switch main
git pull --ff-only
git switch -c story/<faction>
git push -u origin story/<faction>
```

Do not create all faction branches in advance. Create each branch at the actual split point.

## Restore workflow

When restoring a checkpoint:

1. validate the `.fos` SHA-256;
2. validate the `.f4se` SHA-256 when status is `present`;
3. verify the two basenames match;
4. back up any existing destination files;
5. copy both files into the Fallout 4 saves directory;
6. restore the recorded Fallout 4 runtime, F4SE, DLC, Creation, plugin order, and relevant mods;
7. do not silently restore a `.fos` without its required `.f4se` co-save.

## Validation before finishing

Always report:

- current branch;
- checkpoint directory;
- `.fos` filename, size, and SHA-256;
- `.f4se` filename, size, SHA-256, or explicit status;
- whether the basenames match;
- Git LFS status for each present save file;
- files created or changed;
- commit hash, if committed;
- push result;
- any uncertain task-state fields.

Do not claim a push succeeded unless `git push` completed successfully.
