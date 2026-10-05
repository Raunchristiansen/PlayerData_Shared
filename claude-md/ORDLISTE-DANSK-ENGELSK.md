# Trin 0: fælles dansk→engelsk ordliste for #124

**Besluttet af Morten 2026-10-05 ("ja til 124 uanset"). Tre niveauer efter hvem der afgør ordet.**

Listen findes, fordi alle tre platformes planer begynder med den: uden den får
vi tre engelske navne for "kamp". iOS' plan har den som trin **0**, før alt
andet; Androids har det fælles ordforråd "til allersidst, og kun koordineret".

## Metoden, så den kan efterprøves

Kandidaterne er fundet ved at udtrække deklarationer fra alle tre træer på
`origin/HEAD`, dele navnene i ord (camelCase + snake_case), og beholde de ord,
der **hverken** står i `/usr/share/dict/words` (104.334 engelske ord) **eller** i
serverens egne 186 skemaord.

```
INTERVAL
  iOS       120 filer,  6020 deklarationer   (func|var|let|struct|class|enum|protocol|case)
  Android   166 filer,  4941 deklarationer   (fun|val|var|class|object|enum|data class)
  Backend    41 filer,   399 deklarationer   (def|class) — ET GULV, se nedenfor
  resultat  1188 distinkte ikke-engelske ord
```

**Instrumentet overvurderer og undervurderer, og begge retninger er kendte:**

- **Overvurderer:** `repo`, `kode`, `dsl`, `hid` er forkortelser eller
  fragmenter, ikke danske ord. De skal ikke omdøbes.
- **Undervurderer for Backend:** mønsteret fanger kun `def`/`class`, ikke
  variable. Backend målte selv **1347** navne i `app/`, hvoraf 676 danske. Mine
  399 er altså et gulv, og Backend-kolonnen i tabellen nedenfor er for lav.
- **Et "ligner dansk"-mønster er bevidst IKKE brugt.** Android viste 05-10, at
  et sådant signal bliver *mere* upræcist, jo længere arbejdet skrider frem:
  det tæller ikke det, der er tilbage, men det der ligner. "Ikke i den engelske
  ordbog" overvurderer, men kan ikke melde "der er mindre tilbage, end du tror".

## REGLEN DER GOER LISTEN BRUGBAR — tilfoejet 05-10-2026 efter iOS' fund

**iOS maalte, at listen ville lave 456 hybrider, og de standsede foer de koerte
noget.** Koordinatorens maaling bagefter, paa alle tre traeer:

```
                        UDEN boejningsregel        MED boejningsregel
iOS        2633 navne    387 helt / 478 hybrid     463 helt / 475 hybrid
Android    2673 navne    370 helt / 405 hybrid     446 helt / 405 hybrid
Backend     391 navne     57 helt /  91 hybrid      71 helt /  90 hybrid
------------------------------------------------------------------------
           5697 navne    814 helt / 974 hybrid     980 helt / 970 hybrid
```

**En hybrid er `hasGuleCard`, `periodMarkeringMs`, `playerNavn`** — hverken dansk
eller engelsk. Og det farlige er, at **ingen af de tre vagter fanger den:** en
hybrid er ikke dansk, saa ratchet-tallet falder, `aeoeaa` forsvinder, og
"ikke i den engelske ordbog"-maaleren bliver glad.

### Reglen: VAERKTOEJET naegter at lave en hybrid

**Omdoeb et navn KUN hvis HVERT ord i det er daekket.** Er bare ét ord udaekket,
springes HELE navnet over og skrives til en liste, et menneske navngiver i haanden.

```
harGuleKort         har + gule + kort            'gule' udaekket   -> SPRINGES OVER
spillerNavn         spiller + navn               'navn' er DANSK   -> SPRINGES OVER
periodeMarkeringMs  periode + markering + ms     udaekket          -> SPRINGES OVER
```

> **RETTET 05-10-2026.** Foerste udgave havde `spillerNavn -> playerNavn` her som
> et eksempel paa "omdoebes helt", med begrundelsen *"'navn' er ENGELSK"*. **Det
> er en hybrid, og eksemplet var forkert.** iOS fangede det: *"Din tekst siger
> 'navn er ENGELSK -> omdoebes helt': det er en hybrid i min laesning, saa jeg har
> IKKE godkendt navn."*

### HVORFOR det var forkert: begge mine heuristikker laekker dansk

