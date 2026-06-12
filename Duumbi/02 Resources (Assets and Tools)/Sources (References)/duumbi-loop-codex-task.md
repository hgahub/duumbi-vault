# Codex feladat: DUUMBI Loop cloud szolgáltatás megtervezése és milestone-alapú implementációs bontása

> **Átadható Codexnek.** Ez a fájl a következő DUUMBI cloud szolgáltatás termék-, architektúra-, repo-, infra-, UI-, adatmodell-, workflow- és elfogadási tervét tartalmazza.  
> **Elsődleges cél:** olyan Rust-alapú, gyors, webes felülettel rendelkező szolgáltatás megépítése, amely a GitHub/GitLab issue → intake → spec → review → closure fejlesztési ciklust végigviszi, és a kódbázisból karbantartott gráf-alapú tudásbázist épít.

> **2026-06-12 frissítés:** nem MVP-ben gondolkodunk. Milestone-ok vannak, iteratívan haladunk előre, és a DUUMBI-native workflow az elsődleges termékmodell. GitHub/GitLab adapterek fontosak, de nem írhatják felül a DUUMBI-native Loop irányt. A roadmap szerinti beillesztés: **M6 / H2 2027**, v1.0 GA előtt, M2/M3/M5 függőségekkel.

> **2026-06-12 kiegészítés (part 2):** a pricing/csomagok, billing- és onboarding-folyamatok (regisztráció, meghívások, lemondás, dunning), a settings/code provider/repository/analytics/run/knowledge felületek részletes UI-UX specifikációja, valamint az üzemeltetői (staff) monitoring a [[duumbi-loop-codex-task-part2]] dokumentumban van kidolgozva. **Ütközés esetén a part 2 az irányadó.** Codex mindkét dokumentumot olvassa el, és az M6.0 v2 dokumentum olvassza össze őket.

## 0.1. Rögzített döntések

- **Milestone framing:** a terv minden `MVP` megnevezése milestone-ra vagy első cloud milestone-ra értendő. Ne használj MVP-scope gondolkodást.
- **Adatbázis:** default metadata database = **Neon Postgres**. Ne használj Azure Database for PostgreSQL-t a bevezető időszak defaultjaként.
- **Graph backend:** első cloud milestone-ban Postgres-backed property graph tables ugyanabban a Neon adatbázisban. Neo4j és Kuzu későbbi adapterként maradhatnak, de nem default.
- **Billing:** Stripe a default billing provider.
- **Email login:** magic link.
- **Központi auth:** külön `hgahub/duumbi-auth` repo/service legyen már az első cloud milestone-tól.
- **Source retention:** raw repo snapshot default = 7 nap; org/repo Settings-ben állítható: 0, 7, 14, 30, 90 nap. `0 days` = ephemeral clone, sikeres indexelés után törlés.
- **LLM data policy:** régiókra bontott policy kell: EU, USA, Kína. Org admin szabályozza provider/model engedélyezést, fallbacket és prompt/source retentiont.
- **GitLab Self-Hosted:** első cloud milestone-ban kézi webhook setup elég, SSRF védelemmel és connection testtel.
- **Spec PR branch protection:** ha a bot nem tud branch-et vagy PR-t létrehozni, UI-ban jelezni kell, és email + Slack értesítés menjen; legyen manual spec bundle fallback.
- **Knowledge update:** closure defaultban automatikusan publikálja a knowledge update-et. Candidate-only mód legyen org settingként.
- **Review strictness:** defaultban comment mode legyen blocking issue esetén is. Strict mode kapcsolóval kérhető provider-native blocking review.

---

## 0. Javasolt név, domainek, repo

### Szolgáltatás neve

**DUUMBI Loop**

Indoklás: a szolgáltatás lényege az iteratív fejlesztési ciklus lezárása: issue megértése, specifikáció, implementáció validálása, review, tudásfrissítés, majd újabb iteráció.

### Domainek

- Marketing / publikus felület: `https://loop.duumbi.dev`
- Dashboard: `https://app.loop.duumbi.dev`
- API: `https://api.loop.duumbi.dev`
- Webhook endpointok: `https://hooks.loop.duumbi.dev`
- Központi DUUMBI SSO issuer: `https://auth.duumbi.dev`

### Repo javaslat

Elsődleges új repo:

```text
hgahub/duumbi-loop
```

Kiegészítő repo módosítások:

```text
hgahub/duumbi-infra      # Azure/Pulumi infra bővítése Loop erőforrásokkal
hgahub/duumbi-web        # loop.duumbi.dev marketing, blog, docs.duumbi.dev/loop dokumentáció
hgahub/duumbi-registry   # meglévő belépés migrálása / összekötése központi auth.duumbi.dev SSO-val
hgahub/duumbi-auth       # központi auth.duumbi.dev issuer és identity/account service
```

Ha később szét kell választani a nagyobb komponenseket:

```text
hgahub/duumbi-loop-workers
hgahub/duumbi-loop-graph
```

Az első cloud milestone-ban a `duumbi-loop` maradjon Rust workspace, hogy Codex könnyen tudjon több komponensen átívelő módosításokat végezni. A központi auth viszont külön repo: `hgahub/duumbi-auth`.

---

## 1. Kontextus és jelenlegi DUUMBI repo-határok

Codex első lépésként olvassa el a meglévő repók `README.md`, `AGENTS.md`, `CLAUDE.md`, `Cargo.toml`, CI és deployment fájljait.

A rendelkezésre álló DUUMBI repo-térkép alapján:

- `duumbi-web` felel a `duumbi.dev`, `docs.duumbi.dev`, publikus üzenetek, blog és fejlesztői dokumentáció felületekért.
- `duumbi-registry` Rust/Axum alapú registry szerver, jelenleg modulok tárolására, kiszolgálására, keresésére, auth-ra és token-kezelésre.
- `duumbi-infra` Azure infrastruktúrát definiál Pulumival, többek között DNS, Static Web Apps, Container Apps, Key Vault, Log Analytics, budget és registry hosting komponensekkel.
- A DUUMBI termékfilozófia intent-first, graph-centered, evidence-oriented és agentic fejlesztési modellt követ.

A publikus böngészés során a `hgahub/duumbi-infra` repo nem volt nyilvánosan elérhető. Codex futtatáskor ettől függetlenül a tényleges checkoutban kötelező átnézni, mert az infra változtatásokat a meglévő Pulumi szerkezethez kell igazítani.

2026-06-12 checkout tények:

- `stack-persistent.ts`: Key Vault, Log Analytics, subscription-level budget, jelenleg $20/hó project cap.
- `stack-platform.ts`: Azure DNS `duumbi.dev`, free Azure Static Web Apps a web/docs felületekhez, Slack approval bridge Function App.
- `stack-registry.ts`: registry Container App, Azure Files-backed `/data`, 1 GiB file share, Log Analytics, `registry.duumbi.dev`, scale-to-zero/min-0 deployment.
- Nincs jelenlegi Loop DB, graph DB, Service Bus vagy Loop Container App stack.

M6 infra döntés: Azure marad DNS/hosting/secrets/observability alap, de a Loop bevezető adatbázisa Neon Postgres legyen.

---

## 2. Termék célja

A DUUMBI Loop egy olyan fejlesztési cloud szolgáltatás, amely:

1. összeköti a felhasználó GitHub/GitLab repository-jait,
2. a kiválasztott repository-kból nyelvfüggetlen kódgráfot és tudásbázist épít,
3. issue/MR/PR komment parancsokból elindítja a DUUMBI workflow-kat,
4. AI provider/model policy alapján kutatást, elemzést, specifikációt, review-t és closure-t készít,
5. minden agent munkához forrásokra, diffekre, kódgráfra és teszteredményekre hivatkozó evidence artifactokat ment,
6. dashboardon keresztül visszakereshetővé, auditálhatóvá és csapatban megválaszolhatóvá teszi a folyamatot.

### Kulcs parancsok

A szolgáltatás ezeket a komment parancsokat figyeli:

```text
@duumbi intake
@duumbi spec
@duumbi review
@duumbi closure
```

A parancsokat GitHub issue-ban, GitHub PR-ben, GitLab issue-ban és GitLab merge requestben kell felismerni.

---

## 3. Milestone scope

### M6.0: repo- és infra-felderítés, terv rewrite

Codex:

- ellenőrizze a `duumbi-infra` Pulumi stack jelenlegi szerkezetét,
- ellenőrizze a `duumbi-registry` auth implementációját,
- ellenőrizze a `duumbi-web` design tokeneket, UI konvenciókat, docs struktúrát,
- írjon rövid `docs/discovery.md` fájlt a `duumbi-loop` repóba a megtalált tényekkel.
- írja át vagy supersede-elje ezt a tervet milestone-alapú v2 dokumentummal, MVP nyelvezet nélkül.

