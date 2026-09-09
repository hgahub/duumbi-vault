# DUUMBI Loop Intake - vizualis komponensek

Forras: `duumbi-loop-intake-v2.html`

## Cel

Az intake oldal celja, hogy a felhasznalo gyorsan megertse, mit ismert fel a rendszer a kiindulo otletbol vagy feladatbol, milyen hianyok akadalyozzak a jo specifikaciot, milyen kerdesekre kell valaszolnia, es milyen tudast erdemes elmenteni a DUUMBI Loop tudastaraba.

## Komponensek

| Komponens | Miert van a kepernyon | Mit ad a felhasznalonak |
|---|---|---|
| DUUMBI sidebar es logo | Allando termekkornyezetet ad, es jelzi, hogy a felhasznalo a Loop munkafolyamaton belul van. | Tajekozodasi pontot ad; a felhasznalo tudja, melyik termekben es workspace-ben dolgozik. |
| Aktiv Intake navigacios elem | Kiemeli, hogy a jelenlegi lepes az intake, nem spec, review vagy knowledge oldal. | Csokkenti a kontextusvesztest; a felhasznalo latja, hol tart a folyamatban. |
| Felso breadcrumb | Megmutatja a szervezeti es oldalszintu helyet: `hgahub / loop / intake`. | Pontos helyzetjelzest ad, kulonosen tobb org vagy workspace eseten. |
| Felso akciok: Save draft, Ask DUUMBI to refine, Spec locked | A legfontosabb globalis muveleteket tartja szem elott. | A felhasznalo menthet, AI-segitseget kerhet, es latja, hogy a spec meg nem indithato. |
| Fo task hero: "DUUMBI found this task" | Az oldal legfontosabb tartalma: mit ertett meg az AI a bejovo otletbol. | Azonnali, emberi nyelvu visszajelzest ad; a felhasznalo gyorsan ellenorizheti, hogy jo iranyba ertette-e a rendszer. |
| Plain-language task blokk | Kiemelt, vizualisan eros osszefoglalo a feladatrol. | Egy mondatban megfogja a lenyeget, igy a felhasznalonak nem kell artifactokbol osszeraknia a celkituzest. |
| Allapotkartya: Needs user input, Native Loop | Megmutatja a futas aktualis allapotat es azt, hogy ez DUUMBI-native flow, nem csak GitHub/GitLab adapter. | Vilagossa teszi, hogy a rendszer var a felhasznalora, es hogy a spec elott meg dontes kell. |
| Main risk sor | A legfontosabb kockazatot nem rejti el: a spec korai lenne dontesek nelkul. | A felhasznalo megerti, miert nem eleg csak tovabblepni; valodi minosegi ok van a blokkolas mogott. |
| Best next step sor | Egyetlen kovetkezo lepesre szukiti a figyelmet. | Csokkenti a dontesi terhelest; a felhasznalo tudja, mit kell eloszor csinalnia. |
| Spec locked CTA | A spec inditasat lathatova, de zaroltta teszi. | Vilagossa teszi az osszefuggest: valaszok nelkul nincs jo specifikacio. |
| Negylepeses flow rail | A teljes intake utat egyszeruen mutatja: understand -> close gaps -> save knowledge -> generate spec. | Mentaleis terkepet ad, szamok es statusz-zaj nelkul. |
| "What DUUMBI understood" szekcio | Harom rovid kartyaban osszefoglalja, mit gondol a rendszer a user goalrol, workflowrol es knowledge rule-rol. | Gyorsan ellenorizheto, hogy az AI ertelmezese helyes-e. |
| User goal kartya | A felhasznaloi celra forditja le a feladatot. | Segit eldonteni, hogy az intake valoban a vegfelhasznalo szempontjabol kozelit-e. |
| Core workflow kartya | A munkafolyamat lenyeget roviden mutatja. | Megerositi, hogy az oldal nem dashboard, hanem dontesi es tisztazasi munkater. |
| Knowledge rule kartya | Elkuli az elfogadott es candidate tudast. | Ved a rossz minosegu, ellenorizetlen tudas tartos bekerulese ellen. |
| "Gaps to close before spec" szekcio | A hianyokat nem altalanos figyelmezteteskent, hanem konkret dontesi pontkent mutatja. | A felhasznalo pontosan latja, mit kell utananezni, pontositani vagy kidolgozni. |
| Gap kartyak | Egy-egy hianyt tematizalnak: approval, citation policy, visual evidence. | A hianyok kezelheto darabokra bomlanak; nem tunnek kaotikusnak. |
| Source linkek a gap kartyakon | A hianyhoz kozvetlenul kapcsolt forrast adnak. | A felhasznalo nem magara marad a kutatassal; tudja, honnan induljon. |
| "Questions that improve the spec" szekcio | A kerdeseket nem adminisztrativ mezokent, hanem gondolkodasi segedeszkozkent mutatja. | A kerdesek ravezetnek a lenyegi problemakra, es segitenek jobb specifikaciot letrehozni. |
| "Why it matters" magyarazatok | Minden kerdeshez megadjak, miert szamit a valasz. | A felhasznalo nem csak valaszol, hanem erti is, milyen dontest hoz. |
| Textarea valaszmezok | Helyet adnak a felhasznalo donteseinek es pontositasainak. | Az intake nem csak olvashato, hanem alakithato. |
| Mark answered gomb | A felhasznalo lezartta teheti a kotelezo kerdeseket. | Lathato haladast ad, es kozvetlenul kapcsolodik a spec unlock allapothoz. |
| Ask AI to propose answer gomb | AI-segitseget ad, ha a felhasznalo nem tudja, hogyan valaszoljon. | Inspiraciot ad, csokkenti az ures lap problemat. |
| "Your path to a good spec" checklist | A felhasznalo sajat teendoit sorolja, nem rendszerstatisztikakat. | Egyertelmu akciotervet ad: mit kell megtenni a jo spec elott. |
| Checklist allapotok | Kipipalhato munkalepeseket adnak. | A felhasznalo erzi a haladast, es nem vesz el az informaciok kozott. |
| "Suggested sources" szekcio | Keves, valogatott, a konkret gapekhez kotott forrast mutat. | Kutatasi tamaszt ad, de nem terheli tul a felhasznalot hosszu forraslistaval. |
| Source kartyak | Megmondjak, melyik forras mire valo. | A felhasznalo gyorsan eldontheti, melyik dokumentumot kell megnyitnia. |
| "Knowledge to save" szekcio | Megmutatja, milyen felismeresek valhatnak tartos tudassa. | A felhasznalo latja, hogy az intake eredmenye nem egyszeri output, hanem bovitheti a Loop tudastarat. |
| Candidate knowledge kartyak | Egyertelmuen jelolik, hogy ezek meg nem elfogadott tudaselemek. | Megorzi a minosegi hatart az otlet, kutatasi jegyzet es elfogadott tudas kozott. |
| Save candidate input | Uj felismeres mentheto candidate tudaskent. | A felhasznalo kutatas kozben bovitheti a tudastar jelolt elemeit. |
| Badge-ek | Kicsi, tomor statuszjelzesek: Required, Candidate, Native Loop, Needs user input. | Gyors szkennelesi pontokat adnak anelkul, hogy nagy szamokkal terhelnek az oldalt. |
| Sotre grid hatter es panelrendszer | Megtartja a DUUMBI Loop technikai, munkafeluletehez illo karakteret. | Stabil, professzionalis kornyezetet ad, mikozben a tartalom marad a fokuszban. |
| Serif cimtipografia | A legfontosabb szekcioknak erosebb hierarchiat ad. | Konnyebben befogadhato, magazinosabb, kevesbe tabla-szeru olvasasi ritmust ad. |
| Mono mikroszovegek | Technikai metaadatokat es statuszokat kulon vizualis hangon kezel. | Elvalasztja a rendszerinformaciot a lenyegi emberi tartalomtol. |

## Tervezesi allitas

Az oldal akkor mukodik jol, ha a felhasznalo eloszor nem adatokat lat, hanem egy ertheto feladatot. Ezutan csak azokat a hianyokat, kerdeseket, forrasokat es teendoket kapja meg, amelyek kozelebb viszik a jo specifikaciohoz.

Ezert a v2 oldal szandekosan kevesebb metrikat, kevesebb artifact-statuszt es kevesebb parhuzamos infot mutat. A hangsuly a megertes, pontositas, forrasolt kutatas es tudastarba mentheto tanulsagok korul van.