**Maalt 05-10 efter iOS' fund:**

```
serverens 186 skemaord       33 ER DANSKE
  navn · dato · raekke · klub · hjemme · ude · deltager · tid · afbud
  aarsag · spillested · troejenr · kampnr · praefiks · puljeid · sektion
  kampdato · kampinfo · beregnet · delte · dag · dbu · json · veo ...

/usr/share/dict/words        laekker mindst disse danske ord
  er · alt · gang · mange · loft · mine · tag · slip · art
```

**Serverens skemaord var den stoerste fejl.** Jeg regnede dem som daekkede, fordi
serveren er "sandheden" — men **33 af dens kolonnenavne er stadig danske**, og
det er praecis dem, `#156` skal omdoebe. En kolonne, der venter paa at blive
engelsk, er ikke et bevis paa, at ordet ER engelsk.

### LOESNINGEN er iOS' og Androids, ikke min: en GODKENDELSESLISTE

> Et ord taeller kun som engelsk, hvis et MENNESKE har gennemgaaet og godkendt
> det. Alle andre sender navnet til springe-listen.

iOS' `docs/omdoebning-124/godkendt-engelsk.txt` har **63 ord**, gennemgaaet i
haanden. **Ordbogen og serverens skemaord er dermed KANDIDAT-generatorer, ikke
autoriteter** — de foreslaar ord til gennemgang, de afgoer ingenting.

**Det er samme form som alt andet, der har virket i aften:** Backends
godkendelsesliste i `test_feltnavne.py` (fejler lukket), Androids
kollisionsliste (committet, afgjort i haanden), `#95`s sprogvagt (en
godkendelsesliste, ikke en afvisningsliste).

**En afvisningsliste kan aldrig blive faerdig. En godkendelsesliste er faerdig i
det oejeblik, den er gennemgaaet.**

**Det er en egenskab ved vaerktoejet, ikke ved listen.** Derfor kan listen vokse
bagefter uden at noget skal rettes om, og springe-listen er maalbar fremdrift.

**Hvorfor det er bedre end at skrive ordlisten faerdig foerst:** halen er lang.
Af de 349 udaekkede ord rammer **146 kun ÉT navn**, og top 100 ord daekker kun
61 % af hybriderne. Et ord, der optraeder én gang, er ikke en ordforraads-
beslutning — det er navngivningen af den ene ting, og den hoerer i haanden.

### Boejningsreglen — 19 ord, 114 navne

Et ord er daekket, hvis dets **stamme** staar i listen og endelsen er en dansk
boejning. Maalt, de der faktisk forekommer:

**RETTET 05-10-2026 efter Androids fund. Reglen gaelder KUN FLERTAL.**

```
FLERTAL — sikker, boejes efter engelsk regel
  kampe = kamp + e            haendelser = haendelse + er
  spillere = spiller + e      farver     = farve + er
  halvlege = halvleg + e      puljer     = pulje + er
  noegler = noegle + er       perioder   = periode + er
  traeninger = traening + er  stoevner   = stoevne + er

IKKE FLERTAL — hoerer paa SPRINGE-LISTEN, ikke i reglen
  vaelger    handlende navneord ELLER nutid   Selector? Selects?
  henter     nutid                             fetches? Fetcher?
  hentet     kort tillaegsform                 fetched
  banen      bestemt form                      thePitch? onPitch?
  traenings  ejeform                           training's? trainingType?
```

**Androids fund, og det braekker reglens foerste udgave:**

> `-er` er tre forskellige ting paa dansk — `noegler` (flertal), `henter`
> (naevneform), `vaelger` (handlende navneord). Din tabel har
> `vaelger = vaelg + er`, og reglen "boej derefter efter engelsk regel" gav
> `Selects`. Det rigtige er `Selector`. **Reglen er rigtig for flertal og
> tvetydig for de to andre, og den kan ikke afgoere hvilken den ser.**

Derfor: **kun flertal.** De fem oevrige klasser er grammatik, ikke ordforraad, og
et menneske navngiver dem.

### "Allerede engelsk" — ordbogen, med en fjendeliste

Et ord er daekket, hvis det staar i `/usr/share/dict/words` (104.334 ord). Det
daekker `start`, `position`, `form`, `live`, `slot`, `type`, `id`, `ms`, `km`,
`api`, `min` — og det er grunden til, at koordinatorens maaling gav 446 og
Androids 176: **de taelte ikke ordbogen med.**

