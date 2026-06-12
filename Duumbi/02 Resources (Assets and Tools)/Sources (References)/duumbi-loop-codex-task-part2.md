# DUUMBI Loop — Part 2: pricing, folyamatok, UI/UX és üzemeltetői monitoring

> A [[duumbi-loop-codex-task]] kiegészítése. Az alapdokumentum architektúrát, adatmodellt és workflow-kat definiál; ez a dokumentum a kereskedelmi és termékfolyamat-réteget dolgozza ki: csomagok és árazás, billing-életciklus, regisztráció/onboarding, settings, code provider és repository kezelés folyamatai, analytics, run- és tudástár-felületek, valamint az üzemeltetői (staff) monitoring.
> **Ütközés esetén ez a dokumentum az irányadó.** Codex mindkét dokumentumot olvassa el az M6.0 discovery során, és a v2 milestone dokumentum mindkettőt olvassza össze.

---

## 1. Gap-összefoglaló: mit pótol ez a dokumentum

Az alapdokumentumból hiányzó vagy alulspecifikált területek:

| # | Hiány | Hol pótolva |
|---|---|---|
| 1 | Pricing: nincsenek csomagok, mérési egység, entitlement-mátrix, Stripe product-leképezés | §2 |
| 2 | Billing-folyamatok: checkout, upgrade/downgrade, lemondás, dunning, adat-offboarding | §3 |
| 3 | Regisztráció/onboarding flow, meghívások (invitations tábla/API teljesen hiányzott), abuse-védelem | §4 |
| 4 | Settings információs architektúra: a `/o/:org/settings` tartalma nem volt kibontva; account-szintű oldalak (profil, identities, sessions, tokenek) hiányoztak | §5 |
| 5 | Code provider folyamatok: reconnect, revoke kezelés, törlés hatáselemzéssel | §6 |
| 6 | Repository: index pipeline állapotai, per-repo beállítások, needs-permissions remediáció | §7 |
| 7 | Analytics oldal: navigációban és route-ban szerepelt, de tartalma sehol nem volt definiálva | §8 |
| 8 | Run-felületek: lista, részletoldal típusonként, Questions flow, cancel/re-run szemantika | §9 |
| 9 | Tudástár UI: knowledge_entries séma volt, de böngészés, candidate jóváhagyási sor, szerkesztés, export nem | §10 |
| 10 | Üzemeltetői monitoring: felhasználók/előfizetések/usage/margó követése, admin felület, product analytics, KPI-k | §11 |
| 11 | Notification/email infrastruktúra: nem volt email provider döntés, template-lista, notification mátrix | §12 |
| 12 | State machine inkonzisztencia: a spec PR fallback `needs_action` állapotot használ, de a state machine-ben csak `needs_input` volt | alapdoc javítva + §13 |
| 13 | Model catalog ártábla: a provider model-list API-k többsége NEM ad árat, így a napi refresh önmagában nem tudja feltölteni a pricing mezőt | §2.6 |
| 14 | Trial, ÁFA/Stripe Tax, számlázás, GDPR account-törlés, org-törlés, adatexport | §3 |
| 15 | DUUMBI-native provider: az alapdoc kimondja, hogy elsődleges adapter, de minden flow GitHub/GitLab-centrikus; a native workflow felülete nyitott kérdés marad | §14 |

---

## 2. Pricing és csomagok

### 2.1 Alapelvek

- **Seat + credit hibrid.** A seat a kiszámítható bevétel, a credit a változó LLM-költség fedezete. Tiszta seat-alapú árazás a futtatásintenzív orgoknál negatív margót adna; tiszta usage-alapú árazás kiszámíthatatlan a vevőnek.
- **Fizetős seat** = `owner`, `admin`, `developer`, `reviewer` szerepű tag. A `viewer` és `billing_admin` ingyenes — ez értékesítési érv ("az egész csapat láthatja az artifactokat").
- **BYOK (bring your own key)** külön dimenzió: ha az org saját LLM API kulcsot ad meg (a meglévő `provider_credentials` tábla org-szintű kulcsokra is szolgál), a runok LLM-költsége őt terheli, mi csak orchestration díjat számítunk fel creditben.
- **A credit a user-facing egység, az USD-költség belső.** A `usage_meter_events` rögzíti a token- és USD-szintű tényadatot, a `credit_ledger` a credit-mozgásokat.

### 2.2 Mérési egység: credit

```text
1 credit = 0.10 USD belső LLM-költségkeret (kurált ártábla szerint számolva)

run credit-terhelés = max(min_credits[workflow], ceil(llm_cost_usd / 0.10))

min_credits:
  intake  = 1
  spec    = 3
  review  = 2
  closure = 2

BYOK run = a fenti érték 25%-a, minimum 0.25 credit (orchestration fee)
```

- Tipikus teljes loop (intake+spec+review+closure) költségbecslés default, költséghatékony routinggal: **8–20 credit**; prémium modellekkel (org policy szerint) 3–5×.
- Az included creditek havonta resetelnek, **nem görgethetők**. Vásárolt (overage) és bónusz creditek 90 napig érvényesek, és az included keret után fogynak.
- A futás előtt a router becsült creditet számol (`max_cost_per_run` policy ellenőrzéssel); a tényleges terhelés a run végén könyvelődik.

### 2.3 Csomagok és entitlement-mátrix

