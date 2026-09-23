---
tags:
  - project/duumbi
  - concept/operations
  - concept/security
status: active
created: 2026-09-23
updated: 2026-09-23
verified: 2026-09-23
---

# DUUMBI - Credential Inventory and Rotation Guide

A DUUMBI credentialök üzemeltetési nyilvántartása: ki használja őket, hol készül a csereérték, és melyik beállításba kell beírni. Titokértéket ez a dokumentum nem tartalmaz.

## Bizonyíték és hatókör

2026-09-23-án élő GitHub API-lekérdezés történt a `duumbi`, `duumbi-vault`, `duumbi-infra`, `duumbi-registry`, `duumbi-web`, `duumbi-auth` és `duumbi-loop` repository Actions secretneveire és az aktuális alapértelmezett ág workflow-jaira. A Pulumi-beállítások kapcsolata a távoli forráskódból származik. Azure-, Doppler-, Slack-app- és személyes PAT-beállítások élő értékellenőrzése nem történt; environment/organization secretleltár sem készült.

- **Ellenőrzött:** secret neve létezik a repository-ban; a megnevezett forrás hivatkozik rá. Ez nem bizonyítja az érték érvényességét vagy egy futás sikerét.
- **Felhasználói adat:** személyes PAT megnevezése, jogosultságai és lejárata az „Ütemezett tokenellenőrzés” beszélgetésben közölt, képernyőképeken alapuló nyilvántartásból. A képernyőképeket ebben az auditban nem vizsgáltuk újra.
- **Azonosítandó:** a személyes PAT neve és egy secret között nincs bizonyított kapcsolat. Azonos név vagy hasonló scope önmagában nem bizonyít azonosságot.

A `updated_at` a secret legutóbbi GitHub-módosításának ideje, **nem lejárati dátum**. Az operatív felelősség a credential tulajdonosáé és az adott szolgáltatás üzemeltetőjéé; ahol nincs külön név, a projektgazda koordinálja a megújítást. Ez javasolt felelősségi rend, nem ellenőrzött személyi hozzárendelés.

## Személyes GitHub PAT-ok és lejáratok