**MEN ordbogen har falske venner, og den foerste er maalt:**

```
er    staar i /usr/share/dict/words (den engelske toevelyd) — OG er dansk
```

**En blind ordbogsregel giver `erEgetHold -> erOwnTeam`**, praecis den hybrid
Androids foerste vaerktoejsudgave lavede. Derfor:

```
FALSKE VENNER — i ordbogen, men dansk. Behandles som UDAEKKEDE:
  er · til · paa · han · hun · den · det · som · ved · var · har
```

Listen voksede ud af én maaling og er sandsynligvis ikke komplet. **Vokser den,
er det en rettelse, ikke en fejl** — og et ord paa springe-listen koster et
menneske fem sekunder, mens en hybrid koster en omdoebning mere.

### KOLLISIONER — ord hvis engelske oversaettelse ikke kan staa som et navn

**Vaerktoejet SKAL stoppe paa disse og skrive dem til en kollisionsliste, som
platformen afgoer i haanden og committer.** Listen er iOS' fund plus
koordinatorens efterproevning:

| dansk | listen siger | hvorfor den ikke kan staa |
|---|---|---|
| pause | `break` | **RESERVERET NOEGLEORD** i Swift, Kotlin OG Python |
| saet | `set` | Swift property-setter · Kotlin soft keyword · Python builtin |
| krop | `body` | SwiftUI `View.body` |
| liste | `list` | Python builtin · SwiftUI `List` |
| tekst | `text` | SwiftUI `Text` |
| fra | `from` | **RESERVERET** i Python |
| til / paa | `to` / `on` | kontekstuelt — `on` kolliderer med Compose-modifiers |

**`pause -> break` kom fra serveren** (`age_rules.has_break`) og er rigtig DER.
**Et ord, der er afgjort paa ledningen, er ikke dermed afgjort i koden** — det
var en graense, listens foerste udgave slet ikke havde.

### Og hvad der IKKE maa goeres

**Tilfoej ikke ord selv.** `kampe`, `spillere`, `haendelser` er daekket af
boejningsreglen; de oevrige 349 kommer i en udvidelse, der gennemgaas. En
platform, der selv vaelger et ord, laver praecis det problem, listen findes for
at loese: tre engelske navne for det samme.

**Og et symbol-uopmaerksomt vaerktoej er en risiko, iOS flagede:** SwiftSyntax
loeser ikke symboler, saa omdoebningen sker efter STAVNING. Et lokalt navn i et
andet lag med samme stavning foelger med. **Byg en spaerring mod det, foer
vaerktoejet koerer.**

## A. Afgjort af serveren — ingen beslutning, kun efterprøvning

**Fire af tabellens seksten `tabel.kolonne`-henvisninger var forkerte i første
udgave**, fundet ved at slå hver enkelt op med `PRAGMA table_info` frem for at
skrive dem fra hukommelsen: `kort_status` findes slet ikke som tabel,
`kamp_gps_data` hedder nu `match_gps_data` (`#144`), `yellow_card_minutes` sidder
i `format_rules` og ikke i `age_rules`, og `sessions` har ingen `venue`-kolonne.
Tabellen nedenfor er den rettede. **Slå dem op igen, hvis du bruger listen efter
`#156`** — den flytter 23 kolonnenavne mere.

**Princippet er Androids** (05-10): *for de ord, serveren allerede har et
feltnavn til, er valget truffet.* Det er stærkere end en forhandlet liste, fordi
begge klienter læser de felter i forvejen, og fordi det kan efterprøves.

