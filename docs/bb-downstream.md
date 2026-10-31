---
artifact-id: "aid-6bbeb68c-9e69-4e7b-93da-f88a4a99c1aa"
artifact-type: operating-procedure
title: "Maintaining BB Downstream"
description: "Keep local BB changes usable while taking official releases and contributing fixes upstream."
status: active
tags: [bb, downstream, git]
created-at: "2026-10-31T02:23:46Z"
origin:
  agent: "OpenAI Codex"
  codex-session-id: "01a18f9f-45cc-7a22-b949-604f05255d29"
Date Created: "2026-10-31"
Date Updated: "2026-10-31"
---

# Maintaining BB Downstream

The owning agent runs this procedure. Git fetches official code; the agent integrates it, resolves failures, verifies the result and records the tested source. Existing commands are sufficient initially. No automatic update schedule or additional script is configured.

## Repository and branches

- Checkout: ~/Studio/07-REPOS/flowji-ai/bb-plugins/bb-downstream.
- `origin`: https://github.com/bb-plugins/bb.git, our fork and backup for retained work.
- `upstream`: https://github.com/get-bb/bb.git, official BB.
- `main`: the selected, unchanged official release. It may intentionally lag `upstream/main`.
- `upstream/main`: the last fetched official development tip; fetching it does not select a release or update the installed app.
- `downstream`: the selected official release plus our maintained changes, including this procedure.
- Topic branches: one logical fix or feature per branch. A permanent `dev` branch is unnecessary initially.

Keep official source on `main` free of local policy and customisations. Keep published history stable. Do not reset or force-push shared branches. Use BB's worktree mechanism for work requiring isolation; leave existing worktree owners and paths intact.

## Making a change

Start an independent upstream contribution from clean `main`. Start dependent custom work from `downstream` only when it needs another local change; record that dependency. Use the project's branch naming and commit-provenance rules.

Add meaningful tests with behavioural changes. Run the relevant Turbo checks and exercise the changed behaviour. Merge the reviewed change into `downstream` once it passes. Push task-owned commits to the fork under the applicable backup policy.

Submit generally useful fixes upstream separately, without this procedure or unrelated customisations. Follow ../CONTRIBUTING.md, including contributor approval and prior maintainer alignment for features and UI changes. Local use need not wait for upstream acceptance.

Record each retained fix's purpose, dependencies, upstream issue/PR and removal condition in its existing task or commit record. An accepted PR is not proof that our selected release includes it. Once the selected official release supplies the fix, reconcile the local equivalent and retest its behaviour; do not blindly reverse a duplicate patch.

## Taking an official update

1. Check checkout ownership, dirty paths, local-only commits, worktrees and the installed version. Preserve outstanding work without automatically stashing another agent's changes.
2. Fetch the official development reference with `git fetch --no-tags upstream main`. Inspect and fetch the explicitly selected immutable `desktop-v<version>` release tag. Do not force-update moving `desktop-latest` or `desktop-nightly` tags. Fetching alone does not alter our selected branches or running app.
3. Confirm the release descends from the current `main`. Advance `main` with a fast-forward to that exact release. If ancestry or ownership is unexpected, resolve it before changing history.
4. Combine the new `main` with `downstream` on a temporary integration branch in an appropriate BB environment. Account for every retained change, resolve conflicts and check for fixes now supplied upstream. A conflict-free merge is not a behavioural test.
5. Run BB's regular typechecks and tests, build the candidate, then exercise retained changes in an isolated runtime. Compare unexplained failures with clean official source. Record actual failures and unperformed checks.
6. Merge the verified result into `downstream`, preserve the previous known-good source reference and artifact, and push the approved source. Installation is a separate step from source integration.

## Checks and local builds

Use the Node version required by ../package.json and pnpm 9.15.0. From the checkout root, install the selected lockfile when preparing a build:

```sh
pnpm install --frozen-lockfile
```

For an individual change, select the affected package and its relevant tests through Turbo. For an official update, run the regular suite and any applicable integration or smoke checks:

```sh
pnpm exec turbo run typecheck test
pnpm exec turbo run build --filter=bb-app --filter=@bb/desktop
```

For a local macOS ARM64 directory bundle, explicitly prevent publication:

```sh
pnpm --filter @bb/desktop exec node scripts/run-electron-builder.mjs --mac --dir --arm64 --publish never
```

The bundle is produced under ../apps/desktop/release/. This command does not install or launch it. Do not use the publishing `desktop:build` script for local work. Follow bb-release-process.md only for a separately authorised public release.

For isolated desktop verification, use the documented `pnpm desktop:worktree` route. Confirm the printed checkout-specific data directory and ports before exercising the build. `pnpm desktop` uses installed BB's data and single-instance lock. See debugging-and-qa.md.

## Installation and recovery

Record the source commit, official release base, checks performed and actual artifact for every installed custom build. Keep the last working artifact available. A Git tag preserves source identity, not an executable or application data.

Before first production use of a custom build, establish its app identity and update behaviour. The inherited desktop configuration uses official BB's identity and update feed; an official app update can replace a custom build. Choose the intended replacement or separate-app arrangement before installing. Back up stopped application data and establish a recovery procedure compatible with any database migrations. Reinstalling an older executable alone does not prove data can be rolled back.

Source checkout recovery and application recovery are separate operations. Recover source through preserved references or a reviewed revert. Do not reset shared history or overwrite the live data directory as a shortcut.

## Setup verification on 2026-10-31

The existing BB project `proj_abjhwjx2iv` is named BB Downstream. Source `src_3ee32mgcdt` now points to the renamed checkout. Repository identity, task records and chat/environment IDs were preserved. The former checkout was ~/Studio/07-REPOS/flowji-ai/bb-plugins/bb-upstream. Linked research worktrees and historical BB/Pi chat folders remain at their existing paths; Git backlinks were repaired.

The selected official source is `desktop-v0.48.1`, commit `e003e9510cbfd3bb6c26a28630b46ffb1c242a44`. `main` matches that commit. No retained source-code modifications, local-only commits or stashes were found in the inspected checkout and linked worktrees. This procedure is the initial downstream addition.

Installed BB reported 0.46.1 during setup. Its exact build commit was not established, and no app installation or restart was performed. Matching a version number is not proof of identical source provenance. Dependencies, a custom bundle, runtime behaviour and a production recovery artifact have not been verified by this setup operation. PTI-15 remains on hold; upstream submission and product changes are separate work.
