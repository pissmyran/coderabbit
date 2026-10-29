# coderabbit

Den centrala CodeRabbit-konfigurationen för repona under `pissmyran`. Varje repo ärver `.coderabbit.yaml` härifrån med `inheritance: true` i sin egen `.coderabbit.yaml` och lägger bara till `path_filters` och `path_instructions`.

## Vad basen ställer in

- **Språk:** granskningar, sammanfattningar och instruktioner på engelska.
- **Profil:** tyst, inga automatiska godkännanden och ingen kodgenerering.
- **Säkerhetsverktyg:** gitleaks, trufflehog, osv-scanner, trivy och zizmor, plus actionlint och shellcheck.
- **Dokumentationen** (`docs/`, `*.md`) hoppar CodeRabbit över; Sourcery och Codex granskar den.
- **Etiketter:** `fix`, `feature`, `enhancement`, `chore`, `documentation`, `dependencies`, `security` och `accessibility` beskriver ändringen. `review-ready`, `needs-rework` och `rewrite-recommended` beskriver den senaste granskningen, och slop-detektionen sätter `slop`. CodeRabbit kan bara sätta etiketter, så den som rättar fynden tar bort en granskningsetikett som inte längre stämmer. Alla etiketter ska finnas i alla repon.

En ändring här slår igenom i alla repon vid nästa granskning.
