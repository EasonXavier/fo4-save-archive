# Codex Project Instructions

## Purpose

This repository archives Fallout 4 save checkpoints and their narrative context. Save integrity, branch correctness, and factual quest-state accuracy take priority over convenience.

When F4SE is used, treat the matching `.fos` and `.f4se` files as one logical save pair.

## Source of truth

Use only:

1. save files supplied by the user;
2. screenshots supplied by the user;
3. explicit user statements;
4. metadata already committed for an earlier checkpoint.

Do not infer unshown quest states from general Fallout 4 knowledge.

Only fields inside the repository's tracking scope may be marked `unknown` or `待确认`. Deliberately untracked fields must be omitted rather than reported as missing information.

## Branch rules

- `main` contains the shared timeline before a final faction route is locked.
- Early and mid-game faction quests may remain on `main` while all major endings remain available.
- Create `story/*` only after an irreversible faction split, permanent hostility, explicit lockout warning, or deliberate commitment to a faction ending.
- Standard branches:
  - `story/brotherhood`
  - `story/railroad`
  - `story/institute`
  - `story/minutemen`
- Never merge a completed faction storyline back into `main`.
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

```text
incoming-saves/
```

Actual save payloads in this directory are ignored by Git.

- Read from `incoming-saves`, but do not commit files directly from it.
- Copy files into a new checkpoint directory.
- Do not move, rename, modify, overwrite, or delete staging originals.
- Do not remove incoming files after a successful checkpoint unless the user explicitly requests it.

## Checkpoint structure

```text
checkpoints/YYYY-MM-DD-short-description/
  save/<original basename>.fos
  save/<original basename>.f4se
  progress.md
  manifest.json
  screenshots/                    # optional
```

## Save-pair requirements

- `.fos` is always required.
- `.f4se` is the F4SE co-save.
- Both files must have exactly the same basename before the extension.
- If a matching `.f4se` exists, archive it with the `.fos`.
- Never pair a `.fos` with a non-matching `.f4se`.
- Never generate an empty or replacement `.f4se`.
- Never overwrite, rename, edit, normalize, compress, or otherwise modify either supplied file.
- Preserve both original filenames.
- Do not delete older saves because a newer checkpoint exists.

If F4SE is known to be in use but the exact matching `.f4se` is missing:

1. stop before commit;
2. report the missing co-save;
3. do not substitute another file;
4. wait for explicit authorization to archive `.fos` alone;
5. only after authorization, record `f4seCoSave.status` as `missing`.

Use `not-applicable` only when the user explicitly confirms F4SE did not apply. Use `unknown` only when the co-save applicability cannot be determined.

## Git LFS requirements

Both save extensions must be managed by Git LFS:

```text
*.fos
*.FOS
*.f4se
*.F4SE
```

Before commit:

```powershell
git check-attr filter -- '<checkpoint .fos path>' '<checkpoint .f4se path>'
git lfs status
```

Each present binary save file must report `filter: lfs`. Otherwise stop before commit.

## `manifest.json`

Use valid UTF-8 JSON with schema version 2 or later:

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
  }
}
```

Allowed `f4seCoSave.status` values:

- `present`
- `missing`
- `not-applicable`
- `unknown`

When status is `present`, `fileName`, `sizeBytes`, and `sha256` are required.

Compute the size and SHA-256 separately for every present `.fos` and `.f4se`:

```powershell
(Get-FileHash -Algorithm SHA256 -LiteralPath '<file>').Hash.ToLowerInvariant()
```

Do not invent file sizes, hashes, timestamps, quest status, faction state, Form IDs, or other numerical game data.

## Tracking scope

Record by default:

- current location, companion, in-game date, and play time;
- current main and faction quest names with coarse progress descriptions;
- whether each major faction is hostile, whether its route remains available, and whether an irreversible branch has begun;
- important changes since the previous checkpoint;
- important companion, NPC, DLC, settlement, and key-choice state supplied by the user;
- save-pair integrity;
- relevant console commands supplied by the user;
- narrative restoration warnings.

Do **not** request, generate, or list as `unknown` by default:

- exact Pip-Boy objective wording;
- quest stage numbers, Form IDs, or internal stage identifiers;
- Fallout 4 runtime version;
- exact F4SE version;
- mod lists, plugin lists, or load order;
- save restore-test results.

F4SE use must still be recorded as a boolean/context fact because the `.f4se` co-save is archived. Its precise version is outside scope.

When the user says other quests, settlements, NPCs, or technical details will not be supplemented, add one concise statement such as `Other quest states were not updated for this checkpoint.` Do not expand this into many unknown fields.

## `progress.md`

Use these sections unless clearly inapplicable:

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
## Irreversible-state assessment
## Restore instructions
## Unknown or unverified information
```