| dansk | engelsk | serverens bevis |
|---|---|---|
| kort | `card` | `format_rules.red_card`, `format_rules.yellow_card_minutes`, `age_rules.has_yellow_cards` |
| bane | `pitch` | `sessions.started_on_pitch` |
| pause | `break` | `age_rules.has_break` |
| kamp | `match` | `match_number_prefix`, `follow_match_end_notifications` |
| spiller | `player` | `match_gps_data.player_name` |
| hændelse | `event` | `event_id`, `event_name` |
| nøgle | `key` | `absence_reason_key`, `activity_type_key`, `field_key` |
| modstander | `opponent` | `sessions.opponent` |
| halvleg | `half` | `active_half_number`, `half_length_min` |
| mål (scoring) | `goal` | `follow_goal_notifications` |
| farve | `color` | `shirt_color` |
| stævne | `tournament` | `tournament_id`, `tournament_name` |
| periode | `period` | `period_minutes`, `period_times_json` |
| hold | `team` | `sessions.team` |
| status | `status` | `status`, `selection_status`, `status_key` |
| årsag | `reason` | `absence_reason_key` |
| antal | `count` | `halves_count` |
| visning | `display` | `activity_types.display_da` |
| valg / udvalgt | `selection` | `form_rules.selection_status` |
| adgangskode | `password` | `password_hash`, `password_salt` |
| pulje | `group` | `dbu_groups.age_group`, `age_rules.age_group` |
| træning | `training` | **en VÆRDI, ikke en kolonne:** `activity_types.category` = `training` |
| varighed | `duration` | `session_duration_min`, `play_duration_min` |
| spillested | `venue` | `shared_matches.venue`, `shared_tournaments.venue` — **men `dbu_matches.spillested` er stadig dansk** (`#156`) |

**Og én, serveren allerede har SPLITTET — den vigtigste i tabellen:**

| dansk | engelsk | hvorfor to |
|---|---|---|
| opstilling | `lineup` **eller** `formation` | `match_lineup` er TABELLEN (hvilke spillere), `match_lineup.formation` er KOLONNEN (4-4-2). Ét dansk ord, to engelske begreber — præcis det, tre sessioner ville gætte forskelligt |

## A2. AFGJORT AF SERVEREN — tilfoejet 05-10-2026 kl. 22:45

**Maalt, ikke valgt.** Alle tre platforme havde maalt disse ord som DANSKE
(`ORD-ENGELSK-DANSK.md`), men ingen af dem havde en engelsk maalvaerdi. Serveren
har den.

**Princippet er Androids:** *for de ord, serveren allerede har et feltnavn til,
er valget truffet.* Og det kan efterproeves med et `PRAGMA table_info`-opslag
frem for forhandles.

| dansk | engelsk | serverens bevis |
|---|---|---|
| afbud | `absence` | `sessions.absence_reason_key`, `absence_reasons.legitimate_absence` |
| alder | `age` | `age_rules.age_group`, `dbu_groups.age_group` |
| dato | `date` | `sessions.date`, `group_formats.confirmed_date` |
| gule | `yellow` | `age_rules.has_yellow_cards`, `format_rules.second_yellow` |
| halve | `half` | `match_halftime_state.active_half_number` — **men se advarslen nedenfor** |
| hjemme | `home` | `dbu_club_colors.home_shirt` |
| klub | `club` | `dbu_club_colors.dbu_club_name` |
| navn | `name` | `dbu_groups.division_name`, `dbu_club_colors.dbu_club_name` |
| positioner | `position` | `position_minutes.position` (flertalsreglen: `positions`) |
| praefiks | `prefix` | `activity_types.match_number_prefix` |
| raekke | `division` | `dbu_groups.division_name` |
| sendt | `sent` | `match_end_push_sent.sent_at` |
| slut | `end` | `families.follow_match_end_notifications` |
| standard | `default` | `age_rules.default_format` |
| tal | `number` | `activity_types.match_number_prefix` |
| tid | `time` | `match_halftime_state.extra_time` |
| trin | `step` | `match_gps_data.step_balance_l` |
| troeje | `shirt` | `dbu_club_colors.home_shirt` |
| typer | `type` | `activity_types.legacy_type` (flertal: `types`) |
| ude | `away` | `dbu_club_colors.away_shirt` |

**ADVARSEL om `halve`:** Backend maalte, at `halve_op` er en **afrundingsregel**
("halve op"), ikke "to halve". `halve -> half` gaelder KUN naar ordet handler om
en halvleg. **Et navn med `halve` i en afrundingssammenhaeng hoerer paa
springe-listen.** Samme form som `sort`: ordet kan ikke afgoeres globalt.

Og `standard` er i samme klasse: Androids fund var, at `STANDARD_FARVE` er
**standardfarven** (default), og det passer med serverens `default_format`. Men
ordet laeser som engelsk "standard", og det er praecis derfor det er farligt.

## A3. STADIG UDEN EN ENGELSK MAALVAERDI — 19 ord

**Serveren navngiver dem ikke, og de er ikke afgjort.** Et navn, der indeholder
et af dem, hoerer paa springe-listen, indtil nogen afgoer ordet:

```
art · deltager · fri · frisk · handling · knap · lager · linje · mangler
ny · prognose · rang · registrer · skift · slip · stilling · tag · tom
```