| | Free | Team | Business | Enterprise |
|---|---|---|---|---|
| Ár | $0 | **$19/seat/hó** ($190/seat/év) | **$39/seat/hó** ($390/seat/év) | egyedi |
| Fizetős seatek | max 3 | korlátlan | korlátlan | korlátlan |
| Aktív (enabled) repo | 2 | 10 | 50 | egyedi |
| Credit / hó | 50 / org | 150 / seat (org-poolban) | 400 / seat (org-poolban) | egyedi |
| Credit overage | nincs (block) | $12 / 100 credit | $12 / 100 credit | egyedi |
| BYOK | nincs | igen | igen | igen |
| Source retention opciók | csak 7 nap | 0/7/14/30 nap | 0/7/14/30/90 nap | egyedi |
| Slack integráció | nincs | igen | igen | igen |
| Strict review mode | nincs | igen | igen | igen |
| Regionális LLM data policy | nincs (global default) | nincs | igen | igen |
| Knowledge candidate-only mód | nincs | nincs | igen | igen |
| Audit log UI / export | 30 nap, csak UI | 90 nap, csak UI | 1 év + CSV export | egyedi + SIEM |
| API token / programmatic access | nincs | org token | org token + analytics export | igen |
| GitLab Self-Hosted | nincs | 1 instance | 5 instance | korlátlan |
| GitHub Enterprise Server | nincs | nincs | nincs | igen |
| Support | community | email | priority email | dedikált + SLA 99.9% |
| PR-komment branding | "powered by DUUMBI Loop" | elrejthető | elrejthető | elrejthető |

Enterprise-only később: SAML/SCIM, dedikált Neon régió / privát deployment, DPA, invoice (nem kártyás) fizetés.

### 2.4 Trial

- Org-létrehozáskor automatikus **14 napos Business trial, kártya nélkül**.
- Dashboard banner visszaszámlálóval; T-3 napnál email az ownernek/billing_adminnak.
- Lejáratkor automatikus visszaesés Free entitlementekre (semmi nem törlődik, lásd §3.3 limit-túllépés kezelés).
- Egy user csak egy trial-t indíthat; egy org csak egyszer trial-ozhat.

### 2.5 Stripe leképezés

```text
Products:
  loop-team        prices: team-monthly ($19, per-seat, licensed),  team-annual ($190)
  loop-business    prices: business-monthly ($39), business-annual ($390)
  loop-credits     price:  credit-overage (metered, $0.12/credit, havi utólagos elszámolás
                           Stripe Billing Meter: `loop_credits_overage`)
```

- **Checkout:** Stripe Checkout hosted page (quantity = fizetős seatek száma). Seat-szám változáskor subscription item quantity update, Stripe proration-nel.
- **Customer Portal:** fizetési mód csere, számlák letöltése. A lemondás **in-app** történik (lásd §3.3), a portálban a cancel ki van kapcsolva, hogy az exit-survey és az adat-offboarding tájékoztatás ne maradjon ki.
- **Stripe Tax** bekapcsolva: EU VAT, B2B reverse charge VAT ID alapján, a checkout kezeli. Számlát a Stripe küld.
- **Entitlement resolver:** a `billing_subscriptions` + plan definíció → `billing_entitlements` materializálás minden releváns webhookon. Az API/worker mindig a `billing_entitlements`-ből olvas, sosem hív Stripe-ot kérés közben.
- **Feldolgozandó webhookok** (idempotensen, `billing_events.stripe_event_id` unique kulccsal):

```text
checkout.session.completed
customer.subscription.created | updated | deleted
invoice.payment_succeeded | payment_failed | finalized
customer.updated
payment_method.attached | detached
charge.refunded
```

### 2.6 Költségszámítás és kurált ártábla

Tény: az OpenAI, Anthropic, DeepSeek, Gemini, MiniMax model-list API-k **nem adnak vissza árat** (kivétel: az xAI `language-models` endpoint ad token-árat). Ezért:

- A `model-catalog` crate tartalmaz egy **kurált `pricing.toml`** táblát (provider, model_id pattern, input/output ár per 1M token, érvényesség-dátum), DB-szintű override-dal.
- A napi catalog refresh a modellek elérhetőségét frissíti; az ár a kurált táblából jön, és a refresh job **megjelöli** azokat az elérhető modelleket, amelyekhez nincs ár (`pricing_missing` flag → staff alert).
- Ár nélküli modellt a platform-kulcsos routing **nem választhat**; BYOK módban választható, konzervatív becsült credit-terheléssel és "estimated" jelöléssel.
- `run_costs` a híváskor érvényes árral számol és tárolja az ár-verziót is (auditálhatóság).

### 2.7 Quota-érvényesítési pontok

| Pont | Ellenőrzés | Viselkedés limitnél |
|---|---|---|
| Webhook command érkezik | credit-egyenleg, plan aktív, repo enabled | udvarias provider-komment: "quota exceeded / subscription inactive" + dashboard link + banner; run nem jön létre |
| Run indítás előtt (worker) | becsült credit ≤ egyenleg + `max_cost_per_run` | mint fent; ha auto-recharge be van kapcsolva, vásárlás majd futás |
| Repo enable | repo-limit | gomb disabled + upgrade CTA |
| Meghívás küldés | seat-szám (fizetős szerep esetén) | dialog: "Adding this member increases your subscription to N seats (+$X/mo)" → megerősítés → quantity update |
| Retention beállítás | plan szerinti opciók | nem elérhető opció lockolva, upgrade tooltip |
| 80% credit-fogyás | — | email + banner (`billing_admin`, `owner`) |
| 100% credit | — | új runok blokkolva; auto-recharge opt-in felajánlva (havi felső kerettel, org állítja) |

### 2.8 Pricing oldal tartalma (`loop.duumbi.dev/pricing`)

