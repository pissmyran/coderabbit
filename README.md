# coderabbit

The central CodeRabbit configuration for the repos under `pissmyran`. Every repo inherits `.coderabbit.yaml` from here with `inheritance: true` in its own `.coderabbit.yaml` and adds only what applies to that repo: `path_instructions` for its own folders, its own `path_filters`, its own labels, linked repos and any repo-specific checks.

## Principles

- **Use everything free:** every feature that is free for public repos is on, including `early_access`.
- **Nothing that costs:** agent features that write commits or PRs (docstrings, unit tests, autofix, fix CI, merge conflicts, simplify, planning) are off, because they can use credits. Their instructions still live under `code_generation`.
- **Advisory, never blocking:** no automatic approvals, no "request changes", no commit status or check that can fail, and every pre-merge check is set to `warning`.
- **Little noise:** the `quiet` profile posts the most important findings inline and keeps the rest collapsed in the summary. Poems, fortunes, sequence diagrams and effort estimates are off.

## What the base sets

- **Language:** reviews, summaries, titles and instructions in English.
- **Automation:** a review of every PR to the default branch and every new push (drafts and PRs labeled `wip` are skipped; public repos with fewer than 10 stars may need `@coderabbitai review` as a manual trigger), automatic labels, automatically assigned reviewers, a PR summary, and a PR title when the title contains the placeholder `[auto-title]`.
- **Quota:** the "Recent review info" section of the summary comment at the top of the PR is updated on every review and states how many included reviews remain this hour. Comment `@coderabbitai rate limit` to see the quota without starting a review.
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
- **Review state (PR):** `review-ready`, `needs-rework` or `rewrite-recommended`. They form a mutually exclusive group, so a PR has at most one of them.
- **Issues:** `bug`, `feature`, `enhancement`, `chore`, `dependencies`, `documentation`, `security`, `accessibility`, `question`, `duplicate` and `good first issue`.
- **Set by others:** `slop` is set by slop detection and `wip` by the author. The owner sets `help wanted`, `invalid` and `wontfix`.

Repos add their own labels in their own `.coderabbit.yaml`, for example `sync-format` in the Bastion repos.

A change here takes effect in every repo at its next review.