**Fem af dem har ÉT plausibelt engelsk ord** og kan afgoeres uden Morten, naar
nogen tager dem: `handling -> action`, `knap -> button`, `linje -> line`,
`ny -> new`, `tom -> empty`, `mangler -> missing`.

**Og fire er reelt tvetydige:**

```
stilling    standings (tabellen) ELLER position (paa banen)?
art         kind ELLER species? — bruges om haendelsestypens ART
rang        rank — men serverens egen _HAENDELSE_RANG er ogsaa dansk
tag         take ELLER tag (et maerkat)? Androids maaling: dansk "tag"
```

**De fire hoerer ikke paa denne liste, foer nogen har laest deres kaldesteder.**

## A4. AFGJORT AF KODEBASENS EGEN BRUG — tilfoejet 05-10-2026 kl. 23:05

**Backend foreslog disse seks med bevis fra deres EGNE kaldesteder, ikke fra en
oversaettelse.** Koordinatoren efterproevede hvert enkelt. **Samme princip som
A2, bare med koden som autoritet frem for skemaet:** ordet er allerede i brug
paa engelsk et andet sted i samme kodebase, saa valget er truffet.

| dansk | engelsk | bevis, efterproevet |
|---|---|---|
| faelles | `shared` | tabellerne `shared_matches` (39 forekomster) og `shared_tournaments`, `#144` |
| skift | `change` | `PositionChangeIn` (9 forekomster), `_position_change_fra_lager` |
| nulstil | `reset` | ruten `/api/auth/reset-password`, tabellen `password_reset_tokens` |
| tjek | `check` | ruterne `/player-check` og `/lineups/check-now` |
| felter | `fields` | `/api/v1/stats/nullable-fields`, `nullable_fields.py`, tabellen `form_fields` |
| deltager | `participant` | `DeltagerIn.participant` fra `#156` trin 1, 19 forekomster |

**Hvorfor det er en MAALING og ikke et valg:** hvert ord har allerede en engelsk
modpart i brug. **At vaelge et andet ord ville skabe to engelske navne for eet
begreb** — praecis det, Mortens regel fra `#83` forbyder: *to begreber maa ikke
dele eet ord, og eet begreb maa ikke have to ord.*

**Og `deltager` er den fineste:** `#156` trin 1 gav request-body-feltet
`deltager` en engelsk tvilling `participant` faa timer foer. **Ordet var afgjort
paa ledningen, foer nogen spurgte om koden** — og det er den omvendte retning af
`pause -> break`, hvor ledningens svar IKKE kunne staa som et navn.

### A4b — tre mere, 05-10-2026 kl. 23:10

| dansk | engelsk | bevis, efterproevet |
|---|---|---|
| aktivitet | `activity` | tabellen `activity_types` (142 forekomster), `activity_type_key` (69), `#37` |
| regler | `rules` | TRE tabeller: `form_rules` (102), `age_rules` (40), `format_rules` (24) — `#44`/`#53` |
| nullable | `nullable` | **allerede engelsk.** Tre UDRULLEDE endepunkter, alle bekraeftet registreret i den koerende container: `/api/v1/stats/nullable-fields`, `/api/v1/sessions/nullable-fields`, `/api/v1/live/nullable-fields` |

**`regler -> rules` lukker Androids stammeskift-hul.** De maalte, at
`regel -> regler` taber et `e`, saa boejningsreglen (stamme + endelse) ikke kan
se det:

```
boejningsreglen   regel + er?   nej — stammen er 'regl', ikke 'regel'
loesningen        'regler' staar som sin EGEN post, med 'rules' som maal
```

**En boejning, reglen ikke kan se, skal staa som sit eget ord.** Det er
billigere end at udvide reglen med stammeskift-regler for dansk — og det er
maalbart, fordi serveren har flertalsformen i tre tabelnavne.

**Og `nullable` er den foerste post, hvor dansk og engelsk er SAMME ord.** Den
hoerer i mappingen alligevel, fordi et vaerktoej ellers ser `nullable` som
"ukendt" og springer hele navnet over. **En post, der oversaetter et ord til sig
selv, er ikke stoej — den er en GODKENDELSE.**


## B. Entydige — jeg foreslår, ingen beslutning nødvendig

Ingen af disse har to plausible engelske ord i denne kodebase.