1. Hero + havi/éves toggle.
2. 4 plan-kártya (Free/Team/Business/Enterprise) a fenti mátrix kivonatával, "Start free" / "Start 14-day trial" / "Contact us" CTA-kkal.
3. **"What is a credit?"** magyarázó blokk példatáblával (tipikus intake/spec/review/closure credit-tartományok, BYOK-kedvezmény).
4. BYOK magyarázat.
5. Teljes feature-összehasonlító tábla (lenyitható).
6. FAQ: mi számít seatnek; mi történik a creditekkel hónap végén; lemondás bármikor; mi történik az adatokkal lemondás után; EU adatkezelés; melyik LLM providerek; BYOK működés.

---

## 3. Billing-folyamatok

### 3.1 Előfizetés indítása

1. `Settings → Billing` → plan-választó (aktuális plan kiemelve, seat-szám előre kitöltve a fizetős tagok számával).
2. `Upgrade` → Stripe Checkout session (POST `/api/billing/checkout-session`, plan + quantity + monthly/annual).
3. Sikeres fizetés → `checkout.session.completed` webhook → subscription + entitlement materializálás → redirect vissza `/o/:org/settings/billing?status=success`.
4. Megerősítő email (Stripe számla + saját "welcome to Team/Business" email).
5. Edge: webhook-késés esetén a success oldal pollozza a subscription státuszt (max 30 s), addig "Activating…" állapot.

### 3.2 Plan-/seat-változtatás

- **Upgrade (Team→Business, havi→éves):** azonnal érvényes, Stripe proration.
- **Downgrade (Business→Team, éves→havi):** periódus végén lép életbe (`schedule` a Stripe-ban); a UI mutatja: "Switches to Team on {date}". Downgrade előtt entitlement-ütközés ellenőrzés: ha pl. 90 napos retention vagy regionális policy aktív, figyelmeztető lista, és a downgrade életbelépésekor a beállítások a megengedett maximumra állnak vissza (audit eventtel).
- **Seat-változás:** fizetős szerepű tag hozzáadása/meghívása automatikusan emeli a quantity-t (megerősítő dialoggal, §2.7); tag eltávolítás/lefokozás viewerre a következő számlázási ciklusban csökkenti (azonnali credit nincs).

### 3.3 Lemondás

1. `Settings → Billing → Cancel subscription` (csak `owner`/`billing_admin`).
2. Exit-survey (opcionális, 1 kérdés + szabad szöveg) → megerősítő dialog, amely explicit felsorolja: meddig él még a plan, mi történik utána (Free-limitek), adat-megőrzés és export lehetőség.
3. `cancel_at_period_end=true` → banner: "Subscription ends on {date}" + `Reactivate` gomb (visszavonás egy kattintással a periódus végéig).
4. Periódus végén → Free entitlementek. **Limit-túllépés kezelése (downgrade after cancel/trial):**
   - meglévő artifactok, runok, knowledge entries, graph snapshotok **nem törlődnek**, olvashatók maradnak;
   - a Free repo-limit felett enabled repók automatikusan `Disabled (plan limit)` státuszba kerülnek (org admin választhatja ki, melyik 2 maradjon aktív; default: a 2 legaktívabb);
   - új runok csak a Free kereten belül futnak;
   - 90 nap Free-inaktivitás után (egy run sem, egy login sem) az org "dormant"; a Free-en felüli adat törlési figyelmeztetés: T-30, T-7, T-1 email, majd archiválás/törlés audit eventtel.
5. Lemondás-visszaigazoló email (dátummal, export-linkkel).

### 3.4 Sikertelen fizetés (dunning)

1. `invoice.payment_failed` → email a `billing_admin`+`owner`-nek + dashboard banner ("Update payment method") + audit event.
2. Stripe Smart Retries (max 4 próbálkozás ~14 nap alatt), minden próbálkozásról értesítés.
3. `past_due` 7. naptól: **új runok blokkolva** (provider-komment: "billing issue"), dashboard és adatok teljeskörűen elérhetők maradnak.
4. Végleges bukás → subscription `canceled` → §3.3/4. pont szerinti downgrade.

### 3.5 Org adatexport és org törlése

- `Settings → General → Export organization data`: aszinkron job (`data_export_jobs`), zip = artifactok (MD+JSON), knowledge entries, question threadek, run-metaadatok, audit log (plan szerint), graph snapshot exportok. Letöltő link 7 napig él, email-értesítés.
- `Settings → General → Delete organization` (csak `owner`, org-név begépelése + aktív előfizetés esetén előbb lemondás): 7 napos grace (visszavonható, email-linkkel), majd hard delete minden org-adatra + provider installation uninstall-kérés + audit (org-anonimizált formában megőrzött minimális billing-rekord, jogszabályi okból).

### 3.6 Felhasználói fiók törlése (GDPR)

- `Account → Danger zone → Delete account`: ha a user egyetlen org egyetlen ownere, előbb ownership-átadás vagy org-törlés kötelező (a UI vezeti végig).
- Törléskor: identities unlink, sessions/tokens revoke, PII anonimizálás (audit eventekben `deleted-user-{hash}`), email-megerősítés.
- Data subject request (export): `Account → Download my data`.

---

## 4. Regisztráció és onboarding

### 4.1 Signup flow

1. `loop.duumbi.dev` CTA → `app.loop.duumbi.dev/login` (signup és login ugyanaz a felület).
2. Módok: GitHub / GitLab / Google / X / email magic link. Magic link: email beírás → 15 perces, egyszer használatos link → kattintás = verifikált email + session.
3. Első belépés után, ha a usernek nincs orgja → **onboarding wizard**:
   - **1. lépés — Org:** név, slug, (opció) régió-preferencia (EU/US). Trial automatikusan indul (§2.4).
   - **2. lépés — Provider:** GitHub App install / GitLab connect / "Skip for now".
   - **3. lépés — Repos:** repo-választó (max plan-limit), enable → indexing indul.