### M6.1: Account, tenant, billing shell

Kötelező:

- Rust workspace scaffold.
- Axum API.
- `duumbi-auth` külön repo/service.
- Email magic link.
- Stripe customer/subscription/entitlement alap.
- Org/team/RBAC.
- Session cookie.
- Neon Postgres metadata store.
- Dashboard shell.
- Settings shell: retention, billing, model/data policy.

### M6.2: Data és graph foundation

Kötelező:

- Async worker.
- Queue/event feldolgozás.
- Postgres-backed property graph tables.
- Graph store adapter interface.
- LLM provider/model catalog adapter interface.
- Raw repo snapshot retention policy és cleanup job.
- Audit log.
- Region-aware LLM data policy: EU, USA, Kína.

### M6.3: provider adapterek

Kötelező:

- DUUMBI-native provider elsődleges adapterként.
- GitHub App összekötés és repo-listázás.
- Repository kiválasztás indexingre.
- GitHub issue comment parancs felismerés.
- GitLab Cloud OAuth / app integráció.
- GitLab Self-Hosted kézi webhook setup guide + connection test + SSRF védelem.

### M6.4: cloud workflow execution

Kötelező:

- `@duumbi intake` issue-on.
- `@duumbi spec` issue-on.
- `@duumbi review` PR-en.
- `@duumbi closure` issue-on.
- Branch/PR permission failure handling: UI + email + Slack + manual spec bundle.
- Review default comment mode blocking issue esetén is.
- Closure default auto-publish knowledge update.

### M6.5: kódgráf és tudásbázis

Kötelező:

- kódbázis klónozás izolált workerben,
- `.gitignore` + `.duumbiignore` + alap kizárások,
- nyelvdetektálás,
- tree-sitter alapú szimbólum, import, call/reference extract,
- graph snapshot mentés,
- dashboard index státusz,
- graph query API intake/spec/review workflow-khoz.

Támogatott nyelvek első cloud milestone-okban:

```text
Rust
Golang
Node.js / JavaScript
TypeScript
TSX
Java
Python
HTML
CSS
```

### M6.6: docs, pricing, hardening

Kötelező:

- `docs.duumbi.dev/loop` szekció,
- marketing oldalak,
- pricing oldal Stripe default providerrel,
- security/data retention docs,
- audit log,
- rate limiting,
- provider/model beállítások,
- napi model catalog refresh.

---

## 4. Nem célok az első cloud milestone-okban

- Nem kell teljes automatikus kódimplementációt végezni. A `spec` PR a specifikációs artifactokat adja fel a repóba.
- Nem kell minden GitHub Enterprise Server és GitLab Self-Hosted edge case-t első körben lefedni; GitLab Self-Hostednél kézi webhook setup elég.
- Nem kell minden programnyelvhez teljes szemantikus compiler-indexing. Tree-sitter + opcionális SCIP adapter elég az első cloud milestone-okban.
- Nem kell user által nem engedélyezett repository-kat indexelni.
- Nem kerülhet LLM providerhez teljes repo dump; csak releváns, minimális, hivatkozott context mehet.

---

## 5. Javasolt monorepo szerkezet

```text
duumbi-loop/
  Cargo.toml
  README.md
  AGENTS.md
  .env.example
  docker-compose.yml
  migrations/
  crates/
    common/
    config/
    db/
    auth/
    api/
    webhook/
    worker/
    jobs/
    provider-core/
    provider-github/
    provider-gitlab/
    slack/
    llm-core/
    llm-openai/
    llm-anthropic/
    llm-xai/
    llm-deepseek/
    llm-minimax/
    llm-gemini/
    model-catalog/
    codegraph-core/
    codegraph-parser/
    codegraph-postgres/
    codegraph-neo4j/
    codegraph-kuzu/
    artifact/
    audit/
  apps/
    dashboard/
      package.json
      src/
      tests/
    marketing-placeholder/
      README.md
  docs/
    discovery.md
    architecture.md
    security.md
    local-dev.md
    api.md
    workflow-examples.md
```

### Rust crate felelősségek

| Crate | Felelősség |
|---|---|
| `common` | közös típusok, ID-k, idő, hibák, tenant context |
| `config` | env/config betöltés, secret reference modellek |
| `db` | sqlx pool, migráció helpers, repository layer |
| `auth` | OAuth/OIDC, session, identity linking, org/team RBAC |
| `api` | Axum REST API, OpenAPI export, dashboard backend |
| `webhook` | GitHub/GitLab/Slack webhook signature, enqueue |
| `worker` | parancsok, indexing, LLM runok, artifactok feldolgozása |
| `jobs` | scheduled feladatok, model catalog refresh, cleanup |
| `provider-core` | egységes code provider trait |
| `provider-github` | GitHub App installation, REST/GraphQL, comments, PRs |
| `provider-gitlab` | GitLab OAuth, REST, webhooks, notes, MRs |
| `slack` | Slack OAuth, webhook/event, notification |
| `llm-core` | provider/model absztrakció, routing, fallback, policy |
| `model-catalog` | napi model list szinkron, normalizálás, capability schema |
| `codegraph-core` | graph schema, snapshot, query DSL |
| `codegraph-parser` | tree-sitter, nyelvdetektálás, szimbólum extract |
| `codegraph-postgres` | első cloud milestone graph backend: Postgres property graph táblák Neonban |
| `codegraph-neo4j` | későbbi opcionális Neo4j adapter, ha mért query igény indokolja |
| `codegraph-kuzu` | opcionális snapshot/analytics adapter; ne legyen hard dependency az első cloud milestone-ban |
| `artifact` | intake/spec/review/closure artifact schema + Markdown render |
| `audit` | audit események, security trail |

---

## 6. Architektúra

### Magas szintű komponensek

```mermaid
flowchart LR
    User["User / Developer"] --> Marketing["loop.duumbi.dev"]
    User --> Dashboard["app.loop.duumbi.dev"]

    Dashboard --> API["api.loop.duumbi.dev<br/>Rust Axum API"]
    GitHub["GitHub / GitHub Enterprise"] --> Hooks["hooks.loop.duumbi.dev<br/>Webhook Receiver"]
    GitLab["GitLab Cloud / Self-Hosted"] --> Hooks
    Slack["Slack"] --> Hooks

    Hooks --> Queue["Queue<br/>commands + indexing + notifications"]
    API --> DB["Neon Postgres<br/>metadata + artifacts + graph tables"]
    API --> Graph["Graph adapter<br/>Postgres first, Neo4j/Kuzu later"]
    API --> Auth["auth.duumbi.dev<br/>DUUMBI SSO"]

    Queue --> Workers["Azure Container Apps Workers<br/>Rust tokio"]
    Workers --> DB
    Workers --> Graph
    Workers --> Blob["Artifact storage<br/>repo snapshots, artifacts"]
    Workers --> LLM["LLM Router<br/>provider:model policy"]
    Workers --> GitHub
    Workers --> GitLab
    Workers --> Slack

    Catalog["Daily Model Catalog Job"] --> LLM
    Catalog --> DB

    KV["Azure Key Vault"] --> API
    KV --> Hooks
    KV --> Workers
```

### Infra komponensek

A `duumbi-infra` Pulumi stackben Codex adjon hozzá vagy használjon újra meglévő komponenseket:

- Azure DNS records:
  - `loop.duumbi.dev`
  - `app.loop.duumbi.dev`
  - `api.loop.duumbi.dev`
  - `hooks.loop.duumbi.dev`
  - `auth.duumbi.dev`
- Azure Static Web Apps:
  - marketing, ha a `duumbi-web` stack ezt használja,
  - dashboard, ha a meglévő pattern ezt támogatja.
- Azure Container Apps:
  - `duumbi-loop-api`
  - `duumbi-loop-hooks`
  - `duumbi-loop-worker`
  - `duumbi-loop-scheduler`
- Azure Container Apps Jobs:
  - `duumbi-loop-indexer-job`
  - `duumbi-loop-model-catalog-refresh`
  - `duumbi-loop-cleanup`
- Queue/event feldolgozás:
  - `loop.commands`
  - `loop.indexing`
  - `loop.notifications`
  - `loop.model_catalog`
  - DLQ minden queue-hoz.
- Neon Postgres:
  - metadata,
  - artifact metadata,
  - Postgres-backed graph tables,
  - connection string Key Vault / secret reference-ből.