```
tekst       text          fejl        error         svar        response
besked      message       titel       title         liste       list
krop        body          konto       account       lås         lock
fra         from          til         to            på          on
har         has           eget        own           vis         show
vælg        select        hent        fetch         slet        delete
sæt         set           indlæs      load          gem         save
```

**`svar` → `response`, ikke `answer`:** det er altid et HTTP-svar i denne
kodebase, aldrig et svar på et spørgsmål.

## C. Målt, ikke spurgt — og det ene, der er farligt

**Jeg havde tre spørgsmål til Morten her. Alle tre kunne måles, så de er væk.**
Det eneste, der blev tilbage, er vigtigere end spørgsmålene var.

### C1. `kort` betyder også `short` — to navne, målt

```
afkortet     iOS       = shortened
forKort      Android   = too short
```

Resten af de 84 (iOS) og 67 (Android) `kort`-navne er kortet som i gult/rødt.
`kodeKort` og `KortTekst` er kort med en kode/tekst **på**, ikke korte ting.

### C2. `hold` rammer SEKS andre betydninger som delstreng

**Dette er det vigtigste i hele listen.** En mekanisk erstatning af `hold → team`
ville producere `Inteam`, `beteamt`, `Placeteamer`, `opteam`, `teamer`,
`forteam` — og hvert enkelt ville kompilere eller ikke kompilere på en måde, der
ikke peger på årsagen.

> **ADVARSEL — DETTE ER IKKE EN MAPPING.** Listen nedenfor er ord, der
> **IKKE må omdøbes.** De er delstrenge, hvor `hold` betyder noget andet. Android
> fandt 05-10, at en parser kan læse en tretrins-tabel som en oversættelse:
> `indhold` endte i deres ordbog som `content` og gjorde `Indhold` til et
> "dækket" navn. **Derfor står de nu som prosa uden en engelsk kolonne.**

**`indhold`** — 13 navne (`Indhold`, `OpstillingIndhold`, `FoelgKampIndhold`).
**`holder`** — 6 navne, låsens ejer (`holderNavn`, `_laas_holder`).
**`placeholder`** — 2 navne, og det er et engelsk ord (`PlaceholderFane`).
**`beholdt`** — 1 navn. **`ophold`** — 1 (`UDKAST_OPHOLD_MS`).
**`forhold`** — 1 (`BANE_FORHOLD`).

**Ingen af de seks har en oversættelse i denne liste.** De er udækkede, og
hybrid-reglen skal springe navne, der indeholder dem, over.

`Indhold` er særligt værd at bemærke: **det er netop den type, iOS' `#164`-fund
handler om** — det gemte Face ID-login i Keychain. Et mekanisk `hold → team`
havde omdøbt den OG slettet testernes logins, tavst.

**Reglen, der følger af det:** `hold → team` kun på et **helt ord** i en
camelCase-/snake_case-opdeling, aldrig på en delstreng. Det er præcis Androids
fælde 1 i deres egen plan (*"korte danske ord er delstrenge af andre ord"*) —
og nu målt med navne.

Og `holder` fortjener sit eget ord, fordi serveren bruger begrebet: låsens ejer.
**`holder → holder`** (uændret, det er engelsk) eller `owner`. Serverens 186
skemaord indeholder allerede `holder`.

### C3. `tilstand` — jeg afgør den, det er en navnekonvention

`status` kun hvor det er **deltagelsesstatus** (`Udtaget`/`Deltog`/`Afbud`),
fordi serveren allerede ejer det ord der (`status`, `status_key`,
`selection_status`). `state` alle andre steder — iOS' `KortTilstand`,
`LiveAktivitetTilstand`, Androids `kortTilstande` er UI-tilstand.

Det er ikke en produktbeslutning, og derfor ikke Mortens. Siger en platform, at
den ikke holder i deres kode, måler vi igen.

## Hvad listen IKKE dækker

- **Ordenes brug i de 1143 øvrige kandidatord.** Listen dækker de hyppigste;
  halen er lang og mest prosa.
- **Om et nyt engelsk navn er et GODT navn**, eller om to begreber fejlagtigt
  får samme navn. Backends plan siger det samme: det afgør kun et menneske.
- **Backends variabelnavne.** Se intervallet ovenfor — mit tal er et gulv.
- **Værdier i data.** `sessions.status` indeholder `'Udtaget'` som DATA, og det
  er `#141`/`#156`, ikke `#124`.
