# IMPROVEMENTS Structure

The `docs/IMPROVEMENTS.md` file is the tracker for all feature ideas, bugs, and optimization plans that arise. Each item follows a lifecycle and is marked with an ID.

## Lifecycle

```
Recorded → Verified → Issue → Implemented → Archived
```

- `recorded` — logged while the idea/finding is discussed, contains `Problem` and `Possible Fix`.
- `verified` — deep review confirms it is valid; a GitHub Issue is created and its number is recorded, `Actual Fix` is filled. If invalid, the item is marked `rejected` and not continued.
- `implemented` — the linked Issue is merged; `Actual Implemented` and `Changes` are filled, the `Issue` column keeps recording the Issue number (not the PR).
- `archived` — moved to `docs/archived/IMPROVEMENT_YYYY-MM-DD.md` only on explicit USER instruction, never automatically.

## ID Scheme

Item IDs are in the format `<LABEL_CODE>-<NNN>` built from default GitHub labels (not custom labels). Numbers count per label code:

| GitHub Label | Code |
|--------------|------|
| `bug` | BUG |
| `documentation` | DOC |
| `enhancement` | ENH |
| `duplicate` | DUP |
| `good first issue` | GFI |
| `help wanted` | HW |
| `invalid` | INV |
| `question` | QST |
| `wontfix` | WFX |

## Status Legend

| Status | Meaning |
|--------|---------|
| `recorded` | Logged, not yet deeply reviewed |
| `verified` | Deep review confirmed valid, Issue created |
| `rejected` | Deep review found it invalid — skipped |
| `implemented` | Linked Issue is closed |

## Field Guide

| Field | When filled | Content |
|-------|-------------|---------|
| `Problem` | `recorded` | The finding described as application behavior when possible; for non-application items (CI, settings, tooling), describe the impact instead. |
| `Possible Fix` | `recorded` | The initial fix plan — written while still `recorded`, so it is not guaranteed to work. |
| `Actual Fix` | `verified` | The final fix plan confirmed during deep review. |
| `Actual Implemented` | `implemented` | What was actually changed during implementation. |
| `Changes` | `implemented` | The behavior changes that result after `implemented`. |
| `Issue` | `verified` | The GitHub Issue number (`#N`); recorded once the issue is opened. |

## Item Template

Each item is written following this skeleton:

```markdown
### <ID> — <Title>
- **Status:** `recorded` | `verified` | `rejected` | `implemented`
- **Issue:** <#NN> | `—`
- **Recorded:** YYYY-MM-DD
- **Implemented:** YYYY-MM-DD | `—`
- **Problem:** ...
- **Possible Fix:** ...
- **Actual Fix:** ...
- **Actual Implemented:** ...
- **Changes:** ...
```