- Graph DB:
  - első cloud milestone: Postgres property graph tables, `codegraph-postgres` adapterrel.
  - későbbi opcionális adapter: Neo4j, ha query workload indokolja.
  - későbbi opcionális snapshot/analytics adapter: Kuzu, csak ha az upstream projektirány és fenntarthatóság tisztázott.
- Artifact/source storage:
  - repo working snapshots,
  - graph exportok,
  - generated infographic SVG/JSON,
  - artifact Markdown/JSON.
- Azure Key Vault:
  - OAuth client secretek,
  - GitHub App private key,
  - webhook secretek,
  - Slack signing secret,
  - LLM provider API kulcsok,
  - database connection string secret reference,
  - token envelope encryption kulcs.
- Log Analytics + Application Insights / OpenTelemetry collector.
- Budgets / alerts a worker és LLM költéshez.

### Adatbázis és graph backend döntés

Default: **Neon Postgres**.

Indoklás:

- A bevezető időszakban ingyenes indulás és kontrollált usage-based növekedés kell.
- Neon Free plan jelenleg 100 CU-hour / projekt / hónap és 0.5 GB storage / projekt.
- Supabase viable alternatíva, de az integrált Auth/Storage csomag kevésbé fontos, mert auth külön `duumbi-auth`, billing pedig Stripe.
- Azure Database for PostgreSQL ne legyen default, mert az bevezetéskor fizetős infrafüggőséget vinne a rendszerbe.

Graph backend default:

- Postgres-backed property graph tables ugyanabban a Neon adatbázisban.
- `codegraph-core` adapter boundary mögött tartja a query DSL-t.
- Neo4j és Kuzu későbbi adapterként maradjon; ne legyenek induló hard dependency-k.

### Container App secret kezelés

Codex használjon Managed Identity-t Key Vault secret reference-ekhez. Ne másoljon hosszú életű secretet Pulumi configba, GitHub Actions secretbe vagy frontend envbe.

---

## 7. SSO és account modell

### Cél

A felhasználó egy DUUMBI regisztrációval férjen hozzá:

- `registry.duumbi.dev`
- `loop.duumbi.dev`
- `app.loop.duumbi.dev`
- későbbi DUUMBI szolgáltatások.

### Javasolt auth stratégia

Hozz létre központi DUUMBI identity issuert:

```text
https://auth.duumbi.dev
```

Ez külön `hgahub/duumbi-auth` repo/service legyen az első cloud milestone-tól. Ne induljon a `duumbi-loop` workspace belső crate-jeként, mert a registry, Loop, cloud app és későbbi felületek közös issuerre támaszkodnak.

### Támogatott belépési módok

- GitHub OAuth
- GitLab OAuth
- X OAuth 2.0 Authorization Code + PKCE
- Google OpenID Connect
- Email magic link

### Központi account fogalmak

```text
User
Identity
Organization
Team
TeamMembership
Session
ApiToken
ServiceAccount
```

Egy `User` több `Identity` rekordot linkelhet:

```text
github:123456
gitlab:7890
google:sub
x:user_id
email:alice@example.com
```

### Session és token

- Web session cookie domain: `.duumbi.dev`
- Cookie:
  - `HttpOnly`
  - `Secure`
  - `SameSite=Lax` vagy ahol kell `None`
  - rotálható session id
- API token:
  - hash-elve tárolva,
  - raw token csak egyszer látszik,
  - scope-okkal korlátozva.
- Registry integráció:
  - a meglévő registry auth ne maradjon külön user-sziget,
  - registry legyen OIDC client vagy trustolja az `auth.duumbi.dev` issuer JWT/opaque introspection szerződését,
  - meglévő registry API tokeneket migrációs módban meg kell őrizni.

### RBAC

Org szintű szerepek:

```text
owner
admin
developer
reviewer
billing_admin
viewer
```

Képességek:

| Capability | owner | admin | developer | reviewer | billing_admin | viewer |
|---|---:|---:|---:|---:|---:|---:|
| provider connect | yes | yes | no | no | no | no |
| repo enable/disable | yes | yes | no | no | no | no |
| model policy edit | yes | yes | no | no | no | no |
| run intake/spec | yes | yes | yes | no | no | no |
| run review | yes | yes | yes | yes | no | no |
| answer questions | yes | yes | yes | yes | no | no |
| billing settings | yes | no | no | no | yes | no |
| view artifacts | yes | yes | yes | yes | no | yes |

---

## 8. Code provider integráció

### Dashboard UX a csatolt screenshotok alapján

Készíts hasonló dark UI mintát a DUUMBI designhoz igazítva:

Top navigation javaslat:

```text
Analytics
Repositories
Intake & Specs
Reviews
Knowledge Graph
Code Providers
Model Settings
Integrations
Organization Settings
```

`Code Providers` oldal:

- Provider cardok:
  - GitHub
  - GitHub Enterprise Server
  - GitLab Cloud
  - GitLab Self-Hosted
- `+ Add Provider` dropdown.
- Last sync kijelzés.
- Csatolt organization/group szám.
- Provider státusz:
  - Connected
  - Needs reconnect
  - Syncing
  - Error
  - Revoked

`Repositories` oldal:

- Search.
- Multi-select.
- Repo oszlop.
- Provider oszlop.
- Status:
  - Enabled
  - Disabled
  - Indexing
  - Index failed
  - Needs permissions
- Graph snapshot.
- Last indexed.
- Last 7 days runs.
- Avg. time to intake/spec/review.
- Enable/disable gomb.
- Reindex gomb.
- View graph gomb.

### GitHub integráció

Az első GitHub adapter milestone-ban GitHub App legyen az alap, nem sima user PAT.

GitHub App permission tervezet:

```text
Metadata: read
Contents: read/write
Issues: read/write
Pull requests: read/write
Checks: read
Commit statuses: read
Members: read
Actions: read
```

Webhook eventek:

```text
installation
installation_repositories
repository
push
issues
issue_comment
pull_request
pull_request_review
pull_request_review_comment
check_run
check_suite
```

Megjegyzés: PR conversation comment GitHubon tipikusan `issue_comment` eventként is érkezhet, mert a PR issue-ként is működik. Inline PR review kommenthez `pull_request_review_comment` is kell.

GitHub feladatok:

- Installation callback kezelése.
- Installation access token generálása.
- Organization/repository sync.
- Repo permission ellenőrzés.
- Repo klónozás installation tokennel.
- Issue/PR komment olvasás.
- 👀 reaction küldés.
- Válasz komment küldés.
- Branch létrehozás.
- Spec file commit.
- PR létrehozás.
- PR review commentek küldése.

Ha branch/PR létrehozást branch protection vagy repo permission tiltja:

- a run állapota legyen `needs_action`,
- dashboard banner mutassa a pontos blokkolt műveletet,
- email menjen org adminoknak / repo owneröknek,
- Slack értesítés menjen, ha Slack integráció be van kötve,
- készüljön letölthető / manuálisan alkalmazható spec bundle fallback.

### GitLab integráció

GitLab Cloud és Self-Hosted támogasson:

- OAuth login/linking.
- Group/project listázás.
- Project webhook regisztráció vagy setup guide.
- Issue és MR note eventek.
- Merge request diff olvasás.
- Note/komment küldés.
- Branch + MR létrehozás.

GitLab Self-Hosted első cloud milestone:

- kézi webhook setup elég,
- UI generáljon webhook URL-t, secretet, event checklistet és setup guide-ot,
- legyen connection test,
- automatikus project webhook regisztráció későbbi milestone.

Self-hosted GitLabnál kötelező SSRF védelem:

- Base URL csak `https://`.
- Ne engedjen:
  - `localhost`
  - loopback IP
  - link-local IP
  - private IP tartomány
  - metadata endpoint
  - nem publikus DNS resolution
- Admin jóváhagyás nagyobb orgoknál.
- Connection test timeout és rate limit.

---

## 9. Repository kiválasztás és indexing

### Repo selection flow

1. Admin összeköti GitHub/GitLab providert.
2. Worker leszinkronizálja org/group repo listát.
3. Dashboard `Repositories` oldalon admin kijelöli az elemzendő repo-kat.
4. A rendszer enqueue-ol egy `IndexRepository` jobot.
5. Worker izolált temp workspace-ben klónoz.
6. Parser kinyeri a graph snapshotot.
7. Graph DB-be és artifact storage-ba ment.
8. Dashboardon megjelenik:
   - index status,
   - graph snapshot version,
   - nyelvek,
   - fájl/szimbólum/call edge szám,
   - utolsó hiba, ha volt.

