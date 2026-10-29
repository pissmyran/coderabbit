# coderabbit

The central CodeRabbit configuration for the repos under `pissmyran`. Repositories can inherit `.coderabbit.yaml` from here with `inheritance: true` in their own `.coderabbit.yaml` and add only what applies to each repo: `path_instructions` for its own folders, its own `path_filters`, its own labels, linked repos and any repo-specific checks.

## Free OSS access and billing

This base targets eligible OSS access: open-source projects receive Team features without a paid subscription. Standard Free access provides hosted PR summaries only, not full hosted PR reviews. Public repositories below 10 stars require a manual trigger such as `@coderabbitai review`, even with automatic reviews enabled. See [plans and limits](https://docs.coderabbit.ai/management/plans).

Zero cost also requires account settings: keep usage-based billing and separately billed products, including CodeRabbit Agent and Security Scan, disabled. Confirm OSS eligibility and account billing settings before relying on this base; neither was verified by this repository update. YAML cannot grant entitlements or enforce a spending limit, and `enable_free_tier: true` is not a billing safeguard. Existing review security scanners remain enabled.

## Principles

- **Free OSS review features:** use eligible included review features; `early_access` is off.
- **Nothing that costs:** agent features that write commits or PRs (docstrings, unit tests, autofix, fix CI, merge conflicts, simplify, planning) are off, because they can use credits. Their instructions still live under `code_generation`.
- **Advisory, never blocking:** no automatic approvals, no "request changes", no commit status or check that can fail, and enabled pre-merge checks use `warning`; docstring coverage is off.
- **Concise output:** the `chill` profile balances defect reporting. Findings name the defect, consequence and fix. The walkthrough stays expanded, though individual service-generated sections may still be collapsed. Repetitive file summaries, related-issue/PR lists, reviewer suggestions, poems, fortunes, sequence diagrams and effort estimates are off. The description summary uses at most three short bullets.

## What the base sets

- **Language:** reviews, summaries, titles and instructions in English.
- **Automation:** request reviews for PRs to the default branch, including drafts, and incremental reviews on new pushes; the `wip` label pauses automatic reviews. Entitlements, manual-trigger requirements and rolling limits still apply. Labels and chat replies are automatic; reviewer suggestions and assignment are off.
- **Titles and squash commits:** `[auto-title]` explicitly triggers title generation. Preserve valid existing titles. Generated titles follow Conventional Commits, with the whole title under 72 characters. Under this repository's governance, the PR title becomes the squash commit title. The hosted PR schema has no commit-message automation setting; existing commits are not rewritten.
- **Quota:** run details, review details and progress are hidden; the review status message stays enabled. Use `@coderabbitai rate limit` on demand for current availability. Remaining reviews and the next available review depend on live rolling-window usage; this configuration cannot supply an ETA.
- **Review scope:** both code and documentation are reviewed. Only generated output is skipped; lock files stay in scope so the dependency scanners can read them.
- **Path instructions:** general rules for every file, documentation, agent instructions, `.github/`, JavaScript and TypeScript, Python, Rust, Swift, Kotlin and C#, shell scripts, Docker, Cloudflare configuration and the documentation site.
- **Runtime rules** (Cloudflare Workers, the SSH clients, the contact tools) live in each repo's own `.coderabbit.yaml`, not here.
- **Pre-merge checks (advisory):** the title follows Conventional Commits, the description, linked issues, "Docs in the same PR" and "No personal values".
- **Tools:** security scanners (gitleaks, trufflehog, osv-scanner, trivy, opengrep, semgrep, presidio, skillspector, checkov, zizmor, ast-grep) and linters for the repos' languages (actionlint, shellcheck, hadolint, dotenv-linter, ruff, clippy, swiftlint, detekt). Linters that overlap Prettier or only add style noise are off.
- **Knowledge:** CodeRabbit reads the repo's `AGENTS.md` as review criteria, searches the web and learns from earlier reviews.
- **Issues:** new issues are enriched with context and labeled automatically.

## Linked repos

CodeRabbit can read linked repos during a review and warn about changes that break something in another repo. The links belong in each repo's own `.coderabbit.yaml` under `knowledge_base.linked_repositories`, not here. This is how the repos are to be linked:

- **Bastion:** `bastion`, `bastion_apple`, `bastion_linux`, `bastion_windows` and `bastion_android` are linked to each other. They are one client on different platforms and share the sync format, which `bastion_apple` owns.
- **The services:** `dumpen`, `jobb`, `klarsprak` and `politiker` are linked to each other. They share the pattern for placeholders, `tools/render.mjs`, Docker, `import-settings.sh` and the documentation site, so a change to the shared tooling must be made the same way in all four.

## Labels

The labels follow GitHub's default set plus a few extras, and every label exists in every repo.

- **Type of change (PR):** `fix`, `feature`, `enhancement`, `chore`, `documentation`, `dependencies`, `security`, `accessibility` and `duplicate`. A PR can have several.
- **Review state (PR):** `review-ready`, `needs-rework` or `rewrite-recommended`. They form a mutually exclusive group, so a PR has at most one. Rework and rewrite assessments require concrete defect evidence. `review-ready` means the latest review found no actionable defects and no thread awaits the author; it is not approval or proof that checks passed. These labels do not change mergeability.
- **Issues:** `bug`, `feature`, `enhancement`, `chore`, `dependencies`, `documentation`, `security`, `accessibility`, `question`, `duplicate` and `good first issue`.
- **Set by others:** the built-in slop classifier sets `slop`; issue-label instructions cannot customize it. Known AI authorship is not a defect, and slop classification stays separate from defect findings. The author sets `wip`. The owner sets `help wanted`, `invalid` and `wontfix`.

Repos add their own labels in their own `.coderabbit.yaml`, for example `sync-format` in the Bastion repos.

A change here supplies the base at the next review for repositories using it. This repository sets `inheritance: false`; consumers can retain `inheritance: true` and local overrides. Objects merge deeply, scalars use the child value, and arrays keep child entries first, then unique parent entries. Matching `path`, `label`, `name`, `id` or `key` entries replace the base entry. See [inheritance rules](https://docs.coderabbit.ai/configuration/configuration-inheritance).

Other repositories' overrides and account-level global overrides were not inspected or changed. Use `@coderabbitai configuration` in a consuming PR to inspect the resolved configuration; local and global overrides can change the effective behavior.
