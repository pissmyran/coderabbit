# CLAUDE.md – coderabbit

Vägledning för Claude Code i `pissmyran/coderabbit`. Repot innehåller den centrala CodeRabbit-konfigurationen som alla andra repon ärver.

De gemensamma reglerna för alla repon finns i [STYRNING.md](https://github.com/pissmyran/.github/blob/main/STYRNING.md) i `pissmyran/.github`. Kärnreglerna nedan är en ordagrann kopia. Jämför versionen i markeringen med källan i början av varje session (`curl -sS https://raw.githubusercontent.com/pissmyran/.github/main/STYRNING.md`); är kopian äldre, uppdatera den först i en egen PR (`docs: sync core rules from .github`).

## Kärnregler

<!-- kärnregler:start version=2026-10-28 källa=https://github.com/pissmyran/.github/blob/main/STYRNING.md -->
1. **Inga personliga värden.** Repot är publikt. Skriv aldrig in riktiga id:n, domäner, e-postadresser, namn, nycklar, betallänkar eller kontouppgifter i kod, konfiguration, tester, dokumentation eller commit-meddelanden. Använd platshållare och `example.com`-värden.
2. **Inga hemligheter.** Tokens, lösenord, sessioner och nycklar hamnar aldrig i Git, loggar, issues eller publik dokumentation. Hemligheter läggs in utanför repot (Cloudflare, GitHub-miljöer, `wrangler secret put`).
3. **Gren och pull request.** Varje session börjar från senaste `main` (`git fetch origin main`) på en egen gren. `main` ändras bara genom en PR som squash-mergas. Ingen pushar direkt till `main`. En sessions arbete hålls i en PR.
4. **Testa lokalt före varje push.** Det finns inga testkontroller i CI. Repots egna tester och kontroller (avsnittet Kommandon i repots `CLAUDE.md`) är den enda testgrinden. Pusha aldrig med fallerande tester, och skriv tester för det du ändrar.
5. **Rör aldrig workflows.** `.github/workflows/`, `.github/actions/` och `CODEOWNERS` ändras inte av Claude, inte ens för att laga ett fel. Ingen kontroll stoppar det längre, men regeln gäller fullt ut. Behövs en ändring: beskriv den i ett issue och låt ägaren göra den. Bara Renovate och Dependabot får uppdatera dem.
6. **Granskning.** CodeRabbit granskar och CodeQL (GitHubs standardinställning) skannar. Varje granskningstråd måste lösas innan merge. Svara i tråden med vad du ändrade, eller med skälet om fyndet är brus.
7. **Auto-merge sist.** Slå på auto-merge först när CodeRabbit har granskat den senaste commiten och alla trådar är lösta. Ändrar PR:en bara filer som CodeRabbit hoppar över (`docs/`, `*.md`), räcker det att CodeQL är klar.
8. **Laga orsaken, inte symptomet.** När något fallerar eller en granskning kräver ändring: läs allt, hitta orsaken och gör om den delen rätt. Små uppenbara fel får rättas direkt. Försvaga aldrig ett test, hoppa aldrig över en kontroll och tysta aldrig en varning.
9. **Säkerhet först.** Säkerhetslarm (CodeQL, Dependabot, Renovate) går före annat arbete. Länka issuet med `Closes #n`.
10. **Senaste versionerna.** Lås aldrig verktyg, runtime eller paket till ett versionsnummer; använd `@latest`, `node`, `latest` och versionsintervall. Renovate håller allt aktuellt. Enda undantaget är actions, som låses med commit-SHA och uppdateras av Renovate.
11. **Commits och titlar.** Sessionen signerar commits själv. Ändra aldrig gits användare, e-post eller signering, och använd aldrig `--no-verify`. PR-titeln blir commiten på `main` och skrivs på engelska enligt Conventional Commits: `typ: beskrivning` eller `typ(område): beskrivning`, med typ feat, fix, docs, refactor, perf, test, build, ci, chore, style eller revert.
12. **Dokumentation i samma PR.** Ändras beteende, inställningar eller kommandon uppdateras `README.md`, `docs/` och repots `CLAUDE.md` i samma PR.
13. **Inga lösa grenar.** Radera grenen när PR:en är mergad eller stängd. PR:er från forkar är förslag: gör ändringen själv på en egen gren om den är önskad, och stäng originalet med tack och länk.
14. **Noll kronor.** Slå aldrig på något som kan kosta pengar.
15. **GitHub bara via REST.** `gh pr` och `gh issue` fungerar inte i molnsessioner (GraphQL är avstängt). Använd `gh api` och de särskilda `ccr`-vägarna; se avsnittet GitHub från sessionen.
<!-- kärnregler:slut -->

## Om repot

- **`.coderabbit.yaml`** är basen för granskningen i alla repon: svenska kommentarer, tyst profil, inga automatiska godkännanden, säkerhetsverktygen (gitleaks, trufflehog, osv-scanner, trivy, zizmor) på och dokumentationen (`docs/`, `*.md`) undantagen.
- **Repona ärver** basen med `inheritance: true` i sina egna `.coderabbit.yaml` och lägger bara till `path_filters` och `path_instructions`.
- **En ändring här slår igenom i alla repon** vid nästa granskning. Väg varje ändring mot hur den påverkar alla repon.

## Kommandon

Kontrollera att filen är giltig YAML:

```bash
npx --yes js-yaml@latest .coderabbit.yaml > /dev/null
```

Jämför ändrade nycklar mot schemat `https://coderabbit.ai/integrations/schema.v2.json`.

## Regler för det här repot

- **Gemensamt här, specifikt där:** inställningar som gäller alla repon hör hemma här; riskinstruktioner för enskilda filer hör hemma i respektive repo.
- **Instruktionerna skrivs på svenska**, korta och konkreta, i samma stil som de befintliga.
- **Försvaga aldrig granskningen:** slå inte av säkerhetsverktyg, slå inte på automatiska godkännanden och lägg inte till undantag för kod.
- **Etiketterna** i `labeling_instructions` ska finnas i alla repon. Lägger du till en etikett, säg det i PR:en så att ägaren kan skapa den.

## Granskning i det här repot

- **Grind:** `main` kräver PR, linjär historik, lösta granskningstrådar och CodeQL utan fel eller allvarliga larm. Inga andra kontroller krävs, så de lokala testerna i Kommandon är testgrinden.
- **CodeRabbit:** ärver den centrala konfigurationen i `pissmyran/coderabbit`. Repots `.coderabbit.yaml` pekar ut riskområden med egna granskningsinstruktioner; läs den del som gäller filerna du ändrar innan du börjar.

## GitHub från sessionen

GraphQL är avstängt, så `gh pr` och `gh issue` fungerar inte. Använd REST:

- Öppna PR: `gh api repos/pissmyran/coderabbit/pulls -f base=main -f head=<gren> -f title='…' -f body='…'`
- PR från gren: `gh api 'repos/pissmyran/coderabbit/pulls?head=pissmyran:<gren>&state=open'`
- Issue: `gh api repos/pissmyran/coderabbit/issues/<n>` och `…/issues/<n>/comments`
- Granskningstrådar: `gh api repos/pissmyran/coderabbit/pulls/<n>/ccr/review_threads`
- Svara i tråd: `gh api repos/pissmyran/coderabbit/pulls/<n>/comments/<id>/replies -f body='…'`
- Lös tråd: `gh api -X POST repos/pissmyran/coderabbit/pulls/<n>/ccr/comments/<id>/resolve`
- Kontroller: `gh api repos/pissmyran/coderabbit/commits/<sha>/check-runs`
- Auto-merge på: `gh api -X PUT repos/pissmyran/coderabbit/pulls/<n>/ccr/auto_merge`
- Stäng PR: `gh api -X PATCH repos/pissmyran/coderabbit/pulls/<n> -f state=closed`
- Radera gren: `gh api -X DELETE repos/pissmyran/coderabbit/git/refs/heads/<gren>`