### Alap kizárások

Mindig hagyd ki:

```text
.git/
node_modules/
target/
dist/
build/
.next/
.nuxt/
coverage/
vendor/
.venv/
venv/
__pycache__/
*.min.js
*.lock
*.png
*.jpg
*.jpeg
*.gif
*.pdf
*.zip
*.tar
*.gz
```

Támogasd:

```text
.duumbiignore
.cgcignore
.gitignore
```

### Kódgráf schema

Node típusok:

```text
Repository
Commit
Branch
Directory
File
Package
Module
Class
Interface
Trait
Struct
Enum
Function
Method
Variable
Import
Export
Route
Endpoint
Config
Test
Spec
Issue
PullRequest
KnowledgeEntry
```

Edge típusok:

```text
CONTAINS
DEFINES
IMPORTS
EXPORTS
CALLS
REFERENCES
IMPLEMENTS
EXTENDS
TESTS
ROUTES_TO
CONFIGURES
CHANGED_BY
RELATED_TO
SATISFIES
VIOLATES
MENTIONS
```

### Graph snapshot

Minden indexelés immutable snapshot legyen:

```text
graph_snapshot_id
repository_id
provider
repo_full_name
commit_sha
branch
indexed_at
parser_version
schema_version
language_summary
node_count
edge_count
storage_uri
status
```

### Graph query API első cloud milestone

```http
GET /api/repositories/{id}/graph/snapshots
GET /api/repositories/{id}/graph/summary
POST /api/repositories/{id}/graph/query
GET /api/repositories/{id}/symbols?query=
GET /api/repositories/{id}/files?query=
```

A graph query ne engedjen raw Cypher/Cypher injectiont a dashboardból. Legyen kontrollált query DSL.

---

## 10. Command parser

### Elfogadott parancsok

```text
@duumbi intake
@duumbi spec
@duumbi review
@duumbi closure
```

Case-insensitive legyen:

```text
@Duumbi Intake
@DUUMBI REVIEW
```

Támogasson opcionális paramétereket később:

```text
@duumbi intake --model openai:gpt-...
@duumbi spec --depth deep
@duumbi review --strict
@duumbi closure --update-knowledge
```

Az első cloud milestone-ban a paramétereket parse-old, de csak validáld és tárold; ha nem támogatott, kulturáltan válaszolj.

### Ne fusson parancs, ha

- komment quote-ban szerepel,
- code blockban szerepel,
- nem jogosult user írta,
- repo nincs enable-elve,
- provider installation revoked,
- már fut ugyanarra az issue/PR-re ugyanaz a command,
- a command egy bot által generált kommentben szerepel, kivéve explicit allowlist.

### Idempotencia

Minden commandhoz képezz deterministic idempotency keyt:

```text
provider + installation_id + repo_id + issue_or_pr_id + comment_id + command
```

---

## 11. Workflow state machine

Állapotok:

```text
created
acknowledged
queued
running
needs_input
needs_action
completed
failed
cancelled
superseded
```

`needs_input` = a run csapat-válaszra vár (nyitott kérdés-topicok). `needs_action` = a run külső jogosultsági vagy konfigurációs beavatkozásra vár (pl. branch protection blokkolja a spec PR-t, hiányzó provider permission).

Általános event flow:

```mermaid
sequenceDiagram
    participant U as User
    participant CP as Code Provider
    participant H as Loop Webhook
    participant Q as Queue
    participant W as Worker
    participant DB as Neon Postgres
    participant G as Graph Adapter
    participant L as LLM Router
    participant S as Slack

    U->>CP: @duumbi intake/spec/review/closure
    CP->>H: webhook event
    H->>H: signature + command parse + idempotency
    H->>Q: enqueue command
    H-->>CP: 2xx quickly
    W->>CP: add 👀 reaction
    W->>DB: create run + audit event
    W->>G: load relevant graph context
    W->>L: run provider:model policy
    W->>DB: save artifact
    W->>CP: post result link/comment
    W->>S: notify if enabled
```

### Performance célok

- Webhook HTTP 2xx: p95 < 500 ms signature validation és enqueue után.
- 👀 reakció: p95 < 5 s.
- Dashboard metadata API: p95 < 200 ms.
- Graph query egyszerű kérdésekre: p95 < 500 ms.
- Intake artifact közepes repo issue-ra: cél < 3 perc.
- Indexing:
  - inkrementális push után: cél < 2 perc közepes repo-n,
  - cold index: queue-zott háttérfeladat, progress reportinggal.

---

## 12. `@duumbi intake`

### Trigger

Felhasználó kommentet ír issue-ba:

```text
@duumbi intake
```

### Viselkedés

1. Rendszer észleli.
2. Reagál 👀 emojival.
3. Ellenőrzi jogosultságot és repo enable státuszt.
4. Betölti:
   - issue title/body/comments,
   - labels/milestone/assignees,
   - repo graph snapshot,
   - releváns fájlok/szimbólumok,
   - hasonló korábbi issue/spec/review artifactok,
   - docs/custom context,
   - ha van, Slack vagy csapat tudásbázis kivonat.
5. Elemzést készít.

### Intake artifact kötelező részei

Structured JSON + Markdown render:

```text
title
source_issue
status
executive_summary
problem_statement
business_context
user_impact
likely_affected_areas
code_references
similar_examples
research_notes
infographic
confidence_score
business_value_score
effort_estimate
time_estimate
risk_assessment
recommended_provider_model
clarifying_question_topics
next_actions
sources
```

### Pontszámok

`confidence_score`:

```text
0.0 - 1.0
```

`business_value_score`:

```text
low | medium | high | critical
```

`effort_estimate`:

```text
XS | S | M | L | XL
```

`time_estimate`:

```text
human_readable: "0.5-1 day"
hours_min
hours_max
```

### Infografika

Az első cloud milestone-ban ne generálj raster képet LLM-mel. Készíts:

- Mermaid diagramot,
- graph neighborhood JSON-t,
- opcionális dashboard SVG renderelést.

Példa:

```mermaid
flowchart LR
    Issue["Issue intent"] --> Areas["Affected areas"]
    Areas --> Files["Files / symbols"]
    Files --> Risks["Risks"]
    Risks --> Questions["Clarifying questions"]
    Questions --> Spec["@duumbi spec"]
```

### Tisztázandó kérdések

A kérdéseket topicokra rendezve kell menteni:

```text
QuestionTopic
  id
  run_id
  title
  status: open|answered|resolved
  questions[]
  messages[]
```

Dashboardon a csapattagok válaszolhatnak. A válaszok később a `spec` workflow inputjai lesznek.

### Visszajelzés issue-ba

Példa komment:

```markdown
👀 DUUMBI Loop elkészült az intake elemzéssel.

- Confidence: 0.74
- Business value: High
- Effort: M, kb. 1-2 nap
- Tisztázandó kérdések: 3 topic

Elemzés: https://app.loop.duumbi.dev/o/{org}/runs/{run_id}

Következő lépés:
`@duumbi spec`
```

---

## 13. `@duumbi spec`

### Trigger

Felhasználó kommentet ír issue-ba:

```text
@duumbi spec
```

### Előfeltétel

- Legyen ugyanahhoz az issue-hoz completed vagy needs_input állapotú intake run.
- Ha kritikus kérdés nyitott, a rendszer figyelmeztessen és csak admin/developer override-dal folytassa.

### Viselkedés

1. Reagál 👀 emojival.
2. Betölti:
   - issue,
   - intake artifact,
   - megválaszolt kérdések,
   - code graph,
   - releváns fájlok,
   - hasonló példák,
   - repo conventions.
3. Elkészíti:
   - üzleti specifikáció,
   - technikai specifikáció,
   - BDD/Gherkin tesztesetek,
   - agent implementation plan,
   - validation checklist.
4. Új branch-et hoz létre.
5. Spec file-okat commitol.
6. PR-t nyit a repóba.
7. Issue-ba visszakommenteli a PR linket.

### Spec file célhely

Repository-n belül:

```text
.duumbi/specs/{issue_number}-{slug}/business.md
.duumbi/specs/{issue_number}-{slug}/technical.md
.duumbi/specs/{issue_number}-{slug}/bdd.feature
.duumbi/specs/{issue_number}-{slug}/agent-plan.md
.duumbi/specs/{issue_number}-{slug}/metadata.json
```

### Business spec tartalom

```text
Context
User problem
Business value
In scope
Out of scope
Assumptions
Acceptance criteria
BDD scenarios summary
Risk and rollout notes
```

### Technical spec tartalom

