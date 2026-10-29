# CLAUDE.md – coderabbit

Guidance for Claude Code in `pissmyran/coderabbit`. The repo holds the central CodeRabbit configuration that every other repo inherits.

The shared rules for all repos live in [STYRNING.md](https://github.com/pissmyran/.github/blob/main/STYRNING.md) in `pissmyran/.github`. The core rules below are a verbatim copy. Compare the version in the marker with the source at the start of every session (`curl -sS https://raw.githubusercontent.com/pissmyran/.github/main/STYRNING.md`); if the copy is older, update it first in a separate PR (`docs: sync core rules from .github`).

## Core rules

<!-- kärnregler:start version=2026-10-28 källa=https://github.com/pissmyran/.github/blob/main/STYRNING.md -->
1. **Inga personliga värden.** Repot är publikt. Skriv aldrig in riktiga id:n, domäner, e-postadresser, namn, nycklar, betallänkar eller kontouppgifter i kod, konfiguration, tester, dokumentation eller commit-meddelanden. Använd platshållare och `example.com`-värden.
2. **Inga hemligheter.** Tokens, lösenord, sessioner och nycklar hamnar aldrig i Git, loggar, issues eller publik dokumentation. Hemligheter läggs in utanför repot (Cloudflare, GitHub-miljöer, `wrangler secret put`).
3. **Gren och pull request.** Varje session börjar från senaste `main` (`git fetch origin main`) på en egen gren. `main` ändras bara genom en PR som squash-mergas. Ingen pushar direkt till `main`. En sessions arbete hålls i en PR.
4. **Testa lokalt före varje push.** Det finns inga testkontroller i CI. Repots egna tester och kontroller (avsnittet Kommandon i repots `CLAUDE.md`) är den enda testgrinden. Pusha aldrig med fallerande tester, och skriv tester för det du ändrar.
5. **Rör aldrig workflows.** `.github/workflows/`, `.github/actions/` och `CODEOWNERS` ändras inte av Claude, inte ens för att laga ett fel. Ingen kontroll stoppar det längre, men regeln gäller fullt ut. Behövs en ändring: beskriv den i ett issue och låt ägaren göra den. Bara Renovate och Dependabot får uppdatera dem.
6. **Granskning.** CodeRabbit, Sourcery och Codex granskar, och CodeQL (GitHubs standardinställning) skannar. Varje granskningstråd måste lösas innan merge. Svara i tråden med vad du ändrade, eller med skälet om fyndet är brus.
7. **Auto-merge sist.** Slå på auto-merge först när granskningsbotarna har granskat den senaste commiten (CodeRabbit hoppar över `docs/` och `*.md`; för sådana ändringar räcker Sourcery och Codex), alla trådar är lösta och alla kontroller som körs på PR:en är gröna, även de som reglerna inte kräver (till exempel `Dokumentation`).
8. **Laga orsaken, inte symptomet.** När något fallerar eller en granskning kräver ändring: läs allt, hitta orsaken och gör om den delen rätt. Små uppenbara fel får rättas direkt. Försvaga aldrig ett test, hoppa aldrig över en kontroll och tysta aldrig en varning.
9. **Säkerhet först.** Säkerhetslarm (CodeQL, Dependabot, Renovate) går före annat arbete. Länka issuet med `Closes #n`.
10. **Senaste versionerna.** Lägg aldrig till nya låsningar till ett versionsnummer; använd `@latest`, `node`, `latest` och versionsintervall. Befintliga låsningar som Renovate håller aktuella (till exempel `packageManager`, `requirements-docs.txt`, `.node-version`, lockfiler och actions låsta med commit-SHA) behålls och ändras bara av Renovate.
11. **Commits och titlar.** Sessionen signerar commits själv. Ändra aldrig gits användare, e-post eller signering, och använd aldrig `--no-verify`. PR-titeln blir commiten på `main` och skrivs på engelska enligt Conventional Commits: `typ: beskrivning` eller `typ(område): beskrivning`, med typ feat, fix, docs, refactor, perf, test, build, ci, chore, style eller revert.
12. **Dokumentation i samma PR.** Ändras beteende, inställningar eller kommandon uppdateras `README.md`, `docs/` och repots `CLAUDE.md` i samma PR.
13. **Inga lösa grenar.** Radera grenen när PR:en är mergad eller stängd. PR:er från forkar är förslag: gör ändringen själv på en egen gren om den är önskad, och stäng originalet med tack och länk.
14. **Noll kronor.** Slå aldrig på något som kan kosta pengar.
15. **GitHub bara via REST.** `gh pr` och `gh issue` fungerar inte i molnsessioner (GraphQL är avstängt). Använd `gh api` och de särskilda `ccr`-vägarna; se avsnittet GitHub från sessionen.
<!-- kärnregler:slut -->

## About the repo

- **`.coderabbit.yaml`** is the review base for every repo: everything free for public repos is on (including `early_access`), everything that can cost credits is off, and CodeRabbit is advisory and never blocks. README.md describes what the base sets.
- **The repos inherit** the base with `inheritance: true` in their own `.coderabbit.yaml` and add only what is repo-specific: `path_instructions` for their own folders, their own `path_filters`, labels, checks and linked repos. Lists merge with the repo's entries first, and an entry with the same `path` or `label` in the repo replaces the base's.
- **A change here takes effect in every repo** at its next review. Weigh every change against how it affects all repos.

## Commands

Check that the file is valid YAML:

```bash
npx --yes js-yaml@latest .coderabbit.yaml > /dev/null
```

Check formatting:

```bash
npx --yes prettier@latest --check .coderabbit.yaml README.md
```

Compare changed keys with the configuration reference at `https://docs.coderabbit.ai/reference/configuration` (the schema is `https://coderabbit.ai/integrations/schema.v2.json`).

## Rules for this repo

- **Language:** everything in this repo is English, documentation included. Only the core rules block above follows its source.
- **Shared here, specific there:** settings that apply to every repo belong here; risk instructions for specific files belong in each repo.
- **Instructions are written in English,** short and concrete, in the same style as the existing ones.
- **Never weaken the review:** do not turn off security tools and do not add exclusions for code.
- **Never blocking:** do not turn on automatic approvals, `fail_commit_status` or checks in `error` mode.
- **Zero cost:** do not turn on `finishing_touches` or `issue_enrichment.planning`; they can use credits. The instructions under `code_generation` may stay.
- **Labels** in `labeling_instructions` must exist in every repo. If you add a label, say so in the PR so the owner can create it. The review states `review-ready`, `needs-rework` and `rewrite-recommended` form a mutually exclusive group (`mutually_exclusive_groups`).

## Review in this repo

- **Gate:** `main` requires a PR, linear history, resolved review threads and CodeQL without errors or serious alerts. No other checks are required, but the ones that run must be green. The local checks under Commands are the test gate.
- **CodeRabbit:** this repo's own `.coderabbit.yaml` is the central base (`inheritance: false`) and inherits nothing. It governs the review here and is what the other repos inherit.
- **Sourcery and Codex:** also review, as GitHub apps without their own configuration here, and they review documentation too. Treat their threads like CodeRabbit's.

## Token optimizer

- **What it is:** the platform-skills token optimizer runs in audit mode. `.token-optimizer.yaml` holds the settings, `.token-optimizer/optimize.sh` is the hook script and `.claude/agents/platform-bulk-reader.md` is the cheap reader agent.
- **What it does:** a `PreToolUse` hook in `.claude/settings.json` logs oversized reads, and shell calls it cannot classify, to `.token-optimizer/decisions.log` (git-ignored). The log never holds command text. The hook never blocks a read.
- **Requirements:** `jq` and mikefarah `yq` v4 (the Python `yq` does not work). Create the git-ignored state directory once with `mkdir -p .token-optimizer/state`.
- **Local changes:** `optimize.sh` differs from the skill's original in two places: it never writes command text to the log, and it replaces control characters and caps field length in `log_line`. The hook command starts from `$CLAUDE_PROJECT_DIR`. Reapply these if the script is updated from the skill.
- **Changing it:** switch `mode` to `redirect` only after `.token-optimizer/probe/run-probe.sh` has verified delegation. Set `enabled: false` to turn it off.

## GitHub from a session

GraphQL is disabled, so `gh pr` and `gh issue` do not work. Use REST:

- Open a PR: `gh api repos/pissmyran/coderabbit/pulls -f base=main -f head=<branch> -f title='…' -f body='…'`
- PR from a branch: `gh api 'repos/pissmyran/coderabbit/pulls?head=pissmyran:<branch>&state=open'`
- Issue: `gh api repos/pissmyran/coderabbit/issues/<n>` and `…/issues/<n>/comments`
- Review threads: `gh api repos/pissmyran/coderabbit/pulls/<n>/ccr/review_threads`
- Reply in a thread: `gh api repos/pissmyran/coderabbit/pulls/<n>/comments/<id>/replies -f body='…'`
- Resolve a thread: `gh api -X POST repos/pissmyran/coderabbit/pulls/<n>/ccr/comments/<id>/resolve`
- Checks: `gh api repos/pissmyran/coderabbit/commits/<sha>/check-runs`
- Enable auto-merge: `gh api -X PUT repos/pissmyran/coderabbit/pulls/<n>/ccr/auto_merge -f merge_method=squash`
- If the PR is already mergeable (auto-merge is then refused), squash-merge directly: `gh api -X PUT repos/pissmyran/coderabbit/pulls/<n>/merge -f merge_method=squash`
- Close a PR: `gh api -X PATCH repos/pissmyran/coderabbit/pulls/<n> -f state=closed`
- Delete a branch: GitHub deletes the branch on merge. A closed PR's branch is deleted with `git push origin --delete <branch>` (the API route is blocked).