The `Save-pair integrity` section records:

- `.fos` filename;
- `.f4se` filename or status;
- whether basenames match;
- whether both hashes were verified;
- Git LFS status.

Do not add a restore-tested field or request a load test.

For each major faction, state when supported:

- current known quest status;
- hostile or non-hostile;
- whether the route remains available;
- whether an irreversible branch has begun.

## Narrative rules

- Distinguish available, active, completed, failed, and not-recorded quests.
- Absence from one screenshot does not prove completion or failure.
- Helping a faction early does not lock its ending.
- Flag irreversible conditions only when supported by current evidence.
- Record coarse progress; exact objective text and internal stage numbers are outside scope.
- Separate checkpoint facts from route recommendations.
- Do not add unnecessary spoilers.

## DLC and environment policy

DLC and Creation installation state may be recorded from user statements or screenshots.

Do not maintain a default inventory of:

- game runtime versions;
- F4SE versions;
- installed mods;
- plugins;
- load order.

Technical files may only be added when the user explicitly asks for them for a specific checkpoint.

## Public-repository privacy

This repository is public. Inspect text, configuration files, logs, and screenshots for:

- email addresses;
- account names not intended for publication;
- access tokens, cookies, API keys, passwords, recovery codes, and credentials;
- private messages, unrelated desktop content, notifications, and personal documents;
- authentication headers, subscription URLs, private keys, and secrets.

Absolute filesystem paths are permitted, including Windows usernames and local directory structures. Do not redact or reject a file solely for that reason.

Never commit `.env`, authentication files, Git credentials, browser session exports, private keys, or unrelated sensitive dumps.

## Git workflow

```powershell
git add -- <checkpoint directory>
git diff --cached --stat
git diff --cached -- <checkpoint progress.md> <checkpoint manifest.json>
git lfs status
git commit -m "checkpoint: <short description>"
git push origin HEAD
```

Binary `.fos` and `.f4se` files have no readable diff. Confirm paths, matching basenames, sizes, hashes, and Git LFS status before commit.

For an irreversible faction split:

```powershell
git switch main
git pull --ff-only
git switch -c story/<faction>
git push -u origin story/<faction>
```

Do not create faction branches in advance.

## Restore workflow

1. validate `.fos` SHA-256;
2. validate `.f4se` SHA-256 when present;
3. verify matching basenames;
4. back up destination files;
5. copy both files into the Fallout 4 saves directory;
6. confirm required DLC, Creation content, and relevant mods are available using the user's current environment;
7. do not silently restore `.fos` without its required `.f4se`.

The repository does not track exact runtime, F4SE, mod, plugin, or load-order versions by default, and does not record restoration testing.

## Validation before finishing

Always report:

- current branch;
- checkpoint directory;
- `.fos` filename, size, and SHA-256;
- `.f4se` filename, size, SHA-256, or explicit status;
- whether basenames match;
- Git LFS status;
- files created or changed;
- commit hash, if committed;
- push result;
- only unresolved fields that are inside the defined tracking scope.

Do not claim a push succeeded unless `git push` completed successfully.
