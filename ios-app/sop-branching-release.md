# SOP — Branching & Release Flow

**Purpose:** Keep every branch predictable and every merge gated by CI.
**Scope:** Branch model, day-to-day flow, promotion to `main`.

## Branches

| Branch | Role |
| --- | --- |
| `main` | Always shippable. Contains only promoted, verified work. |
| `dev` | Integration branch. Target for day-to-day pull requests. |
| `feature/<name>` | Short-lived branch for one feature or fix, cut from `dev`. |

## Day-to-day flow

1. Cut a feature branch from `dev`:
   ```sh
   git checkout dev && git pull
   git checkout -b feature/<name>
   ```
2. Commit and push the branch; open a **pull request against `dev`**.
3. CI runs on the PR (simulator build + unit tests) — see *SOP — CI & Testing*.
4. Merge once CI is green.
5. Delete the feature branch.

## Promotion to main

1. Confirm `dev` is stable (CI green on `dev` itself).
2. Merge `dev` → `main` via pull request. CI runs on `main` too.
3. `main` is the source of any release artifact.

## Rules

- **No direct commits to `main`.**
- Every change lands through a reviewed PR with green CI.
- Feature branches are short-lived — one concern per branch.
- Never force-push `main` or `dev`.

## Verification

- `git log main..dev --oneline` shows only integrated work pending promotion.
- The latest commit on `main` has a green CI run.