```text
Architecture impact
Affected modules/files/symbols
Data model/API changes
Migration plan
Implementation steps
Test strategy
Observability
Security/privacy considerations
Rollback plan
Open questions
```

### BDD

Gherkin formátum:

```gherkin
Feature: ...

  Scenario: ...
    Given ...
    When ...
    Then ...
```

### PR cím/body

```text
DUUMBI spec: #{issue_number} {issue_title}
```

PR body:

```markdown
This PR contains DUUMBI-generated specification artifacts.

Source issue: ...
Intake analysis: ...
Generated by: DUUMBI Loop run ...
Validation checklist:
- [ ] Business acceptance criteria reviewed
- [ ] Technical plan reviewed
- [ ] BDD scenarios accepted
```

---

## 14. `@duumbi review`

### Trigger

Felhasználó kommentet ír PR-be/MR-be:

```text
@duumbi review
```

### Előfeltétel

- Repo enabled.
- PR/MR diff olvasható.
- Ha van `.duumbi/specs/...`, azt be kell tölteni.
- Ha a PR egy DUUMBI specből indult, linkeld a review-t az intake/spec runhoz.

### Review cél

Greptile-szintű, kódközpontú, de DUUMBI-specifikus review:

- ellenőrzi a diffet,
- ellenőrzi a specifikáció teljesülését,
- ellenőrzi a teszt/BBD lefedettséget,
- jelzi a security, performance, maintainability kockázatokat,
- inline kommentel, ahol van pontos kódsor,
- összefoglalót küld a PR-be.

### Review artifact schema

```text
summary
overall_status: approved|changes_requested|commented
spec_compliance
blocking_issues[]
warnings[]
nits[]
positive_findings[]
test_evidence
missing_tests
security_notes
performance_notes
knowledge_update_candidates
inline_comments[]
sources
```

### Severity

```text
blocking
warning
nit
info
```

### Review strictness

Default: **comment mode**.

Blocking issue esetén is `commented` legyen az alap provider-native review state. A blocking issue-t a summaryban és inline kommentben egyértelműen jelölni kell, de ne legyen automatikus `changes_requested`.

Strict mode org/repo settingként kapcsolható be. Strict mode esetén blocking issue provider-native `changes_requested` review-t eredményezhet.

### PR komment minta

```markdown
## DUUMBI Review

Overall: Commented — blocking issues found

### Blocking
1. Spec AC-3 is not satisfied: ...

### Warnings
1. ...

### Tests
- Existing tests touched: ...
- Missing BDD coverage: ...

Dashboard artifact:
https://app.loop.duumbi.dev/o/{org}/runs/{run_id}
```

### Inline komment szabály

- Max 20 inline komment alapértelmezetten.
- Ugyanarra a problémára ne küldj ismétlődő kommenteket.
- Ha a diff túl nagy, summary-first review legyen, majd dashboardon teljes részletezés.
- Ne írj "LGTM" jellegű review-t bizonyíték nélkül.

---

## 15. `@duumbi closure`

### Trigger

Felhasználó kommentet ír issue-ba:

```text
@duumbi closure
```

### Előfeltétel

- Legyen kapcsolódó intake/spec/review workflow.
- Kapcsolódó PR/MR merged vagy explicit override.
- Review lezárva vagy elfogadva.

### Viselkedés

1. Reagál 👀 emojival.
2. Betölti:
   - issue,
   - intake,
   - spec,
   - PR/MR diff,
   - review,
   - merge commit,
   - CI állapot,
   - korábbi graph snapshot.
3. Lefuttat inkrementális indexelést a merge commitra.
4. Összehasonlítja a régi és új graph snapshotot.
5. Tudásfrissítési javaslatokat készít.
6. Frissíti a DUUMBI tudástárat:
   - graph snapshot,
   - durable knowledge entries,
   - spec-to-code linkek,
   - issue/PR artifact linkek.
7. Visszajelez issue-ba és Slackre, ha be van kötve.

### Knowledge update policy

Két mód legyen:

```text
auto_candidate
auto_publish
```

Alapértelmezés: `auto_publish`.

`auto_publish` esetén closure automatikusan publikálja a végleges DUUMBI tudástár-frissítést:

- graph snapshot,
- durable `KnowledgeEntry`,
- spec-to-code linkek,
- issue/PR artifact linkek,
- closure artifact.

`auto_candidate` esetén a rendszer nem publikál végleges csapat-tudást automatikusan, hanem:

- létrehoz `KnowledgeEntry` candidate rekordokat,
- dashboardon admin/developer jóváhagyhatja,
- graph snapshotot ettől függetlenül frissíti.

`auto_candidate` regulated / conservative org settingként választható.

### Closure komment minta

```markdown
✅ DUUMBI Loop lezárta a fejlesztési ciklust.

- Merged PR: ...
- Graph snapshot updated: ...
- Published knowledge updates: 4
- Spec compliance: 92%
- Follow-up items: 2

Closure artifact:
https://app.loop.duumbi.dev/o/{org}/runs/{run_id}
```

---

## 16. LLM provider és model catalog

### Támogatott providerek

- Anthropic
- OpenAI
- xAI
- DeepSeek
- MiniMax
- Gemini

### Kötelező követelmény

Ne hardcode-olj aktuális model listát production UI-hoz. A rendszer naponta egyszer frissítse a model catalogot provider API-ból vagy hivatalos model endpointból.

### Provider endpoint források, amelyeket Codex használjon

- OpenAI: `GET /models`
  - docs: `https://developers.openai.com/api/reference/resources/models/methods/list/`
- Anthropic: Models API list
  - docs: `https://platform.claude.com/docs/en/api/models/list`
- xAI:
  - `GET /v1/models`
  - `GET /v1/language-models`
  - docs: `https://docs.x.ai/developers/rest-api-reference/inference/models`
- DeepSeek:
  - `GET /models`
  - docs: `https://api-docs.deepseek.com/api/list-models`
- Gemini:
  - models endpoint
  - docs: `https://ai.google.dev/api/models`
- MiniMax:
  - OpenAI-compatible: `GET /v1/models`
  - Anthropic-compatible: `GET /anthropic/v1/models`
  - docs:
    - `https://platform.minimax.io/docs/api-reference/models/openai/list-models`
    - `https://platform.minimax.io/docs/api-reference/models/anthropic/list-models`

### Normalizált model schema

```json
{
  "provider": "openai",
  "model_id": "string",
  "display_name": "string",
  "status": "available",
  "aliases": [],
  "modalities": ["text"],
  "capabilities": {
    "tool_use": false,
    "json_schema": false,
    "vision": false,
    "code": false,
    "reasoning": false,
    "streaming": false,
    "embeddings": false
  },
  "context_window": null,
  "max_output_tokens": null,
  "pricing": {
    "input_per_1m": null,
    "output_per_1m": null,
    "currency": "USD"
  },
  "region_availability": [],
  "source": "provider_api",
  "fetched_at": "timestamp",
  "deprecated_at": null
}
```

### Model policy

Org admin dashboardon állítható:

```text
allowed_providers
blocked_providers
default_intake_model
default_spec_model
default_review_model
default_closure_model
default_embedding_model
fallback_chain
max_cost_per_run
max_context_tokens
data_residency_preference
```

### LLM data policy és region buckets

Kötelező első régiók:

```text
EU
USA
China
```

Org admin állíthassa:

- provider/model allowlist régiónként,
- provider/model blocklist régiónként,
- cross-region fallback engedélyezése vagy tiltása,
- prompt retention,
- response retention,
- code snippet retention,
- max context tokens,
- régióhoz kötött default model policy intake/spec/review/closure taskokra.

Context builder szabály: teljes repo dump nem mehet LLM providerhez. Csak minimális, releváns, forrásolt context küldhető.

### Routing stratégia

Az `llm-core` ne konkrét modellre, hanem capability-re kérjen modellt:

```text
task=intake
needs=reasoning+code+json_schema+long_context
max_cost=...
provider_policy=...
```

A router ez alapján választ:

```text
provider:model
```

Ha a kiválasztott modell hibázik:

1. retry ugyanazzal exponential backoffal,
2. fallback ugyanazon provideren belül,
3. fallback másik engedélyezett providerre,
4. run állapot `failed` részletes hibával.

### Napi refresh

- Azure Container Apps Job vagy scheduler napi egyszer.
- Queue: `loop.model_catalog`.
- Providerenként külön timeout.
- Sikertelen provider ne törölje az előző catalogot.
- UI jelezze:
  - `last_checked_at`
  - `last_success_at`
  - `last_error`
  - `available_models_count`

---

## 17. Adatmodell