4. Dashboard első nézet: **activation checklist widget** (Connect a provider → Enable a repo → Wait for indexing → Run `@duumbi intake` on an issue → Open your first artifact), elemenként kipipálódik, eltüntethető.
5. Ha a user meghívással érkezik (§4.3), a wizard kimarad, egyből a meghívó orgba lép.

### 4.2 Identity linking és ütközések

- Azonos verifikált email különböző providerből → ugyanahhoz a Userhez linkelődik (megerősítő képernyővel: "We found an existing account for {email}. Link this {provider} identity?").
- Nem egyező email → új User; utólagos linkelés `Account → Linked identities` alól (belépett állapotban indított OAuth flow).
- Identity unlink csak akkor, ha marad legalább egy login-képes identity.

### 4.3 Meghívások (az alapdocból hiányzott)

```sql
invitations (
  id UUID PK,
  organization_id UUID NOT NULL,
  email TEXT NOT NULL,
  role TEXT NOT NULL,           -- RBAC szerep
  invited_by UUID NOT NULL,
  token_hash TEXT NOT NULL,
  status TEXT NOT NULL,         -- pending | accepted | revoked | expired
  expires_at TIMESTAMPTZ,       -- default: 7 nap
  created_at TIMESTAMPTZ
)
```

```http
GET    /api/orgs/{id}/invitations
POST   /api/orgs/{id}/invitations        # email + role; fizetős szerepnél seat-dialog (§2.7)
DELETE /api/invitations/{id}             # revoke
POST   /api/invitations/accept           # token; login után hívódik
```

Flow: admin meghív (`Settings → Members → Invite`) → email "{name} invited you to {org} on DUUMBI Loop" → `/invite/:token` → login/signup → accept → tag a megadott szereppel. Pending meghívók listája revoke/resend gombbal. Lejárt token → "Ask for a new invitation" képernyő.

### 4.4 Abuse-védelem

- Disposable email domain blocklist a magic linknél.
- Signup rate limit IP-nként; magic link request rate limit emailenként.
- **1 Free org / user** (továbbiakhoz kártya vagy meghívás kell).
- Velocity-szabályok (staff alert + auto-flag): Free org > 20 run/óra; sok org ugyanarról az IP-ről; trial-újrapróbálkozás minta.
- Flagelt org: runok felfüggesztve (`suspended` entitlement), banner + email, staff oldja fel (§11).

---

## 5. Settings információs architektúra

### 5.1 Org-szintű (`/o/:org/settings/*`)

```text
/general          org név, slug, logo, régió-preferencia, data export, delete org
/members          taglista (név, email, szerep, utolsó aktivitás), szerep-módosítás,
                  eltávolítás, Invite gomb, pending meghívók
/billing          aktuális plan + seatek, következő számla, credit-egyenleg (progress bar:
                  included + purchased), overage/auto-recharge kapcsoló + havi cap,
                  számlák (Stripe portal link), payment method (portal link),
                  upgrade/downgrade, cancel/reactivate
/usage            run-fogyás idősor (típusonként), credit burn-down, BYOK LLM-költés
                  (ha van), repo-limit kihasználtság, CSV export (Business+)
/model-policy     allowed/blocked providerek és modellek, default modellek
                  workflow-nként, fallback chain, max_cost_per_run, max_context_tokens
/model-policy/regions   (Business+) régió-bucket szerkesztő: EU/USA/China allowlist,
                  blocklist, cross-region fallback, retention overrideok
/retention        raw source retention (plan szerinti opciók), prompt/response/snippet
                  retention, osztályonkénti magyarázattal
/notifications    org-szintű default notification mátrix (§12.3), Slack channel mapping
/api-tokens       org/service tokenek: létrehozás scope-okkal, utolsó használat, revoke;
                  raw token egyszer látszik
/audit-log        szűrhető audit-nézet (actor, esemény-típus, dátum), plan szerinti
                  visszatekintéssel, CSV export (Business+)
```

`Integrations` (Slack) marad külön top-nav oldal az alapdoc szerint.

### 5.2 Account-szintű (`/account/*`, org-független)

```text
/profile          név, avatar, email (csere: magic link megerősítés az új címre),
                  nyelv (UI első körben English-only — lásd §13.5)
/identities       linkelt identityk (GitHub/GitLab/Google/X/email), link/unlink
/sessions         aktív sessionök (eszköz, IP, utolsó aktivitás), revoke / revoke all
/tokens           személyes API tokenek
/notifications    per-user értesítési preferenciák (org-onkénti override, §12.3)
/danger           account törlés, data export (§3.6)
```

### 5.3 Általános UI-szabályok (minden dashboard-oldalra)

- Minden oldalnak legyen **loading** (skeleton), **empty** (illusztráció + elsődleges CTA) és **error** (retry gomb + hibarészlet lenyitható) állapota.
- Destruktív műveletek: piros, megerősítő dialog, súlyos esetben (org törlés, provider törlés) név begépelése.
- Státusz-színtérkép (dark theme, WCAG AA kontraszt): completed/enabled = DUUMBI zöld; running/syncing = kék, pulzáló pont; needs_input = sárga; needs_action = narancs; failed/error = piros; cancelled/disabled = szürke; superseded = szürke, áthúzott.
- Táblák: oszloprendezés, szabad szöveges szűrő, oldalankénti 25/50/100, bulk-kijelölés ahol értelmes.
- Minden run/artifact entitásnak van stabil, megosztható deep linkje.

