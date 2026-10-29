# coderabbit

Den centrala CodeRabbit-konfigurationen för repona under `pissmyran`. Varje repo ärver `.coderabbit.yaml` härifrån med `inheritance: true` i sin egen `.coderabbit.yaml` och lägger bara till det som gäller just det repot: `path_instructions` för repots egna mappar, egna `path_filters`, egna etiketter och eventuella repospecifika kontroller.

## Principer

- **Allt gratis används:** alla funktioner som är gratis för publika repon är på, inklusive `early_access`.
- **Inget som kostar:** agentfunktioner som skriver commits eller PR:er (docstrings, enhetstester, autofix, fix CI, sammanslagningskonflikter, förenkling, planering) är av, eftersom de kan dra krediter. Deras instruktioner finns ändå kvar under `code_generation`.
- **Rådgivande, aldrig blockerande:** inga automatiska godkännanden, inga "request changes", ingen commit-status eller check som kan fallera, och alla kontroller före merge står på `warning`.
- **Lite brus:** profilen `quiet` lägger de viktigaste fynden inline och resten hopfällt i sammanfattningen. Dikter, fortunes, sekvensdiagram och tidsuppskattningar är av.

## Vad basen ställer in

- **Språk:** granskningar, sammanfattningar, titlar och instruktioner på engelska.
- **Automatik:** granskning av varje PR och varje ny push (utkast och PR:er med etiketten `wip` hoppas över), automatiska etiketter, automatiskt tilldelade granskare, PR-sammanfattning, och PR-titel när titeln innehåller `@coderabbitai`.
- **Granskning:** både kod och dokumentation granskas. Bara genererade filer och låsfiler hoppas över.
- **Instruktioner per sökväg:** allmänna regler för alla filer, dokumentation, agentinstruktioner, `.github/`, Workers-kod, Python, SSH-klienternas kod, skalskript, Docker, Cloudflare-konfiguration och dokumentationssajten.
- **Kontroller före merge (rådgivande):** titeln enligt Conventional Commits, beskrivningen, länkade ärenden, "Docs in the same PR" och "No personal values".
- **Verktyg:** säkerhetsskannrar (gitleaks, trufflehog, osv-scanner, trivy, opengrep, semgrep, presidio, skillspector, checkov, zizmor, ast-grep) och linters för repornas språk (actionlint, shellcheck, hadolint, dotenv-linter, ruff, clippy, swiftlint, detekt). Linters som överlappar Prettier eller bara ger stilbrus är av.
- **Kunskap:** CodeRabbit läser repots `CLAUDE.md` och `AGENTS.md` som granskningskriterier, söker på webben och lär sig av tidigare granskningar.
- **Ärenden:** nya ärenden berikas med sammanhang och får etiketter automatiskt.

## Etiketter

Etiketterna följer GitHubs standarduppsättning plus några extra, och alla finns i alla repon.

- **Typ av ändring (PR):** `fix`, `feature`, `enhancement`, `chore`, `documentation`, `dependencies`, `security`, `accessibility` och `duplicate`. En PR kan ha flera.
- **Granskningsläge (PR):** `review-ready`, `needs-rework` eller `rewrite-recommended`. De är en ömsesidigt uteslutande grupp, så en PR har högst en av dem.
- **Ärenden:** `bug`, `feature`, `enhancement`, `chore`, `dependencies`, `documentation`, `security`, `accessibility`, `question`, `duplicate` och `good first issue`.
- **Sätts av andra:** `slop` sätts av slop-detektionen, `wip` av författaren. `help wanted`, `invalid` och `wontfix` sätter ägaren.

Repona lägger till egna etiketter i sin egen `.coderabbit.yaml`, till exempel `sync-format` i Bastion-repona.

En ändring här slår igenom i alla repon vid nästa granskning.