### Neon Postgres fő táblák

```sql
users
identities
organizations
teams
team_memberships
sessions
api_tokens

code_providers
provider_accounts
provider_installations
provider_webhooks
repositories
repository_permissions
repository_selections
repository_sync_runs

graph_snapshots
graph_index_jobs
graph_language_summaries

duumbi_runs
run_events
run_artifacts
run_sources
run_costs

question_topics
question_messages

spec_artifacts
review_artifacts
closure_artifacts
knowledge_entries
knowledge_entry_sources

model_providers
model_catalog_entries
model_refresh_runs
model_policies
provider_credentials

slack_installations
slack_notifications

audit_events
idempotency_keys
webhook_events

billing_customers
billing_subscriptions
billing_entitlements
billing_events
usage_meter_events

retention_policies
source_snapshots
source_snapshot_deletion_jobs
```

### Fontos indexek

```sql
idx_repositories_org_provider_full_name
idx_runs_org_repo_source_type_status
idx_runs_issue_pr_command
idx_idempotency_key_unique
idx_webhook_delivery_id_unique
idx_graph_snapshots_repo_commit
idx_model_catalog_provider_model
idx_audit_org_created_at
idx_billing_events_provider_event_id
idx_source_snapshots_org_repo_expires_at
```

### Tenant isolation

Minden org-szintű táblában legyen:

```sql
organization_id UUID NOT NULL
```

Minden query explicit tenant scope-pal fusson. Ne legyen olyan repository layer metódus, amely org nélkül query-zik org adatot.

---

## 18. API tervezet

### Auth

```http
GET  /auth/{provider}/start
GET  /auth/{provider}/callback
POST /auth/email/start
POST /auth/email/verify
POST /auth/logout
GET  /api/me
GET  /api/orgs
POST /api/orgs
```

### Code providers

```http
GET  /api/code-providers
POST /api/code-providers/github/install/start
GET  /api/code-providers/github/install/callback
POST /api/code-providers/gitlab/connect
POST /api/code-providers/gitlab-self-hosted/connect
POST /api/code-providers/{id}/sync
DELETE /api/code-providers/{id}
```

### Repositories

```http
GET   /api/repositories
PATCH /api/repositories/{id}
POST  /api/repositories/{id}/enable
POST  /api/repositories/{id}/disable
POST  /api/repositories/{id}/reindex
GET   /api/repositories/{id}/graph/summary
GET   /api/repositories/{id}/graph/snapshots
POST  /api/repositories/{id}/graph/query
```

### Runs és artifactok

```http
GET  /api/runs
GET  /api/runs/{id}
GET  /api/runs/{id}/artifact
GET  /api/runs/{id}/events
POST /api/runs/{id}/cancel
```

### Kérdések

```http
GET  /api/runs/{id}/question-topics
POST /api/question-topics/{id}/messages
POST /api/question-topics/{id}/resolve
```

### Model settings

```http
GET  /api/models/providers
GET  /api/models/catalog
POST /api/models/refresh
GET  /api/model-policy
PUT  /api/model-policy
GET  /api/model-policy/regions
PUT  /api/model-policy/regions
```

### Billing

```http
GET  /api/billing/customer
GET  /api/billing/subscription
POST /api/billing/checkout-session
POST /api/billing/portal-session
POST /webhooks/stripe
```

Default billing provider: Stripe. Stripe webhook feldolgozás legyen idempotens és tenant-scoped.

### Retention settings

```http
GET  /api/retention-policy
PUT  /api/retention-policy
GET  /api/repositories/{id}/retention-policy
PUT  /api/repositories/{id}/retention-policy
```

Raw repo snapshot retention opciók: `0`, `7`, `14`, `30`, `90` nap. Default: `7` nap.

### Integrations

```http
GET  /api/integrations/slack
POST /api/integrations/slack/install/start
GET  /api/integrations/slack/install/callback
DELETE /api/integrations/slack
```

### Webhooks

```http
POST /webhooks/github
POST /webhooks/gitlab
POST /webhooks/slack
```

Minden API-hoz generálj OpenAPI dokumentációt `utoipa` vagy hasonló Rust toolinggal.

---

## 19. Frontend és design

### Technológia

Codex először nézze meg a `duumbi-web` jelenlegi stackjét. A dashboard technológiája ehhez igazodjon.

Ha nincs erős meglévő frontend döntés:

- Dashboard:
  - React + Vite + TypeScript
  - TanStack Router
  - TanStack Query
  - Zod
  - shadcn/ui vagy saját DUUMBI komponens layer
- Marketing/docs:
  - a `duumbi-web` aktuális docs/blog rendszerében.

### Design elvek

- Sötét alap, a csatolt screenshotokhoz hasonló admin dashboard sűrűség.
- DUUMBI zöld brand accent.
- Erős státuszjelzések.
- Táblák gyors kereséssel, filterrel, bulk actionnel.
- Minden AI runnál látszódjon:
  - status,
  - provider:model,
  - duration,
  - cost estimate,
  - confidence,
  - sources,
  - audit trail.

### Oldalak

Publikus `loop.duumbi.dev`:

```text
/
  Hero
  Features
  How it works
  Code graph knowledge base
  Intake/spec/review/closure workflow
  Security
  Pricing preview
  CTA to dashboard

/features
/pricing
/blog
/dashboard -> redirect app.loop.duumbi.dev
```

Dashboard `app.loop.duumbi.dev`:

```text
/login
/o/:org/analytics
/o/:org/repositories
/o/:org/code-providers
/o/:org/runs
/o/:org/runs/:runId
/o/:org/questions
/o/:org/knowledge
/o/:org/model-settings
/o/:org/integrations
/o/:org/settings
```

### Dashboard run detail

Run detail oldal menü:

```text
Overview
Sources
Code references
Infographic
Questions
Business value
Effort
Provider/model
Cost
Timeline
Raw artifact
```

### Graph visualization

Első cloud milestone:

- Graph summary card.
- Fájl/szimbólum keresés.
- Mini neighborhood visualization intake artifactban.
- Mermaid fallback.

Később:

- Interaktív graph explorer Cytoscape.js / Sigma.js alapon.

---

## 20. Dokumentáció

A dokumentáció a `docs.duumbi.dev` része legyen. Codex a `duumbi-web` docs struktúrája alapján hozzon létre új szekciót:

```text
docs/loop/
  index.md
  quickstart.md
  github.md
  gitlab.md
  commands.md
  intake.md
  spec.md
  review.md
  closure.md
  code-graph.md
  model-settings.md
  slack.md
  security.md
  data-retention.md
  troubleshooting.md
```

### Docs tartalmi minimum

- Mi az a DUUMBI Loop?
- GitHub App telepítés.
- GitLab Cloud/Self-Hosted bekötés.
- Repository kiválasztás.
- `@duumbi intake` használata.
- `@duumbi spec` használata.
- `@duumbi review` használata.
- `@duumbi closure` használata.
- AI provider/model beállítás.
- Adatbiztonság és source code handling.
- Slack integráció.
- FAQ.

---

## 21. Slack integráció

### Scope

- Értesítés intake/spec/review/closure elkészültéről.
- Link dashboard artifactra.
- Kérdés topicok Slack threadbe küldése opcionálisan.
- Slack ne legyen durable source of truth; a végleges artifact a dashboard + DB + code provider komment.

### Events

- Slack OAuth install.
- Org/channel mapping.
- Notification preferences:
  - all runs,
  - failed only,
  - needs input,
  - closure summary.

---

## 22. Security és privacy

### Kötelező

- Webhook signature validation.
- Idempotency minden webhook eventre.
- RBAC minden API endpointon.
- Tenant scoped DB queries.
- Secret redaction logokban.
- Provider tokenek envelope encryptionnel.
- Key Vault secret storage.
- LLM prompt/response retention org policy szerint.
- Region-aware LLM data policy: EU, USA, China.
- Code snippets minimization.
- Raw repo snapshot retention policy: default 7 nap, org/repo settinggel állítható.
- Source snapshot cleanup job és deletion audit event kötelező.
- Audit events:
  - login,
  - provider connect,
  - repo enable,
  - model policy change,
  - command received,
  - run started/completed/failed,
  - source snapshot retained/deleted,
  - retention policy changed,
  - Stripe billing event processed,
  - secret rotation,
  - registry auth migration events.

### LLM privacy

- Ne küldj teljes repót LLM-hez.
- Context builder csak releváns kódrészletet küldjön.
- Artifactban forráslink legyen:
  - file path,
  - commit SHA,
  - line range,
  - graph snapshot id.