---

## 6. Code provider kezelés — folyamatok

### 6.1 Connect

- **GitHub:** `+ Add provider → GitHub` → GitHub App install redirect → org/repo kiválasztás GitHubon → callback → installation az orghoz kötve → automatikus első repo-sync → kártya `Connected`, "N repos discovered".
- **GitLab Cloud:** OAuth → group-választó → webhook auto-regisztráció → `Connected`.
- **GitLab Self-Hosted:** form (base URL + admin token) → SSRF-validáció (alapdoc §8) → connection test (verziók, jogosultságok kijelzése) → generált webhook URL + secret + event checklist + lépésenkénti setup guide → "Verify webhook" gomb (test delivery-re vár, 60 s timeout) → `Connected`.

### 6.2 Életciklus-műveletek

| Művelet | Trigger | Viselkedés |
|---|---|---|
| Sync | manuális gomb / 6 óránkénti scheduled job | repo-lista frissítés; renamed/transferred/archived/deleted repók státusz-frissítése; kártyán `Last sync` |
| Reconnect | `Needs reconnect` státusz (token expiry, jogosultság-szűkülés) | kártya-CTA újraindítja az OAuth/install flow-t; meglévő repo-bekötések megmaradnak |
| Revoke (külső) | `installation.deleted` / token revoke webhook | provider `Revoked`, érintett repók `Disabled (provider revoked)`, email az adminoknak, banner |
| Delete (belső) | kártya menü → Remove provider | hatáselemző dialog: érintett enabled repók, futó runok, graph snapshotok listája; megerősítés névbeírással; runok cancel, repók disable, artifactok megmaradnak |

### 6.3 Kártya-UI

Providerenként: logó, instance URL (self-hostednál), státusz-badge, csatolt org/group szám, enabled repo szám, last sync, hibaüzenet (ha van), műveletek (Sync now / Reconnect / Remove). Empty state: a négy provider-típus kártyája "Connect" CTA-val.

---

## 7. Repository kezelés — folyamatok

### 7.1 Enable → index pipeline

```text
Disabled → (enable, quota-check) → Queued → Cloning → Parsing → Storing
        → Enabled (Indexed)  |  Index failed
```

- A táblázat `Status` oszlopa a pipeline-fázist mutatja, folyamatjelzővel (Cloning/Parsing alatt becsült hátralévő idő, ha mérhető).
- `Index failed`: sor-szintű hiba-ikon → lenyitható hibarészlet (mely fájl/fázis, parser-hiba) + `Retry` gomb. 3 egymást követő bukás → email az adminnak.
- `Needs permissions`: remediációs panel — pontosan mely permission hiányzik, mit kell a providernél átállítani (deep link a GitHub App settings / GitLab project settings oldalra), majd `Re-check` gomb.
- Push-trigger: enabled repón push webhook → inkrementális index (alapdoc performancia-cél: < 2 perc).

### 7.2 Műveletek és szemantika

- **Disable:** parancsok és indexelés leáll; meglévő artifactok/snapshotok olvashatók maradnak; a sor `Disabled`-re vált. Komment-parancs disabled repón: udvarias válasz "this repository is not enabled".
- **Reindex:** teljes újraindexelés megerősítéssel ("uses indexing capacity, current snapshot stays until the new one completes").
- **Bulk:** multi-select → Enable/Disable/Reindex; quota-túllépésnél részleges végrehajtás jelzéssel ("Enabled 3 of 7 — plan limit reached").

### 7.3 Per-repo beállítások (sor → Settings)

```text
retention override (org defaulttól eltérés, plan-kereten belül)
strict review mode override
default branch (ha nem a provider-default)
command allowlist (mely parancsok futhatnak ezen a repón)
.duumbiignore státusz kijelzése (létezik-e, hány pattern)
```

---

## 8. Analytics oldal (`/o/:org/analytics`) — org-facing

Globális szűrők: dátumtartomány (7/30/90 nap, custom), repo multi-select.

**KPI-kártyasor (felül):**

```text
Runs this period (típusonkénti bontás-tooltippel)   Success rate (%)
Median cycle time (issue → closure)                 Credits used / included
```

**Widgetek:**

| Widget | Tartalom |
|---|---|
| Runs over time | stacked area, intake/spec/review/closure |
| Funnel | intake → spec → review → closure konverzió a periódusban |
| Cycle time breakdown | medián idő intake→spec, spec→merge, merge→closure |
| Review findings | severity szerinti trend (blocking/warning/nit) + resolved arány |
| Spec compliance | átlag % trend |
| Knowledge | published entries, pending candidates (link a jóváhagyási sorra) |
| Top repositories | aktivitás, success rate, átlag credit/run |
| Cost panel | credit burn-down; BYOK orgnál tényleges LLM USD-költés providerenként |

- CSV export (Business+): a widget-adatok és a run-szintű nyersanyag.
- Empty state (nincs még run): onboarding checklist + dokumentáció-link.

---

## 9. Run-felületek: intake / spec / review / closure

### 9.1 Runs lista (`/o/:org/runs`)