| GitHubon látható név | Típus és közölt scope | Nyilvántartott lejárat | Fogyasztó és azonosítási állapot | Hol kell megújítani / megadni? |
|---|---|---|---|---|
| Cursor | classic; `repo` | 2026-09-24 | Pontos fogyasztó nem azonosított. A név nem bizonyítja, hogy Cursor-secretként van tárolva. | [Classic PAT-ok](https://github.com/settings/tokens); a csere célhelyét előbb azonosítani kell a Cursor/Git-integráció beállításaiban. |
| Packages | classic; `project`, `read:packages` | 2026-09-20 | A nyilvántartott dátum szerint lejárt. Lehetséges fogyasztó a registry `GHCR_PAT`, illetve a Loop GHCR-credentialje; egyik azonosság sem bizonyított. | [Classic PAT-ok](https://github.com/settings/tokens); az igazolt fogyasztó összes tárolási helyén frissítendő. |
| Grok | classic; `project`, `repo` | 2026-10-06 | Pontos Grok Bot/VM GitHub-bejelentkezés vagy secretnév nem azonosított. Külön credential az `XAI_API_KEY`-től. | [Classic PAT-ok](https://github.com/settings/tokens); a Grok környezet tényleges GitHub-auth tárolóját kell azonosítani. |
| Slack | classic; `repo` | 2026-10-07 | Erős jelölt a Slack bridge Pulumi `githubPat` → Azure `GITHUB_TOKEN` kapcsolata. A név szerinti azonosság még nem igazolt. Külön credential a `SLACK_BOT_TOKEN`-től. | [Classic PAT-ok](https://github.com/settings/tokens); ha ez a bridge PAT-ja, a platform stack `githubPat` beállítását kell frissíteni az alábbi eljárással. |
| Vault | fine-grained | 2026-10-12 | A felhasználói nyilvántartás szerint `duumbi-vault` → `DUUMBI_INTAKE_DISPATCH_TOKEN`. A secret és a fogyasztó workflow élő forrásból ellenőrzött; a PAT címkéje nem kérdezhető vissza ebből. | [Fine-grained PAT-ok](https://github.com/settings/personal-access-tokens); majd a [vault Actions secretje](https://github.com/hgahub/duumbi-vault/settings/secrets/actions). |
| DUUMBI GitHub Actions Project V2 updater | classic; `project`, `repo` | 2026-10-22 | A felhasználói nyilvántartás szerint `duumbi` → `GH_PROJECT_PAT`. A secret és fogyasztói ellenőrzöttek. | [Classic PAT-ok](https://github.com/settings/tokens); majd a [duumbi Actions secretje](https://github.com/hgahub/duumbi/settings/secrets/actions). |

A Project V2 updater korábbi `2026-10-26` dátuma helyett a javított `2026-10-22` az irányadó felhasználói adat. A dátumok minden rotáció után frissítendők; a táblázat nem állítja, hogy azóta nem történt csere.

## Ellenőrzött Actions credentialök

Az Actions-beviteli hely minden esetben: repository → **Settings → Secrets and variables → Actions → Repository secrets → megadott név → Update**. Az alábbi linkek a megfelelő repository beállítására mutatnak.

| Credential | Hol kell megadni? | Ki / mire használja? | Hol készül az új érték? |
|---|---|---|---|
| `GH_PROJECT_PAT` | [duumbi Actions](https://github.com/hgahub/duumbi/settings/secrets/actions) | Project V2 olvasás/írás, intake és triage, státusz- és jóváhagyási workflow-k; az enrichment a vaultba is ír. A projektgazda PAT-ja automatizálást szolgál ki. | GitHub classic PAT; a Project V2 mellett az érintett repository-hozzáférést is meg kell őrizni. [Orchestration forrás](https://github.com/hgahub/duumbi/blob/ab4c8776f795052d3669774a7ff12bdd3231fd02/docs/automation/agentic-development-orchestration.md). |
| `DUUMBI_INTAKE_DISPATCH_TOKEN` | [duumbi-vault Actions](https://github.com/hgahub/duumbi-vault/settings/secrets/actions) | A vault `intake-events.yml` workflow-ja `repository_dispatch` eseményt küld a `hgahub/duumbi` felé. | Fine-grained PAT: resource owner `hgahub`, kizárólag `duumbi`, **Contents: Read and write**. Fontos: a token a vaultban van tárolva, de a jogosultság célja a `duumbi`. [Setup](https://github.com/hgahub/duumbi/blob/ab4c8776f795052d3669774a7ff12bdd3231fd02/docs/automation/intake-events-setup.md). |
| `DEEPSEEK_API_KEY` | [duumbi Actions](https://github.com/hgahub/duumbi/settings/secrets/actions) | Stage 3b Inbox enrichment és `clarification-routing.yml` LLM-hívásai. | [DeepSeek Platform](https://platform.deepseek.com/) → API keys → új kulcs. [Hivatalos útmutató](https://api-docs.deepseek.com/). |
| `ZHIPUAI_API_KEY` | [duumbi Actions](https://github.com/hgahub/duumbi/settings/secrets/actions) | `triage-queue-refill.yml`: Z.ai/Zhipu-alapú issue-triage, amikor a queue feltöltése szükséges. | A használt szolgáltatói fiók API-key kezelője; Z.ai esetén [Z.ai](https://z.ai/) → API Keys. A tényleges fiók és endpoint összetartozását ellenőrizni kell. [Útmutató](https://docs.z.ai/guides/overview/quick-start). |
| `SLACK_BOT_TOKEN` | [duumbi Actions](https://github.com/hgahub/duumbi/settings/secrets/actions) | Jóváhagyáskérések, tisztázás, státusz, review és handoff Slack-üzenetek. | [Slack Apps](https://api.slack.com/apps) → érintett app → OAuth & Permissions → bot OAuth-token kezelése; rotáció az app OAuth-beállításának megfelelően. |
| `SLACK_BOT_TOKEN` | [duumbi-web Actions](https://github.com/hgahub/duumbi-web/settings/secrets/actions) | `linkedin-progress-reminder.yml`: LinkedIn-progress emlékeztető Slackre. | Az ehhez tartozó Slack app tokenkezelése. A `duumbi`-ban és itt tárolt érték azonossága nem ellenőrzött. |
| `CODECOV_TOKEN` | [duumbi Actions](https://github.com/hgahub/duumbi/settings/secrets/actions) | `coverage.yml`: coverage-jelentés feltöltése a Codecovba. | Codecov → `hgahub/duumbi` → Settings / General → upload token kezelése. Ellenőrizni kell, repository- vagy account/global-token van-e használatban. [Útmutató](https://docs.codecov.com/docs/adding-the-codecov-token). |
| `GHCR_PAT` | [duumbi-registry Actions](https://github.com/hgahub/duumbi-registry/settings/secrets/actions) | `cd.yml`: a `ca-duumbi-registry` Container App registry-credentialjének beállítása a `rg-duumbi-registry` resource groupban; GHCR image pull. Az image push a workflow automatikus `GITHUB_TOKEN`-jét használja. | GitHub classic PAT megfelelő package-hozzáféréssel; letöltéshez `read:packages`. A „Packages” nevű PAT-tal való azonosság nyitott. [GHCR-dokumentáció](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry). |
| `AZURE_CREDENTIALS` | [duumbi-registry Actions](https://github.com/hgahub/duumbi-registry/settings/secrets/actions) | `cd.yml` → `azure/login`: Azure service principal hitelesítés a registry deployhoz. | Azure Portal → Microsoft Entra ID → App registrations → az érintett alkalmazás → Certificates & secrets → új client secret. A teljes JSON-t frissíteni kell: `clientId`, `clientSecret`, `subscriptionId`, `tenantId`. A konkrét app-azonosító nincs ebben az auditban azonosítva. [Microsoft útmutató](https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure-secret). |
| `AZURE_STATIC_WEB_APPS_API_TOKEN` | [duumbi-web Actions](https://github.com/hgahub/duumbi-web/settings/secrets/actions) | `deploy.yml`: a weboldal deploymentje. Infrastrukturális cél: `swa-duumbi-web`. | Azure Portal → megfelelő Static Web App → Manage deployment token → Reset; az új értéket a GitHub secretbe kell írni. |
| `AZURE_STATIC_WEB_APPS_API_TOKEN_DOCS` | [duumbi-web Actions](https://github.com/hgahub/duumbi-web/settings/secrets/actions) | `deploy-docs.yml`: a dokumentáció deploymentje. Infrastrukturális cél: `swa-duumbi-docs`. | A dokumentációhoz tartozó Static Web App deployment-tokenjének resetje, majd a DOCS secret frissítése. [Microsoft útmutató](https://learn.microsoft.com/en-us/azure/static-web-apps/deployment-token-management). |

Ezeknél a nem PAT-ként beazonosított credentialöknél nincs ellenőrzött lejárati dátum. Az ismeretlen lejárat nem jelent korlátlan érvényességet.

### Jelen lévő, de az ellenőrzött workflow-kból nem használt bejegyzések

| Repository / név | Megfigyelés | Következő azonosítási lépés |
|---|---|---|
| `duumbi` / `AZURE_STATIC_WEB_APPS_API_TOKEN` | A repository secretlistájában jelen van, az aktuális workflow-k nem hivatkozzák. A web deploy a `duumbi-web` repository-ban van. | Azonosítani kell, mely SWA-hoz tartozik és használja-e külső/korábbi folyamat. Nem törölhető pusztán a workflow-találat hiánya alapján. |
| `duumbi` / `DISCORD_WEBHOOK_URL` | Jelen van, az aktuális workflow-k nem hivatkozzák. A webhook URL maga hitelesítő adat. | Az érintett Discord szerver/csatorna → Integrations → Webhooks és a külső fogyasztók ellenőrzése szükséges. Pontos webhook nincs azonosítva. |

`SLACK_REVIEW_CHANNEL_ID` (`duumbi`) és `DUUMBI_LINKEDIN_SLACK_CHANNEL_ID` (`duumbi-web`) azonosítók, nem tokenek, még ha secretként vannak is tárolva. `DUUMBI_AGENT_DISPATCH_CHANNEL_ID` több workflow-ban opcionális hivatkozás, de a lekért repository-secretlistában nem szerepelt; ebből nem következik, hogy environment/organization szinten sincs beállítva.

## Slack bridge: a Pulumi a tartós beállítási hely

| Pulumi platform secret | Azure Function app setting | Fogyasztó / szerep | Megújítás |
|---|---|---|---|
| `githubPat` | `GITHUB_TOKEN` | `func-duumbi-slack-bridge`: Slack-interakcióból GitHub-dispatch. Ez tartós PAT, és különbözik az Actions automatikus, azonos nevű tokenjétől. | GitHub PAT csere, majd `duumbi-infra/scripts/rotate-slack-github-token.sh`. A „Slack” nevű PAT lehetséges kapcsolata még igazolandó. |
| `slackBotToken` | `SLACK_BOT_TOKEN` | Ugyanennek a Function Appnak a Slack appja; többek között döntési modal megnyitása. | Slack app tokenkezelése, majd `duumbi-infra/scripts/configure-slack-bot-token.sh`; ez csak a titkosított konfigurációt menti, külön Pulumi preview/apply szükséges. |
| `slackSigningSecret` | `SLACK_SIGNING_SECRET` | A Slacktől érkező HTTP-kérések aláírásának ellenőrzése. | Slack app → Basic Information → App Credentials → Signing Secret kezelése; a platform stackben `slackSigningSecret` frissítés, majd kontrollált deploy. |

A pontos stack: `hgahub/duumbi-infra/platform`. A csereérték saját interaktív terminálban, Pulumi rejtett beviteli promptjába kerül; a `Pulumi.platform.yaml` titkosított változása verziókezelendő. Csak az Azure Portalban átírt értéket egy későbbi Pulumi-frissítés felülírhatja. A teljes app-settings lista kezelésénél a `WEBSITE_RUN_FROM_PACKAGE=1` megőrzése szükséges a function kód betöltéséhez.

A rotáció részletes és meglévő eljárása: [bridge GitHub PAT](https://github.com/hgahub/duumbi-infra/blob/20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2/docs/slack-token-rotation.md), [bridge Slack bot token](https://github.com/hgahub/duumbi-infra/blob/20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2/docs/slack-bot-token.md), [[Slack Bridge Deployment Boundary]].

A Slack OAuth tokenrotáció külön működési mód: bekapcsolva lejáró access token és refresh-folyamat szükséges. Nem elegendő egyszer egy új bot tokent bemásolni egy statikus secretbe, ha a fogyasztó nem kezeli a frissítést. Az app aktuális rotációs módja nem lett ellenőrizve. [Slack dokumentáció](https://docs.slack.dev/authentication/using-token-rotation/).

## További, forráskódban deklarált runtime credentialök

Ezeket a távoli infrastruktúrakód igényli vagy támogatja. A tényleges Azure/Pulumi/Doppler-konfiguráltságot és lejáratot nem ellenőriztük. A megnevezett PAT-ok bármelyikével való azonosságot nem szabad feltételezni.

| Környezet / beállítás | Fogyasztó és cél | Hol kell cserélni? |
|---|---|---|
| Registry stack: `githubClientSecret` → `GITHUB_CLIENT_SECRET` | Registry GitHub OAuth bejelentkezés; `githubClientId` a hozzá tartozó azonosító. | Az érintett [GitHub OAuth app](https://github.com/settings/developers) client secretje, majd Pulumi `hgahub/duumbi-infra/registry` secretkonfiguráció és deploy. Az app pontos azonossága nyitott. |
| Registry stack: `jwtSecret` → `JWT_SECRET` | Registry tokenek aláírása. | Kriptográfiailag biztonságos új alkalmazástitok, Pulumi registry stack; a meglévő tokenek érvényességére gyakorolt hatást a csere előtt fel kell mérni. |
| Loop staging: `githubClientSecret`, `githubAppPrivateKey`, `githubWebhookSecret` | GitHub App/OAuth/webhook integráció. `githubAppId` és `githubClientId` kapcsolódó azonosítók. | Az érintett GitHub App beállításai, majd a staging Pulumi stack megfelelő secretjei. |
| Loop staging: `ghcrToken` | Loop web/worker konténerképek letöltése; `ghcrUsername` a felhasználónév. | GitHub classic package PAT, majd staging Pulumi secret. |
| Loop staging: `loopDatabaseUrl` | Adatbázis kapcsolat, benne hozzáférési adat. | Az adatbázis-szolgáltatónál cserélt credential, majd staging Pulumi secret. |
| Loop production: `DUUMBI_LOOP_GITHUB_CLIENT_SECRET`, `DUUMBI_LOOP_GITHUB_APP_PRIVATE_KEY`, `DUUMBI_LOOP_GITHUB_WEBHOOK_SECRET` | Production Loop GitHub-integráció. | GitHub App-kezelés; a kód szerinti forrás Doppler `duumbi-loop/prd`, onnan Azure Key Vault `kv-duumbi-loop-prod`, majd managed identity secret-hivatkozások a Container Appokban. A szinkronizálás élő állapota és végrehajtója nincs igazolva. |
| Production: `DUUMBI_LOOP_GHCR_TOKEN` | Loop és Auth GHCR-image pull. Key Vault-név: `duumbi-loop-ghcr-token`. | GitHub classic PAT; ugyanazon Doppler → Key Vault útvonal. A registry `GHCR_PAT`-tal való azonosság ismeretlen. |
| Production Auth: `DUUMBI_AUTH_GITHUB_CLIENT_SECRET`, `DUUMBI_AUTH_GOOGLE_CLIENT_SECRET` | GitHub- és Google OAuth-bejelentkezés. | Az érintett GitHub OAuth app, illetve Google Cloud projekt OAuth kliensének kezelése; Doppler → Key Vault → Auth Container App. A konkrét szolgáltatói appok nincsenek azonosítva. |
| Production: `DUUMBI_AUTH_HMAC_SECRET` | Auth és Loop közös hitelesítési titka. | Új biztonságos alkalmazástitok a Doppler → Key Vault útvonalon; az Auth és Loop fogyasztókat összehangoltan kell átállítani. |
| Production: `DUUMBI_LOOP_DATABASE_URL`, `DUUMBI_AUTH_DATABASE_URL` | Loop és Auth adatbázis-kapcsolata. | Az adatbázis credentialjének cseréje, majd Doppler → Key Vault → érintett alkalmazás. |

Forrás: [registry stack](https://github.com/hgahub/duumbi-infra/blob/20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2/stack-registry.ts), [staging stack](https://github.com/hgahub/duumbi-infra/blob/20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2/stack-loop-staging.ts), [production stack](https://github.com/hgahub/duumbi-infra/blob/20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2/stack-loop-production.ts). A Pulumi által Azure API-ból kapott Storage/Log Analytics kulcsok további infrastruktúra-credentialök; ezek teljes életciklus-auditja nem része a PAT-leltárnak.

### Automatikus és opcionális hitelesítés

- **Actions `GITHUB_TOKEN`:** a GitHub biztosítja a workflow-futásokhoz; nem szerepel kézzel létrehozandó repository secretként. A registry/auth/loop image-publikálása ezt használja. Nem tartozik a hat személyes PAT lejárati listájába.
- **Azure OIDC:** a `duumbi` Slack bridge deployja és a `duumbi-auth` production image deployja `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID` repository-változókat és federált hitelesítést használ. Ezen az útvonalon nincs kézzel rotálandó `AZURE_CREDENTIALS` JSON. A registry régebbi deployja továbbra is azt használja.
- **`XAI_API_KEY`:** a DUUMBI xAI-provider credentialneve, de a lekért repository Actions-secretlistákban nem szerepelt. A CLI/provider támogatás önmagában nem bizonyít telepített kulcsot. A tényleges szolgáltatói account és runtime beviteli hely azonosítandó; a Grok nevű GitHub PAT külön tétel.
- **Más provider- és agent-credentialök:** helyi fejlesztői gépek, Grok VM, Cursor account, előfizetéses bejelentkezések és további szolgáltatói kulcsok nem voltak teljeskörűen auditálva. Ezeket csak igazolt fogyasztóval szabad „használatban” tétellé emelni.

## DUUMBI token expiry watch

Forrásbeszélgetés: [Ütemezett tokenellenőrzés](https://chatgpt.com/c/6ab392bf-078c-83eb-a121-c6720fc65477). A beszélgetés szerint a feladat felhőben naponta fut, a következő naptári napon lejáró credentialről a `#duumbi-ops` (`C08SK7E6R7T`) csatornára jelez, és a hat fenti PAT dátumát tartalmazza.

**Ellenőrzött eredmény:** a [Cursor/Packages riasztás](https://hgabor.slack.com/archives/C08SK7E6R7T/p1790154689814529) a Slackből visszaolvasva létezik, és az ismeretlen token–secret kapcsolatokat helyesen jelöli. Ez egy elküldött figyelmeztetés bizonyítéka, önmagában nem igazolja a napi ütemező működését.

**Nem ellenőrzött:** scheduler-azonosító, aktív/szüneteltetett állapot, pontos napi futási idő, időzóna, következő futás és futási előzmények. A helyi Codex automations között nem található ilyen nevű konfiguráció. A felhős task élő kezelőfelületének elérése nem sikerült. A korábbi asszisztensi „frissítettem” állítás ezért nem kezelhető élő scheduler-ellenőrzésként.

A 2026-09-23-i nyilvántartás szerint a Cursor lejárata másnap, a Packages dátuma már elmúlt. A többi előző napi célértesítés: Grok 10-05, Slack 10-06, Vault 10-11, Project V2 updater 10-21. Ezek a megadott dátumokból számított napok, nem igazolt jövőbeli ütemezőfutások.

### Fenntartási szabály és ellenőrzendő működés

Rotáció után a tulajdonos frissítse ezt a nyilvántartást **és a meglévő felhős feladat credentiallistáját**: új lejárat, ellenőrzés napja, típus/scope, minden fogyasztó és tárolási hely. A GitHub-secret módosítása önmagában nem frissíti a task promptját. Nem szabad feltételezni, hogy a feladat automatikusan olvassa ezt a vaultjegyzetet.

A következő scheduler-audit ellenőrizze az időzónát és a napi futást, a riasztások ismétlésének elkerülését, a kihagyott futás után már lejárt token kezelését és a Slack-küldési hibák láthatóságát. Javasolt naptári időzóna: `Europe/Budapest`; ez jelenleg javaslat, nem ellenőrzött beállítás. Az ismeretlen dátumú credentialökből nem számolható hiteles lejárati riasztás.

## Rotációs munkamenet

1. Azonosítsd a szolgáltatói credentialt, tulajdonost, scope-ot és **minden** fogyasztót. A PAT-címke és a secretnév kapcsolatát itt rögzítsd; bizonyítatlan kapcsolat alapján ne cserélj több rendszert egyszerre.
2. A szolgáltató kezelőfelületén készíts cserecredentialt vagy regeneráld a meglévőt. Regeneráláskor a régi érték azonnal érvénytelenné válhat; több fogyasztónál, ahol támogatott, előbb külön cserecredentialt készíts és tervezz átállást.
3. A megfelelő repository secretet, Pulumi secretet vagy Doppler/Key Vault forrást frissítsd. A titokérték a szolgáltató biztonságos mezőjébe vagy rejtett promptba kerüljön.
4. A konfiguráció mentése után alkalmazd a szükséges runtime-frissítést a szolgáltatás saját runbookja szerint. Például egy új `GHCR_PAT` repository secret önmagában nem írja át a már futó Azure Container App registry-beállítását.
5. Az érintett következő engedélyezett művelettel igazold a hozzáférést. A bridge aláírás nélküli HTTP 401 válasza csak a function betöltését igazolja, nem a GitHub PAT vagy Slack bot token érvényességét; a dispatch HTTP 204 pedig csak átvételt jelent.
6. Frissítsd a lejárati nyilvántartást és a meglévő felhős watchot. Sikeres átállás után vond vissza a leváltott credentialt, ha az még érvényes. Production deploy és jóváhagyási művelet a meglévő Owner-jóváhagyási rend szerint történjen.

## Források és frissítés

Az audit során beolvasott távoli források:

| Repository | Commit | Fő bizonyíték |
|---|---|---|
| duumbi | `ab4c8776f795052d3669774a7ff12bdd3231fd02` | [workflow-k](https://github.com/hgahub/duumbi/tree/ab4c8776f795052d3669774a7ff12bdd3231fd02/.github/workflows) és automation dokumentáció |
| duumbi-vault | `247068d4cb4806acfcdb90159f3d12e8d0bc2883` | [intake-events.yml](https://github.com/hgahub/duumbi-vault/blob/247068d4cb4806acfcdb90159f3d12e8d0bc2883/.github/workflows/intake-events.yml) |
| duumbi-infra | `20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2` | [platform stack](https://github.com/hgahub/duumbi-infra/blob/20d1d864f976c6b7ee7bbc0ed4933dfd3084b2a2/stack-platform.ts), registry/loop stackek és rotációs útmutatók |
| duumbi-registry | `a3adef41242229feaf00f5352fee7d7a7c5891ce` | [cd.yml](https://github.com/hgahub/duumbi-registry/blob/a3adef41242229feaf00f5352fee7d7a7c5891ce/.github/workflows/cd.yml) |
| duumbi-web | `29f9a7877874c940cfde5a1092506ba719070dc3` | [workflow-k](https://github.com/hgahub/duumbi-web/tree/29f9a7877874c940cfde5a1092506ba719070dc3/.github/workflows) |
| duumbi-auth | `d3aee94e6a4abd5a693fad194e0080d1e969a74f` | [production image workflow](https://github.com/hgahub/duumbi-auth/blob/d3aee94e6a4abd5a693fad194e0080d1e969a74f/.github/workflows/publish-production-image.yml) |
| duumbi-loop | `28c4e2c0ccab53163f8162cd3eaff14b61662d7d` | [staging image workflow](https://github.com/hgahub/duumbi-loop/blob/28c4e2c0ccab53163f8162cd3eaff14b61662d7d/.github/workflows/publish-staging-image.yml) |

Secret-metaadatok: GitHub REST `GET /repos/hgahub/{repo}/actions/secrets`, 2026-09-23. A `GH_PROJECT_PAT` legutóbbi módosítása `2026-09-22T19:44:02Z`; a `DUUMBI_INTAKE_DISPATCH_TOKEN`-é `2026-09-12T20:15:59Z`; a registry `GHCR_PAT`-é `2026-06-22T08:05:16Z`. Ezek nem lejáratok és nem PAT-azonossági bizonyítékok. A `duumbi-infra`, `duumbi-auth`, `duumbi-loop` repository-szintű Actions-secretlistája üres volt; ebből nem következik a runtime credentialök hiánya.

GitHub PAT-kezelés: [hivatalos útmutató](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens). A szolgáltatói felületek részletes útvonalai változhatnak; rotációkor az aktuális appot és fiókot kell kiválasztani.

## Related

- [[DUUMBI Repository Map]]
- [[DUUMBI - Agentic Development Runbook]]
- [[Slack Bridge Deployment Boundary]]
- [[DUUMBI Azure Infrastructure Model]]
- [[Registry Authentication Model]]