- UI mutassa, mely provider:model dolgozott.
- Org admin állíthassa:
  - allowed providers,
  - forbidden providers,
  - region-specific allowed/forbidden providers,
  - cross-region fallback,
  - max context,
  - prompt retention,
  - response retention,
  - code snippet retention.

### Sandbox

Indexer worker:

- izolált temp directory,
- nem futtat build scriptet defaultból,
- nem futtat arbitrary package installt,
- nem exfiltrál env secretet,
- clone token csak scoped és rövid életű,
- cleanup minden job végén.

---

## 23. Observability

### Structured logs

Használj `tracing` crate-et és JSON logot productionben.

Kötelező mezők:

```text
request_id
organization_id
user_id
repository_id
provider
run_id
command
job_id
trace_id
span_id
```

### Metrics

```text
webhook_received_total
webhook_signature_failed_total
command_detected_total
command_unauthorized_total
run_started_total
run_completed_total
run_failed_total
run_duration_seconds
llm_request_total
llm_error_total
llm_cost_estimate
model_catalog_refresh_total
index_duration_seconds
graph_nodes_total
graph_edges_total
queue_lag_seconds
```

### Alerts

- Webhook failure rate.
- Queue lag.
- Worker crash loop.
- Model catalog all providers failed.
- LLM cost budget threshold.
- DB connection exhaustion.
- Graph DB unavailable.
- Key Vault access denied.

---

## 24. Hiba- és edge case kezelés

### Code provider edge cases

- Installation revoked.
- Repo renamed.
- Repo archived.
- Repo deleted.
- Repo transferred.
- Permission changed.
- Rate limit.
- Fork PR read-only.
- Protected branch blocks spec PR.
- GitHub/GitLab outage.

### Command edge cases

- Duplicate webhook delivery.
- Out-of-order events.
- Command edited after processing.
- Bot command loop.
- Unauthorized user command.
- Repo disabled during run.
- Open questions unresolved.
- Existing run in progress.
- Large issue with too many comments.

### Indexer edge cases

- Monorepo túl nagy.
- Binary/generated files.
- Unsupported language.
- Syntax errors.
- Partial checkout failure.
- Submodules.
- Git LFS.
- Vendored code.
- Secrets accidentally present in source.

### LLM edge cases

- Provider timeout.
- Model retired between refreshes.
- API key invalid.
- Context too large.
- JSON parse fail.
- Tool call fail.
- Cost cap exceeded.
- Safety refusal.
- Inconsistent output.

---

## 25. Testing stratégia

### Rust

```bash
cargo fmt --check
cargo clippy --all-targets --all-features -- -D warnings
cargo test --all-features
```

Ha elérhető:

```bash
cargo nextest run
```

### Unit tesztek

- Command parser.
- Webhook signature validation.
- Idempotency key.
- RBAC permission matrix.
- Model catalog normalizer.
- Provider:model router.
- Graph schema builder.
- Artifact Markdown renderer.
- GitHub/GitLab event mapping.

### Integration tesztek

- PostgreSQL migrations.
- Queue/event abstraction local fake-kel.
- GitHub API mock.
- GitLab API mock.
- LLM provider mock.
- Graph DB adapter mock.
- Slack mock.

### E2E teszt repo

Hozz létre `test-fixtures/repos/` alatt kis mintarepókat:

```text
rust-service/
go-api/
node-ts-app/
java-service/
python-app/
mixed-web-tsx/
```

E2E scenariok:

1. GitHub provider connected.
2. Repo sync.
3. Repo enable.
4. Index job completed.
5. `@duumbi intake` issue comment processed.
6. Intake artifact exists.
7. `@duumbi spec` creates branch and PR.
8. `@duumbi review` posts PR review.
9. `@duumbi closure` updates graph and knowledge candidate.

### Frontend tesztek

- Dashboard routes.
- Login redirect.
- Code Providers page.
- Repositories table.
- Run detail page.
- Model Settings page.
- Question topics UI.

### Performance tesztek

- Command parser high-volume webhook events.
- Indexing 10k / 100k LOC fixture.
- Graph query p95.
- Dashboard API p95.
- Queue throughput.

---

## 26. Elfogadási kritériumok

### Milestone end-to-end

A felhasználó képes:

1. Megnyitni `loop.duumbi.dev` publikus oldalt.
2. Belépni `app.loop.duumbi.dev` dashboardba.
3. GitHubbal vagy Google-lel SSO-zni.
4. GitHub Appot telepíteni.
5. Látni a GitHub organization repository-jait.
6. Kiválasztani legalább egy repository-t indexingre.
7. Látni, hogy az indexing completed.
8. GitHub issue-ban kiadni:

   ```text
   @duumbi intake
   ```

9. Látni a 👀 reakciót.
10. Kapni dashboard linket az issue-ba.
11. Megnyitni az intake artifactot dashboardon.
12. Kiadni:

   ```text
   @duumbi spec
   ```

13. Látni a létrejött spec PR-t.
14. PR-ben kiadni:

   ```text
   @duumbi review
   ```

15. Látni a review summaryt és inline kommenteket.
16. Issue-ban kiadni:

   ```text
   @duumbi closure
   ```

17. Látni a closure summaryt, graph snapshot frissítést és automatikusan publikált knowledge update-et.
18. Settingsben módosítani a raw repo snapshot retentiont.
19. Billing felületen látni a Stripe customer/subscription állapotot.
20. Model/data policy felületen régiónként szabályozni az LLM provider/model használatot.

### Minőségi kritériumok

- Minden run auditálható.
- Minden artifact forrásokra hivatkozik.
- Nincs cross-tenant adat szivárgás.
- Nincs raw secret log.
- Model catalog napi refresh működik.
- Provider:model policy UI-ból állítható.
- Webhook duplicate delivery nem indít duplikált runokat.
- Dashboard illeszkedik DUUMBI dark designhoz.
- Dokumentáció elérhető a `docs.duumbi.dev/loop` alatt.

---

## 27. Codex milestone végrehajtási sorrend

### M6.0. Discovery / plan rewrite PR

1. Clone/checkout:
   - `duumbi-infra`
   - `duumbi-registry`
   - `duumbi-web`
   - `duumbi-auth`
   - új `duumbi-loop`
2. Olvasd el:
   - `AGENTS.md`
   - `README.md`
   - `Cargo.toml`
   - `package.json`
   - Pulumi stack files
   - CI workflows
3. Írd meg:
   - `duumbi-loop/docs/discovery.md`
   - `duumbi-loop/docs/architecture.md`
4. Supersede-eld ezt a tervet v2 milestone dokumentummal, MVP nyelvezet nélkül.
5. Ne módosíts infra production stacket discovery nélkül.

### M6.1. Scaffold PR

1. Hozd létre Rust workspace-t.
2. Add hozzá:
   - Axum health endpoint,
   - DB migration alap,
   - config,
   - tracing,
   - Dockerfile,
   - docker-compose local dev.
3. CI:
   - fmt,
   - clippy,
   - test,
   - docker build.

### M6.1. Auth + org + billing PR

1. `duumbi-auth` repo/service scaffold.
2. Központi user/org/team schema.
3. Email magic link.
4. GitHub/Google identity linking.
5. Stripe customer/subscription/entitlement alap.
6. Session cookie.
7. `/api/me`, `/api/orgs`.

### M6.2. Data + graph foundation PR

1. Neon Postgres migration alap.
2. Postgres-backed property graph tables.
3. Graph store adapter boundary.
4. Retention policy model + cleanup job.
5. Regional LLM data policy model.
6. Audit events.

### M6.3. GitHub provider PR

1. GitHub App install flow.
2. Repo sync.
3. Code Providers page.
4. Repositories page.
5. Enable repo.
6. Branch/PR permission failure handling: UI + email + Slack + manual spec bundle.

### M6.3. GitLab provider PR

1. GitLab Cloud.
2. GitLab Self-Hosted manual webhook setup guide.
3. Connection test + SSRF protection.
4. Issue/MR note commands.
5. MR review/comments.

### M6.4. Intake PR

1. Webhook receiver.
2. Command parser.
3. 👀 reaction.
4. Intake run.
5. Artifact render.
6. Issue response link.
7. Dashboard run detail.

### M6.4. Spec PR

1. Spec generation artifact.
2. Branch + files + PR creation.
3. Permission-failure fallback path.
4. Issue response link.
5. Dashboard spec view.

### M6.4. Review PR

1. PR diff loader.
2. Spec compliance checker.
3. Review artifact.
4. Default comment mode; strict mode setting.
5. Inline comments + summary.
6. Dashboard review view.