Oszlopok: típus-ikon + command, repo, forrás (issue/PR # link a providerre), státusz, trigger user, provider:model, credit, időtartam, indítás ideje. Szűrők: típus, státusz, repo, user, dátum. Élő frissítés (WS/SSE) a futó sorokra. `needs_input`/`needs_action` sorok kiemelve a lista tetején külön szekcióban ("Waiting on you").

### 9.2 Run detail — közös fejléc

```text
[típus-badge] [státusz-badge]  {repo} #{issue/pr}  ↗ provider link
provider:model | duration | credits (becsült → végleges) | confidence | triggered by | started at
Műveletek: Cancel (futónál) | Re-run (lezártnál, model-választóval) | Copy link | Raw artifact (JSON)
```

- **Cancel szemantika:** cancel flag → a worker lépéshatáron áll le (folyamatban lévő LLM-hívást megszakítja); részleges artifact `cancelled` státusszal mentődik; a provider-komment frissül ("cancelled by {user}"); a már elfogyasztott credit terhelődik.
- **Re-run:** új run, a régi `superseded`; a provider-kommentben a friss link.

### 9.3 Intake nézet

Tabok: `Overview` (executive summary, scores kártyákon: confidence/business value/effort/time), `Affected areas` (tábla: terület → miért releváns → evidence-link fájl:sor mélységig), `Infographic` (Mermaid/SVG render + graph neighborhood), `Questions`, `Sources`, `Timeline`, `Raw`.

**Questions flow** (a `/o/:org/questions` oldal aggregálja az összes nyitott topicot org-szinten):

1. Intake nyitott `QuestionTopic`-okat hoz létre → run `needs_input` → értesítés (§12) a developer+ szerepeknek.
2. Topic-nézet: kérdéslista + chat-szerű thread; bárki (developer+) válaszol; @mention támogatás.
3. Topic `Resolve` gomb → ha minden topic resolved → run-fejlécben "Ready for spec" jelzés + (opció) automatikus provider-komment: "All questions answered — run `@duumbi spec` to continue."
4. Spec run a megválaszolt threadeket inputként tölti be (alapdoc §13).

### 9.4 Spec nézet

`Artifacts` tab: a `.duumbi/specs/...` fájlok fanézete inline markdown-renderrel; `PR` tab: PR link, státusz (open/merged/closed), validation checklist állapota a PR-ből tükrözve; `Plan` tab: agent implementation plan; közös tabok (Sources/Timeline/Raw). `needs_action` esetben (branch protection): teljes szélességű remediációs panel — pontos blokkolt művelet, szükséges permission, `Download spec bundle` gomb (zip), `Retry` a jogosultság javítása után.

### 9.5 Review nézet

`Findings` tab: severity szerint csoportosítva (blocking/warning/nit/info), elemenként: leírás, fájl:sor link a provider-diffre, spec-AC hivatkozás, "posted inline" jelölés; `Spec compliance` tab: AC-nkénti teljesülés (✓/✗/partial) + összesített %; `Tests` tab: érintett tesztek, hiányzó BDD-lefedettség; közös tabok. Ha a 20 inline-komment limit miatt nem minden finding került ki a PR-be, a dashboard a teljes listát mutatja, jelölve, mi ment ki.

### 9.6 Closure nézet

`Graph changes` tab: snapshot-diff összefoglaló (added/changed/removed nodes & edges, érintett szimbólumlista); `Knowledge updates` tab: publikált/candidate entries linkekkel (§10); `Outcome` tab: spec compliance végső érték, merged PR, CI-állapot; `Follow-ups` tab: follow-up itemek + "Create issue" gomb (provider-be issue-t nyit, elő-kitöltött bodyval); közös tabok.

---

## 10. Tudástár (`/o/:org/knowledge`)

### 10.1 Lista

Oszlopok/kártyák: cím, típus (pattern/decision/convention/api-contract/gotcha), repo, státusz (`published` / `candidate` / `deprecated`), forrás-run, utolsó frissítés. Keresés (Postgres FTS + opcionális szemantikus keresés pgvectorral), szűrés repo/típus/státusz szerint.

**Candidate-jóváhagyási sor** (auto_candidate módú orgnál, illetve manuálisan visszatartott entryknél): külön tab badge-dzsel ("4 pending"); elemenként diff-szerű nézet (mit adna hozzá/módosítana), `Approve` / `Edit & approve` / `Reject` (indoklással), bulk approve; minden döntés audit event.

### 10.2 Entry-részlet

```text
Tartalom (markdown)
Provenance: forrás-run, issue/PR, commit SHA, fájl:sor hivatkozások, graph snapshot id
Kapcsolatok: linkelt szimbólumok/fájlok (graph), kapcsolódó entryk
Verziótörténet: ki/mi módosította (run vagy user), diff-nézet, visszaállítás
Műveletek: Edit (developer+) | Deprecate (indoklással; a régi verzió kereshető marad,
           "deprecated" jelöléssel) | Delete (admin, csak manuálisan létrehozottra)
```

- Manuális létrehozás: `New entry` (developer+) — cím, típus, repo, tartalom, opcionális fájl/szimbólum-hivatkozások.
- Export: teljes tudástár MD+JSON zipként (a §3.5 org-export része, külön gombbal is).
- Closure-integráció: closure run a publikált entryket azonnal, a candidate-eket a sorba teszi; a `knowledge_entries` táblához `status` mező kell (`candidate|published|deprecated`) — alapdoc-kiegészítés (§13).

---

## 11. Üzemeltetői monitoring: felhasználók, előfizetések, usage

### 11.1 Eszköz-stack és felelősség-megosztás

| Réteg | Eszköz | Mire |
|---|---|---|
| Billing source of truth | **Stripe Dashboard** | MRR, churn, dunning, számlák, refundok — nem duplikáljuk, csak tükrözzük |
| Billing-tükör | `billing_*` táblák (webhookból) | entitlement-resolver + staff felület + riportok alapja |
| Product analytics | **PostHog EU Cloud** (app) + **Plausible** (marketing, cookie-mentes) | funnel, aktiváció, retenció, feature-használat |
| Usage-aggregátum | `org_daily_usage` napi rollup job (`usage_meter_events`-ből) | staff felület, margó-számítás, org-szintű Usage oldal |
| Infra/observability | meglévő Log Analytics + OpenTelemetry (alapdoc §23) | hibák, latency, queue |
| Staff felület | dashboardba épített `/staff/*` (lásd 11.3) | napi operáció |

### 11.2 Esemény-taxonómia (PostHog)

Pszeudonim user/org ID-kkal, **forráskód-tartalom és prompt soha nem kerül analytics eseménybe**:

```text
signup_started, signup_completed, org_created
provider_connect_started, provider_connected, repo_enabled, index_completed
first_intake_completed          # aktivációs esemény
intake_completed, spec_pr_created, review_completed, closure_completed
question_answered, knowledge_candidate_approved
checkout_started, subscription_activated, plan_upgraded, plan_downgraded,
subscription_cancel_requested, subscription_canceled
credit_limit_warning_shown, credit_limit_blocked, byok_enabled
```

### 11.3 Staff felület (`/staff/*`)

A dashboard appon belül, `is_staff` claim-mel (duumbi-auth-ban karbantartott staff-lista) gátolva; minden staff-megtekintés és -művelet audit event. Org-adatba betekintés **read-only "view as org"** módban, jól látható banner alatt.

```text
/staff/overview        signups (ma/7 nap), aktivációs ráta, aktív orgok (WAU),
                       runok ma + failure %, LLM-költés ma (USD), MRR + dunning count,
                       DLQ-méret, catalog-health
/staff/orgs            kereshető tábla: plan, seatek, credit-fogyás, LLM COGS,
                       becsült margó, flagek; org-részlet: usage-idősor, runok,
                       billing-események, entitlement-override (bónusz credit,
                       manuális plan, suspend/unsuspend — mind indoklással + audit)
/staff/users           keresés email/identity szerint, orgok, utolsó belépés
/staff/billing         subscription-lista státusszal, dunning-sor, feldolgozatlan/
                       hibás Stripe webhook-események replay gombbal
/staff/abuse           velocity-flagek sora (§4.4), suspend/unsuspend
/staff/ops             queue lag, DLQ-elemek replay-jel, model catalog refresh napló
                       (pricing_missing modellek!), legutóbbi failed runok
```

### 11.4 KPI-definíciók

```text
Aktiváció        = org első completed intake-je a signuptól számított 7 napon belül
Aktív org (WAU)  = legalább 1 run az adott héten
W4-retenció      = aktivált orgok aránya, amelyeknek a 4. héten is volt runja
MRR              = aktív subscriptionök havi normalizált összege (Stripe-tükörből)
Logo churn       = lemondott fizető orgok / fizető orgok (havi)
NRR              = meglévő kohorsz bevétel-változása (upgrade/seat/overage − churn)
Bruttó margó/org = (subscription + overage bevétel) − LLM COGS − becsült infra-hányad
```

### 11.5 Riportok és üzemeltetői alertek

- **Heti digest** (scheduled job → email + Slack az operátornak): signups, aktivációk, MRR-delta, top 10 org usage szerint, negatív margójú orgok, run failure-ráta, pricing_missing modellek.
- Alertek (a meglévő alert-listán felül): fizetésibukás-spike; Free-tier velocity-flag; org napi LLM-költése > $X platform-kulcson; Stripe webhook feldolgozási hiba; credit-könyvelés és Stripe meter eltérése (napi egyeztető job).

---

## 12. Notification- és email-rendszer

### 12.1 Email provider

Default: **Resend** (developer-barát, jó deliverability); küldő domain: `mail.duumbi.dev` (SPF/DKIM/DMARC a `duumbi-infra`-ban). EU data residency igény esetén alternatíva: Postmark vagy AWS SES eu-west-1 — M6.1-ben ellenőrizendő, döntés a discoveryben. Bounce/complaint webhookok logolva.

### 12.2 Email template-leltár

```text
Auth:      magic link, invitation, email-csere megerősítés
Billing:   trial T-3, payment failed (+ retry-k), subscription started/canceled,
           credit 80% / 100%, downgrade-ütközés összefoglaló
Workflow:  run needs_action (permission-blokk), run needs_input (új kérdés-topic),
           ismétlődő index-bukás, provider revoked
Lifecycle: dormant-org adat-törlés T-30/T-7/T-1, org-törlés grace + megerősítés,
           account-törlés megerősítés, data export kész
Digest:    heti org-összefoglaló (opt-in): runok, findings, knowledge-frissítések
```

Tranzakciós emailek nem leiratkozhatók (auth/billing/törlés-figyelmeztetés); a digest és a workflow-értesítések igen.

### 12.3 Notification-mátrix (default; org-szinten és per-user felülírható)

| Esemény | Email | Slack | In-app banner | Címzett |
|---|---|---|---|---|
| run completed | – | ✓ (pref szerint) | – | trigger user |
| run failed | ✓ | ✓ | ✓ | trigger user + admin |
| run needs_input (új kérdés) | ✓ | ✓ thread | ✓ | developer+ |
| run needs_action (permission) | ✓ | ✓ | ✓ | admin + repo owner |
| knowledge candidate pending | heti összevont | ✓ | badge | admin/developer |
| billing-események | ✓ | – | ✓ | owner + billing_admin |
| credit 80%/100% | ✓ | ✓ | ✓ | owner + billing_admin |
| provider revoked / index-bukás | ✓ | ✓ | ✓ | admin |

In-app értesítési központ (harang-ikon) későbbi milestone; első körben bannerek + a "Waiting on you" runs-szekció (§9.1) fedi le.

---

## 13. Alapdokumentum-kiegészítések (errata + delta)

### 13.1 State machine

A `needs_action` állapot felvéve (az alapdocban javítva): `needs_input` = csapat-válaszra vár (kérdés-topicok); `needs_action` = külső jogosultsági/konfigurációs beavatkozásra vár (pl. branch protection, provider permission).

### 13.2 Új/módosított DB-táblák

```sql
invitations                    -- §4.3
credit_ledger (id, organization_id, delta, kind,  -- included|overage_purchase|run_charge|bonus|expiry
               run_id, period, created_at)
org_daily_usage (organization_id, date, runs_by_type, credits_used,
                 llm_cost_usd, tokens_in, tokens_out)
data_export_jobs (id, organization_id, requested_by, status, storage_uri, expires_at)
account_deletion_requests (id, user_id, status, execute_after)
notification_preferences (user_id, organization_id, event_class, channel, enabled)
-- módosítás:
knowledge_entries: + status TEXT (candidate|published|deprecated), + deprecated_reason
model_catalog_entries: + pricing_missing BOOLEAN, + pricing_source (curated|provider_api)
billing_entitlements: kulcs-érték entitlementek a §2.3 mátrix szerint + suspended flag
```

### 13.3 Új API-végpontok

```http
# Billing / usage
GET  /api/billing/usage            POST /api/billing/cancel
GET  /api/billing/invoices         POST /api/billing/reactivate
POST /api/billing/credits/purchase

# Members / meghívások (§4.3)        # Account
GET    /api/orgs/{id}/members        GET  /api/me/export
PATCH  /api/orgs/{id}/members/{uid}  DELETE /api/me
DELETE /api/orgs/{id}/members/{uid}  GET/POST/DELETE /api/me/tokens
GET/POST /api/orgs/{id}/invitations  GET/DELETE /api/me/sessions
DELETE /api/invitations/{id}
POST   /api/invitations/accept

# Analytics                          # Knowledge
GET /api/analytics/summary           GET/POST  /api/knowledge/entries
GET /api/analytics/export.csv        GET/PATCH /api/knowledge/entries/{id}
                                     POST /api/knowledge/entries/{id}/approve
# Org lifecycle                      POST /api/knowledge/entries/{id}/reject
POST /api/org/export                 GET  /api/knowledge/export
GET  /api/org/export/{jobId}
DELETE /api/org                      # Runs
                                     POST /api/runs/{id}/rerun
GET/PUT /api/notification-preferences

# Staff (külön authz: is_staff)
GET /staff/api/overview | orgs | orgs/{id} | users | billing | abuse | ops
POST /staff/api/orgs/{id}/entitlement-override | suspend | unsuspend
POST /staff/api/webhook-events/{id}/replay
```

### 13.4 Dashboard route-delta

```text
/o/:org/settings/{general|members|billing|usage|model-policy|model-policy/regions|
                  retention|notifications|api-tokens|audit-log}
/o/:org/knowledge/:entryId
/account/{profile|identities|sessions|tokens|notifications|danger}
/invite/:token
/staff/{overview|orgs|users|billing|abuse|ops}
```

### 13.5 Egyéb rögzítések

- UI nyelv első körben **English-only**; i18n-keret nélkül, de string-externalizálással.
- Mobil: a dashboard tablet-szélességig responsive; telefonon read-only nézetek (runs, artifactok) az elvárás, szerkesztő-felületek nem.
- Accessibility: WCAG AA kontraszt a dark theme-en, teljes billentyűzet-navigáció a táblákban.
- Metrics-bővítés (alapdoc §23-hoz): `credits_charged_total`, `credit_balance`, `stripe_webhook_failed_total`, `dunning_active_total`, `margin_negative_orgs_total`, `invitation_sent_total`, `activation_total`.
- Audit-bővítés: invitation sent/accepted/revoked, member role change, entitlement override (staff), suspend/unsuspend, knowledge approve/reject, data export requested/downloaded, account/org deletion lifecycle.

---

## 14. Nyitott kérdések (terméktulajdonosi döntést igényel)

1. **Credit-árfolyam kalibráció:** a 0.10 USD/credit és a plan-keretek (150/400 credit/seat) launch előtt mért COGS-adatokkal validálandók (M6.4 staging-runok költségméréséből). A §2 számok kiinduló javaslatok, nem véglegesek.
2. **China régió realitása:** mely providerek szolgálhatók ki kínai régió-bucketben (MiniMax/DeepSeek mainland endpointok? compliance?). Javaslat: a policy-keret épüljön meg (EU/USA/China), de a China bucket launchkor "contact us" státuszú legyen.
3. **Éves árazás launchkor vagy később:** javaslat — legyen launchtól (a Stripe-leképezés triviális), de éves csak kártyás self-serve, invoice-os éves Enterprise-only.
4. **Email provider EU-residency:** Resend vs Postmark vs SES eu-west-1 — M6.1 discovery dönti el.
5. **PostHog EU Cloud vs self-host:** induláskor EU Cloud (üzemeltetési teher minimalizálása), self-host csak ha adatkezelési igény kikényszeríti.
6. **DUUMBI-native provider felülete:** az alapdoc szerint elsődleges adapter, de a native workspace-ekre az intake/spec/review/closure trigger- és artifact-felület nincs specifikálva (nincs "issue komment"). Ez a [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]] (M3) kimenetére vár — az M6.0 v2 dokumentumnak kötelezően fel kell oldania.
7. **PR-komment branding elrejtés** Team-en (§2.3) vs csak Business-en: growth (branding mindenhol) vs konverzió kérdése.
