# AGENTS.md — djbclark/VESTI fork only

**Do not include this file in any PR, branch, or push to `abraxas914/VESTI`.**
It is operator context for AI sessions on this fork. Upstream gitignores `/AGENTS.md`; this copy is force-tracked on `origin` only.

Last updated: 2026-08-22.

## Remotes

| remote | repo | role |
|---|---|---|
| `origin` | https://github.com/djbclark/VESTI | this fork; OK to push |
| `upstream` | https://github.com/abraxas914/VESTI | canonical project; **never push** |

Upstream PRs: `gh pr create --repo abraxas914/VESTI --head djbclark:<branch> --base main`.

Fork issues (enabled on this fork): use `--repo djbclark/VESTI`. Do not file routine tracking issues on upstream.

## Language

Upstream commits, PR titles/bodies, issue text, and changelog/README edits that land on `abraxas914/VESTI` are **Chinese**, matching existing PRs (`feat: …`, `## 背景` / `## 主要变更` / `## 验证`). Do not assume maintainers read English.

This file, and discussion with the fork owner, may be English.

## How to keep this file off upstream

1. Branch every DPR from `upstream/main`, not from fork `main`. Example: `git fetch upstream && git worktree add /tmp/vesti-pr -b feat/foo upstream/main`.
2. Before opening a PR: `git diff --name-only upstream/main...HEAD` must **not** list `AGENTS.md`.
3. Never `git push upstream`. Prefer a disabled push URL: `git remote set-url --push upstream DISABLE`.
4. Do not squash/merge fork `main` into an upstream PR branch.

## DPR split (do not mix)

| # | status | tracker | what |
|---|---|---|---|
| 1 | open | https://github.com/abraxas914/VESTI/pull/109 | Toolbar owl contrast |
| 2 | blocked | https://github.com/djbclark/VESTI/issues/1 | BYOK region picker (China / International / US) |
| 3 | blocked | https://github.com/djbclark/VESTI/issues/2 | English README/CHANGELOG as `documents/*.en.md` sidecars |

DPR 2 and 3 wait on Alibaba Cloud international account verification (~4 days from 2026-08-22). Do not open those PRs until the owner can actually call `dashscope-intl` / `dashscope-us`.

## DPR 1 — toolbar icon (PR 109)

Chrome **does not honor** `action.theme_icons` (Firefox-only). A cream `default_icon` fixed the dark toolbar and vanished on default light appearance.

Shipped approach: `manifest.action.default_icon` is a **dark owl + light halo** (`frontend/assets/icon-theme-contrast-{16,32,48,64,128}.png`). Plasmo shallow-merges `manifest.action`, so the package.json `action` block must include `default_icon` or Plasmo’s default is dropped.

Asset paths are `../assets/…` relative to `frontend/.plasmo/`. Prod build rewrites them to flat hashed names (`icon-theme-contrast-16.<hash>.png`). Top-level Plasmo `icons` (CWS / `chrome://extensions` card) stay the original dark `icon.png`.

Regenerate: `bash frontend/scripts/generate-toolbar-icons.sh` (ImageMagick `magick`). Do not reintroduce `theme_icons` or the old light/dark-only glyphs.

Worktree used for the PR: `/tmp/vesti-icon-pr` on `fix/toolbar-icon-contrast` (from `upstream/main`). Checkout at `/Users/djbclark/src/VESTI` is fork `main` and must **not** be the PR branch.

## DPR 2 — BYOK regions (uncommitted on fork `main`)

Default stays **China**, matching upstream. Additive picker in Settings → Model Access.

| region | DashScope | ModelScope | default models |
|---|---|---|---|
| `china` (default) | `dashscope.aliyuncs.com` | `api-inference.modelscope.cn` | `qwen-plus` / `qwen-turbo` |
| `international` | `dashscope-intl.aliyuncs.com` (Singapore) | `api-inference.modelscope.ai` | same ids |
| `us` | `dashscope-us.aliyuncs.com` (Virginia) | no US host → intl ModelScope | `qwen-plus-us` / `qwen-flash-us` |

Keys are not cross-region. Existing installs without `byokRegion` stay on China. Demo proxy is unchanged. Implementation already in the dirty worktree: `frontend/src/lib/services/llmConfig.ts`, `SettingsPage.tsx`, en/zh/ja/ko i18n, `llmConfig.test.ts`. When PRing, also add `host_permissions` for `dashscope-intl`, `dashscope-us`, and `modelscope.ai` — **not** on the icon PR.

Do not ship `pnpm-workspace.yaml` `allowBuilds` or unrelated `pnpm-lock.yaml` churn in any upstream PR.

## DPR 3 — English docs (uncommitted on fork `main`)

Canonical roots are Chinese: `README.md`, `CHANGELOG.md`. English lives in `documents/README.en.md` and `documents/CHANGELOG.en.md`. One `[English](documents/….en.md)` line at the top of each Chinese root. Never replace the Chinese root with English if the PR is for upstream.

## Local extension

Plasmo Chrome MV3. Build: `pnpm -C frontend build` → load unpacked `frontend/build/chrome-mv3-prod`. Reload at `chrome://extensions` after icon changes; Chrome caches toolbar bitmaps.

**Do not take control of the desktop GUI without asking the owner first.**

## Working tree (as of 2026-08-22)

- `/Users/djbclark/src/VESTI` — fork `main`, dirty with DPR 2+3 plus contrast icons copied for local load. Ahead of `origin/main` by upstream rc.9 merge commits. Do not dump this tree into an upstream PR.
- `/tmp/vesti-icon-pr` — icon PR worktree, branch `fix/toolbar-icon-contrast`.
- Unpacked load is from the main checkout’s `frontend/build/chrome-mv3-prod`.