### M6.4. Closure PR

1. Merge/PR state loader.
2. Incremental graph refresh.
3. Automatic knowledge publish.
4. Candidate-only org setting.
5. Closure comment.
6. Dashboard closure view.

### M6.5. Model catalog PR

1. Provider adapters.
2. Daily refresh job.
3. Model settings UI.
4. Regional model/data policy UI.
5. Routing policy.
6. Cost/fallback basics.

### M6.5. Docs + marketing PR

1. `duumbi-web/docs/loop`.
2. `loop.duumbi.dev` landing.
3. Pricing oldal Stripe defaulttal.
4. Blog tag/route.
5. Security/data retention docs.

### M6.5. Infra PR

1. Pulumi resources.
2. DNS.
3. Static Web Apps / Container Apps.
4. Key Vault.
5. Queue/event resources.
6. Neon Postgres secret reference.
7. Observability.
8. Budgets.

---

## 28. Környezeti változók

`.env.example`:

```dotenv
DUUMBI_ENV=local
DUUMBI_BASE_URL=http://localhost:3000
DUUMBI_API_URL=http://localhost:8080
DUUMBI_HOOKS_URL=http://localhost:8081
DUUMBI_AUTH_ISSUER=http://localhost:8090

DATABASE_URL=postgres://duumbi:duumbi@localhost:5432/duumbi_loop
NEON_DATABASE_URL=
REDIS_URL=redis://localhost:6379
SERVICE_BUS_CONNECTION_STRING=
AZURE_STORAGE_CONNECTION_STRING=

GITHUB_APP_ID=
GITHUB_APP_PRIVATE_KEY_PEM=
GITHUB_WEBHOOK_SECRET=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=

GITLAB_CLIENT_ID=
GITLAB_CLIENT_SECRET=
GITLAB_WEBHOOK_SECRET=

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

X_CLIENT_ID=
X_CLIENT_SECRET=

SLACK_CLIENT_ID=
SLACK_CLIENT_SECRET=
SLACK_SIGNING_SECRET=

STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=

OPENAI_API_KEY=
ANTHROPIC_API_KEY=
XAI_API_KEY=
DEEPSEEK_API_KEY=
MINIMAX_API_KEY=
GEMINI_API_KEY=

DUUMBI_DEFAULT_SOURCE_RETENTION_DAYS=7
DUUMBI_ALLOWED_LLM_REGIONS=EU,USA,China
DUUMBI_ENCRYPTION_KEY=
RUST_LOG=duumbi_loop=debug,tower_http=info
```

Productionben ezek ne közvetlen env secretként, hanem Key Vault reference-ként legyenek kezelve.

---

## 29. Példa artifact Markdown struktúrák

### Intake artifact

````markdown
# DUUMBI Intake: {issue_title}

## Summary

...

## Confidence

0.74

## Business value

High

## Effort

M, estimated 1-2 days

## Affected areas

| Area | Why relevant | Evidence |
|---|---|---|
| ... | ... | ... |

## Infographic

```mermaid
flowchart LR
...
```

## Similar examples

...

## Clarifying questions

### Topic 1: API behavior

1. ...

## Recommended provider:model

...

## Sources

- GitHub issue ...
- Code graph snapshot ...
- File ...
````

### Spec artifact

```markdown
# Business Specification

## Context
## Goals
## Non-goals
## Acceptance Criteria
## BDD Summary
## Rollout
```

```markdown
# Technical Specification

## Architecture Impact
## Affected Files/Symbols
## Data/API Changes
## Implementation Plan
## Tests
## Observability
## Rollback
```

### Review artifact

```markdown
# DUUMBI Review

## Overall status
## Spec compliance
## Blocking issues
## Warnings
## Missing tests
## Inline comments
## Sources
```

### Closure artifact

```markdown
# DUUMBI Closure

## Final outcome
## PR/MR summary
## Graph changes
## Published knowledge updates
## Follow-up items
## Sources
```

---

## 30. Rögzített terméktulajdonosi döntések

Ezek a döntések lezárják a korábbi nyitott kérdéseket. Codex ezeket követve dolgozzon, és csak akkor kérdezzen újra, ha új tény vagy érdemi architekturális kockázat indokolja.

1. **Billing:** Stripe a default billing provider.
2. **Email login:** magic link az első email login flow.
3. **Központi auth:** külön `hgahub/duumbi-auth` repo/service legyen, ne `duumbi-loop` belső crate.
4. **Graph backend:** első cloud milestone-ban Postgres-backed property graph tables Neon Postgresben. Neo4j/Kuzu csak későbbi adapterként.
5. **Source retention:** raw repo snapshot default 7 nap. Settingsben állítható: 0, 7, 14, 30, 90 nap. `0` = ephemeral clone, sikeres indexing után törlés.
6. **LLM data policy:** legyen régiókra bontott org policy: EU, USA, China.
7. **GitLab Self-Hosted:** első cloud milestone-ban kézi webhook setup elég.
8. **Spec PR branch protection:** ha a repo nem engedi botnak branch/PR létrehozását, a run legyen `needs_action`, UI-ban jelezzen, küldjön emailt és Slack üzenetet, és adjon manual spec bundle fallbacket.
9. **Knowledge update:** closure defaultban automatikusan publikáljon. Candidate-only mód legyen org settingként.
10. **Review strictness:** defaultban comment mode legyen blocking issue esetén is; strict mode külön org/repo setting.
11. **Database:** Neon Postgres legyen a default, Azure Database for PostgreSQL ne legyen bevezető default.
12. **Milestone framing:** ne MVP-ként kezeld a feladatot; M6 milestone workstreamként illeszd be, M2/M3/M5 függőségekkel.

---

## 31. Referenciák, amelyeket Codex nézzen meg

### DUUMBI repók

```text
https://github.com/hgahub/duumbi-web
https://github.com/hgahub/duumbi-web/tree/main/docs
https://github.com/hgahub/duumbi-registry
https://github.com/hgahub/duumbi-infra
```

### Kódgráf referencia

```text
https://github.com/CodeGraphContext/CodeGraphContext
```

Megfigyelések ebből a referenciából:

- CLI + MCP modellben épít kódgráfot AI asszisztenseknek.
- Tree-sitter alapú elemzés és több graph backend támogatás hasznos minta.
- Támogatott nyelvek között szerepel Python, JavaScript, TypeScript, Java, Go, Rust, HTML, CSS, TSX.
- A DUUMBI Loopban ezt nem kell másolni, de a graph schema, parser és backend adapter szemlélet követhető.

### Provider/auth/webhook dokumentáció

```text
https://docs.github.com/en/webhooks/webhook-events-and-payloads
https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/using-webhooks-with-github-apps
https://docs.gitlab.com/user/project/integrations/webhooks/
https://docs.gitlab.com/integration/oauth_provider/
https://docs.x.com/fundamentals/authentication/oauth-2-0/user-access-token
https://developers.google.com/identity/openid-connect/openid-connect
```

### Azure dokumentáció

```text
https://learn.microsoft.com/en-us/azure/container-apps/scale-app
https://learn.microsoft.com/en-us/azure/container-apps/jobs
https://learn.microsoft.com/en-us/azure/container-apps/manage-secrets
https://learn.microsoft.com/en-us/azure/service-bus-messaging/
https://learn.microsoft.com/en-us/azure/static-web-apps/custom-domain
```

### Model catalog dokumentáció

```text
https://developers.openai.com/api/reference/resources/models/methods/list/
https://platform.claude.com/docs/en/api/models/list
https://docs.x.ai/developers/rest-api-reference/inference/models
https://api-docs.deepseek.com/api/list-models
https://ai.google.dev/api/models
https://platform.minimax.io/docs/api-reference/models/openai/list-models
https://platform.minimax.io/docs/api-reference/models/anthropic/list-models
```

---

## 32. Codex végső output elvárás

A feladat végén Codex adjon:

1. PR linkeket repónként.
2. Rövid architektúra összefoglalót.
3. Végrehajtott tesztek listáját.
4. Ismert hiányosságokat.
5. Rögzített döntések és esetleges új, indokolt nyitott kérdések listáját.
6. Screenshotokat vagy local dev URL-eket:
   - Code Providers oldal,
   - Repositories oldal,
   - Run detail oldal,
   - Model Settings oldal.
7. Infra preview outputot:
   - `pulumi preview` releváns részlet,
   - új DNS/Container App/queue/Key Vault komponensek listája.

Ne zárd le completednek a milestone-t, ha nincs legalább a vállalt M6 slice-hoz tartozó end-to-end működés lokális vagy staging környezetben.
