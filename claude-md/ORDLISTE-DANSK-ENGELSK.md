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

### SAMMENSAT-REGLEN — tilfoejet 05-10-2026 kl. 23:45

**Dansk skriver sammensatte ord i EET ord. Camel­Case og `_` kan ikke dele dem.**
Androids fund: *"navne med sammensatte smaabogstaver (kamptype, kampnr) rammes
ikke."*

```
aktivitetstyper   hverken _ eller camelCase deler den
aldersregler      men BEGGE dele staar i mappingen
```

**Reglen: laengste-match-foerst mod mappingens egne noegler, med et tolereret
BINDE-S.**

```python
noegler = sorteret(MAP, efter laengde, laengste foerst)
for k in noegler:
    for binde in ('', 's'):          # aktivitetS+typer, alderS+regler
        if ordet starter med k+binde:
            del resten op paa samme maade
```

**Og valideringen er staerk: reglen reproducerer serverens EGNE navne.**

| dansk, eet ord | reglens split | resultat | serveren har |
|---|---|---|---|
| `aktivitetstyper` | aktivitet + typer | `activity_types` | **TABELLEN `activity_types`** |
| `aldersregler` | alder + regler | `age_rules` | **TABELLEN `age_rules`** |
| `holdliste` | hold + liste | `team_list` | **FILEN `team_list.py`** |
| `kamphaendelser` | kamp + haendelser | `match_events` | — |
| `holdkort` | hold + kort | **se A5b** | **SAMMENSAT gav det rigtige svar for det ENE begreb:** `hold -> team` + `kort -> card` = `team_card`, som er korrekt for statistik-kortet. For DBU-holdkortet er det engelske begreb ÉT ord, `teamsheet`. To begreber, eet dansk ord — se A20 |

**De tre foerste er regnet UDEN at se paa serveren, og de rammer det, serveren
allerede hedder.** En regel, der genskaber et navn, nogen har valgt i haanden,
er ikke et gaet.

**Laengste-match-foerst er noedvendigt**, ikke en optimering: `hold` og
`holdkort` staar begge i mappingen, og et korteste-match ville dele `holdkort`
som `hold` + `kort` selv naar posten `holdkort` findes. **Den laengste post
vinder, fordi den er den mest specifikke.**

**Hvad reglen IKKE kan:**

- **Et binde-s, der ogsaa er en ordendelse.** `alders` kunne vaere ejeform af
  `alder`. Reglen tolererer det, fordi resultatet er det samme — men et ord, hvor
  `s` hoerer til stammen, ville deles forkert. **Ingen fundet endnu; det er en
  graense, ikke et bevis.**
- **Et sammensat ord, hvis dele IKKE er i mappingen.** Det hoerer stadig paa
  springe-listen.
- **Tre-dels-sammensaetninger** er proevet og virker (`kamp+gps+data`), men
  hver ekstra del ganger risikoen for et forkert split.

### RETTELSE 06-10 kl. 00:05 — hver del skal vaere **DANSK**, ikke blot daekket

Androids maaling af reglen paa deres eget trae gav **`kropper -> bodyPer`**:

```
kropper  ->  krop + per      begge dele er i mappingen
                             `per` er et GODKENDT ENGELSK ord
```

**Alle dele var daekkede, og intet i hybrid-reglen kunne se noget forkert.**
Det er praecis koordinatorens `_to_int -> _plausible_int` fra samme nat, hvor
alle oevrige ord var engelske og derfor passerede.

**Reglen kraever nu, at hver del er DANSK.** Et sammensat dansk ord er sat
sammen af danske dele — en engelsk del betyder, at opdelingen er forkert, ikke
at ordet er halvt oversat.

```
krop + per        AFVIST   'per' er engelsk -> opdelingen er forkert
alder + regler    OK       begge danske     -> age_rules
hold + kort       OK       begge danske     -> team_card
```

### OG BOEJNINGEN SKAL PROEVES PAA STAMMEN, IKKE PAA HELE ORDET

Androids to, der slap forbi deres foerste rettelse:

```
spillerRaekker -> playerDivisions      KAN_IKKE_AFGOERES blev tjekket paa
skifte         -> changes               HELE ordet, ikke paa STAMMEN
```

`raekker` og `skifte` er boejninger af `raekke` og `skift`, som begge staar i
A5b. **En spaerring, der kun kender grundformen, er aaben for hver boejning af
det samme ord** — og boejningsreglen loeber FOER opslaget, saa den naar det
foerst.

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
| til | `to` | / `on` | kontekstuelt — `on` kolliderer med Compose-modifiers |
| paa | `on` | / `on` | kontekstuelt — `on` kolliderer med Compose-modifiers |

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
| haendelse | `event` | `event_id`, `event_name` |
| noegle | `key` | `absence_reason_key`, `activity_type_key`, `field_key` |
| modstander | `opponent` | `sessions.opponent` |
| halvleg | `half` | `active_half_number`, `half_length_min` |
| maal (scoring) | **se A5b** | `follow_goal_notifications` afgoer SCORINGS-betydningen til `goal`. **Men `maal` betyder ogsaa maaling/maalsaetning** (`target` 12 forekomster i serveren), saa ordet maa ikke staa som en parsebar post. Se A5b |
| farve | `color` | `shirt_color` |
| stoevne | `tournament` | `tournament_id`, `tournament_name` |
| periode | `period` | `period_minutes`, `period_times_json` |
| hold | `team` | `sessions.team` |
| status | `status` | `status`, `selection_status`, `status_key` |
| aarsag | `reason` | `absence_reason_key` |
| antal | `count` | `halves_count` |
| visning | `display` | `activity_types.display_da` |
| valg | `selection` | | `form_rules.selection_status` |
| udvalgt | `selection` | | `form_rules.selection_status` |
| adgangskode | `password` | `password_hash`, `password_salt` |
| pulje | `group` | `dbu_groups.age_group`, `age_rules.age_group` |
| traening | `training` | **en VÆRDI, ikke en kolonne:** `activity_types.category` = `training` |
| varighed | `duration` | `session_duration_min`, `play_duration_min` |
| spillested | `venue` | `shared_matches.venue`, `shared_tournaments.venue` — **men `dbu_matches.spillested` er stadig dansk** (`#156`) |

**Og én, serveren allerede har SPLITTET — den vigtigste i tabellen:**

| dansk | engelsk | hvorfor to |
|---|---|---|
| opstilling | **se A5b** | `match_lineup` er TABELLEN (hvilke spillere), `match_lineup.formation` er KOLONNEN (4-4-2). Eet dansk ord, to engelske begreber — praecis det, tre sessioner ville gaette forskelligt. **Maalvaerdien er bevidst IKKE et parsebart felt her**: raekken stod foer som `` `lineup` **eller** `formation` ``, og ethvert parse tog den foerste backtick-gruppe og laeste `opstilling -> lineup` som afgjort. Det er kilden til koordinatorens eget `get_opstillinger -> get_lineups`-forslag 06-10 kl. 00:00 |

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
| praefiks | `prefix` | `activity_types.match_number_prefix` |
| sendt | `sent` | `match_end_push_sent.sent_at` |
| slut | `end` | `families.follow_match_end_notifications` |
| standard | `default` | `age_rules.default_format` |
| tid | `time` | `match_halftime_state.extra_time` |
| trin | `step` | `match_gps_data.step_balance_l` |
| troeje | `shirt` | `dbu_club_colors.home_shirt` |
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


### A4c — fire mere, 05-10-2026 kl. 23:35

| dansk | engelsk | bevis, efterproevet |
|---|---|---|
| familie | `family` | `family_id` i 587 forekomster, kolonner i `auth_sessions`, `password_reset_tokens` m.fl. |
| soeg | `search` | 20 forekomster, ruten `/club-colors/search` |
| fejlede | `failed` | 15 forekomster, ruten `/club-colors/failed` |
| funktion | `feature` | **IKKE `function`** — se nedenfor |

**`funktion -> feature` er den laererige.** Koordinatoren soegte foerst efter
`function` og fandt **nul forekomster** — og var paa vej til at kalde ordet
uafgjort.

**Men ordet betyder noget andet, end det ser ud til.** `admin.py:399`s
`saet_funktion` serverer ruten `/features/{feature_key}` med kroppen
`FunktionsKnapIn`, og docstringen siger: *"Slaar en central funktions-knap
til/fra for ALLE klienter."*

```
funktion   = en FEATURE FLAG, ikke en funktion i kodebetydningen
serveren   har allerede features-tabellen og feature_key
```

> **Jeg oversatte ordet bogstaveligt i stedet for at laese, hvad det betyder.**
> Et dansk ord har ikke een engelsk modpart — det har den, konteksten giver det.

Det er samme fejlform som resten af natten, nu paa en oversaettelse: **jeg maalte
et ORD og konkluderede om et BEGREB.** Rettelsen kostede ét `git grep -A10`.

**Og `saet_funktion` kan stadig ikke omdoebes**, fordi `saet` staar blandt de
uafgoerlige (`set` er en Python-builtin OG kontekstuelt). `funktion`s afgoerelse
frigiver altsaa ikke netop det navn — men den frigiver ordet.


## A5. AFGJORT AF SERVEREN — 05-10-2026 kl. 23:55, prioriteret efter iOS' maaling

**Kilden til prioriteringen er iOS' egen optaelling**, ikke mit gaet:
`docs/omdoebning-124/frigoer-flest-navne.md` — *"237 navne er EET ord fra at
kunne omdoebes; 167 ord."* Kun navne med praecis EET blokerende ord taelles, saa
afgoerelsen af det ene ord frigiver navnet.

**Hvert ord herunder er slaaet op i serverens egen kode, ikke i en ordbog.**
Kolonnen "bevis" er det, en `git grep` i `backend/app/` svarer.

**FIRE PARSER-FEJL ER MAALT PAA DENNE FIL, 05-10/06-10. Laes dem foer du
skriver en femte** — tre var Androids, een var koordinatorens, og **ingen af dem
meldte en fejl.** De gav alle et TAL, der lignede et svar:

```
koordinatoren  prosalinjen laest som data        7 falske par, paa -> et
                                                 overskrev den rigtige paa -> on
Android        antalskolonnen i A5               74 -> 83   nul raekker laest
Android        header-filter paa ORDET "navn"    et RIGTIGT ord udeladt tavst
Android        set("") <= set("-: ")             83 -> 78   hver tabels SIDSTE
                                                 raekke laest som overskrift
```

**Den tredje og fjerde er de lumske.** Et filter skrevet for at springe
OVERSKRIFTER over udelukkede ordet `navn`, fordi overskriften hedder det samme —
*en tabel skal kendes paa sin FORM, ikke paa sine ord.* Og **den tomme maengde er
delmaengde af alt**, saa en tom linje efter en tabels sidste raekke bestod den
strukturelle proeve.

Androids egen note: *"Tallet gik fra 83 til 78, mens det LIGNEDE en stramning."*
Det blev kun fundet, fordi de laeste de 78 igennem og savnede `bekraeft`.

**TIL DEN, DER PARSER FILEN: A5's foerste kolonne er et ANTAL, ikke et ord.**
De oevrige afsnits tabeller har dansk i foerste kolonne. Et moenster, der antager
det, laeser **nul** af de atten raekker herunder — og nul ser ud som "ingen nye
ord", ikke som en fejl.

```python
# tolererer baade formen med og uden antalskolonne
r'^\|\s*(?:\**\d+\**\s*\|\s*)?([a-zaeoeaa ()/]+?)\s*\|\s*`([a-z_]+)`'
```

**Moensteret er proevet ordret mod filen 06-10 kl. 00:20: 82 par, 72 distinkte
danske ord, NUL ord med mere end eet engelsk maal** (A5b udeladt, som den skal
vaere). Proev det igen efter en redigering — det tager fem sekunder og er den
eneste kontrol, der fanger et format, der er gledet.

To tidligere maalinger paa samme fil: 71 par foer antalskolonnen blev tolereret,
78 da antallet var FED i nogle raekker og bart i andre. **Den anden er den
lumske** — jeg skrev tabellen for at loese et parse-problem og gav den en
inkonsistent foerste kolonne, saa fire af fem nye raekker faldt bort. `\**`
tolererer nu begge.

| navne | dansk | engelsk | serverens bevis |
|---|---|---|---|
| **7** | faelles | `shared` | `shared_matches`, `shared_tournaments` (28 forekomster) |
| **7** | navn | `name` | `dbu_club_name`, `created_by_name`, `event_name` (39) |
| **4** | aktivitet | `activity` | `activity_type_key`, `activity_type_category` (93) |
| **4** | klub | `club` | `dbu_club_id`, `dbu_club_name`, `dbu_club_colors` (9) |
| **3** | holdkort | **se A5b** | **FLYTTET TIL A5b i A20.** A19 rettede den fra `team_card` til `teamsheet`; begge er rigtige, for hvert sit begreb. DBU-holdkortet er `teamsheet`, statistik-kortet er `team_card`. Maalvaerdien er fjernet her, saa posten ikke kan parses som afgjort |
| **3** | tider | `times` | `period_times_json` |
| **3** | nulstil | `reset` | `password_reset_tokens`, `idx_password_reset_tokens_family` |
| **3** | tjek | `check` | `get_player_check`, `/dbu/player-check` |
| **3** | minut | `minute` | `period_minutes`, `yellow_card_minutes`, `idx_position_minutes_session` |
| **2** | deltager | `participant` | `participant`, `participant_required` |
| **2** | troeje | `shirt` | `shirt_color`, `home_shirt`, `away_shirt` |
| **2** | tidslinje | `timeline` | `sessions.timeline_json` |
| **2** | afbud | `absence` | `absence_reason_key`, `absence_reason` (30) |
| **2** | aktive | `active` | `active_half_number`, `active_period_start_ms` (60) |
| **2** | nummer | `number` | `active_half_number`, `match_number_prefix` |
| **1** | foelg | `follow` | `follow_goal_notifications`, `follow_match_end_notifications` |
| **1** | bekraeft | `confirm` | `confirmed`, `confirmed_by`, `confirmed_date` (21) |

**Og to, SAMMENSAT-reglen afgoer af sig selv**, fordi deres dele nu er daekket:

```
aldersregler     age_rules        serveren HAR tabellen (17 forekomster)
aktivitetstyper  activity_types   serveren HAR tabellen (61)
```

De er altsaa ikke nye beslutninger — de falder ud af reglen plus A5 ovenfor, og
**det er den kontrol, reglen blev valideret med.**

### RETTET 06-10 kl. 00:05: `raekke` er FLYTTET til A5b — 51 navne, to betydninger

**Posten stod her i to timer som "afgjort af serveren". Den var forkert, og
Android maalte det i deres eget trae:**

```
DBU-raekken           -> division   Raekke, RaekkeKamp, RaekkeKampeSvar
en RAEKKE i en liste  -> row        GpsRaekke, HistorikRaekke, KamptrupRaekke
                                    BaenkRaekke, KampprogramRaekke, KampRaekke
```

`GpsRaekke -> GpsDivision` stod paa deres liste af 77 foreslaaede omdoebninger.
Det er **een raekke fra en GPS-upload**, og `GpsDivision` er ikke en stavefejl —
det er en forkert model, og den kompilerer.

**Og intet kunne fange den: `Gps` og `Raekke` er begge daekkede ord.**

**Hvorfor posten var forkert, selv om maalingen var rigtig:** serveren HAR
afgjort, at DBU-raekken hedder `division` (`/divisions`, `division_name`). Det
er sandt. Men jeg maalte **serveren** og konkluderede om **klienternes navne** —
og klienterne bruger ordet i to betydninger, serveren kun i een.

Det staar i min egen commit-besked fra samme indsaettelse:

> *"En ordbog ville have sagt `row`, og `row` ER rigtigt i `_row_to_dict`."*

**Jeg havde modeksemplet i haanden og lagde ordet i det entydige afsnit
alligevel.** Det er ikke en maalefejl; det er en placeringsfejl, og den er
dyrere, fordi afsnittets overskrift siger *"ingen beslutning, kun
efterproevning"*.

**Reglen, der foelger:** et ord maa kun staa i et A-afsnit, naar serverens brug
og klienternes brug er maalt til at vaere DEN SAMME. Serverens feltnavn afgoer,
hvad ordet HEDDER paa engelsk — ikke hvor mange betydninger klienten bruger det i.

### `ord` er en delstreng af `password` — samme faelde som `hold`

Maalt: `grep` for `ord` i serveren giver **44 traef, og de er `admin_password`,
`admin_password_hash`, `admin_password_salt`.** Ordet `password` indeholder `ord`.

`ord` er ikke afgjort her, men faelden er, og den gaelder uanset maalvaerdien:
**kun paa et HELT ord i en opdeling.** Samme regel som C2's `hold`, og listen har
nu to beviste tilfaelde af den, ikke eet.

## BOEJNING: `-r`-FLERTALLET ER AFVIST, OG TO FEJL I SAMME REGEL

**Backend spurgte 06-10 kl. 00:25, om `-r` kan regnes som flertal paa linje med
`-er`** — dansk tilfoejer kun `-r`, naar ordet ender paa `-e` (`periode` ->
`perioder`). Reglen er sprogligt rigtig. **Som VAERKTOEJSREGEL er den afvist**,
og maalingen viser hvorfor med det samme.

**Af mapningens 24 ord der ender paa `-e` er en stor del slet ikke navneord:**

```
aktive   -> aktiver     ADJEKTIV. Og 'aktiver' er et rigtigt dansk ord: assets
fejlede  -> fejleder    datids-participium
gule     -> guler       adjektiv  ·  halve -> halver   adjektiv/talord
hjemme   -> hjemmer     adverbium ·  nullable          engelsk ord
```

`aktive -> aktiver` er den vaerste: den producerer et **eksisterende dansk ord
med en anden betydning.** Intet i en hybrid-kontrol kan se det.

**Og den ENGELSKE side er uafhaengigt forkert.** Et `+s` paa maalvaerdien giver:

```
bane -> pitch    ->  pitchs     skal vaere  pitches
familie -> family ->  familys    skal vaere  families
halvleg -> half  ->  halfs      skal vaere  halves
```

**To fejl i samme regel, paa hver sin side af pilen.** Dansk flertal kan ikke
udledes af endelsen alene, fordi endelsen ikke siger, om ordet er et navneord;
og engelsk flertal kan ikke udledes med `+s`, fordi `-ch`, `-y` og `-f` boejer
anderledes.

**Konsekvens:** de 9 navne, Backend fandt, gaar paa **haandnavne-listen**, ét ad
gangen med deres kaldested. Det er den form, der har virket fire gange i nat —
og den koster mindre end en regel, der skal have undtagelser for adjektiver,
participier og engelsk ortografi.

### Ordstillingen: `noun_count`, maalt

```
halves_count   30 forekomster   <- serverens egen form
_pause_count    7
_session_count  6
```

Navneordet foerst, `count` sidst. Saa `_antal_perioder -> period_count`, ikke
`count_of_periods`. **Afgjort af serveren, ikke af mig.**

### `gem_halvleg_tilstand -> save_half_state`, efter C3

Serveren bruger `_status` om deltagelse og kort (`card_status`,
`expected_status`, `actual_status`) og `_state` om alt andet
(`content_state`, `_byg_live_activity_content_state`). En halvlegs tilstand er
ikke en deltagelsesstatus, saa **`state`** — praecis C3's snit, nu med serverens
egne navne som bevis.

## A5b. KAN IKKE AFGOERES PAA LISTEN — maal kaldestedet

> **TIL ET VAERKTOEJ: SPRING HELE DETTE AFSNIT OVER.** Raekkerne herunder har
> samme form som mapningens — `| ord | \`maal\` | ... |` — men kolonnen "de to"
> indeholder TO eller TRE maalvaerdier, og et parse tager den foerste
> backtick-gruppe. Laeser du A5b, faar du `opstilling -> lineup`,
> `raekke -> division` og `maal -> goal` som AFGJORTE, og det er praecis
> modsat afsnittets formaal.
>
> Det er ikke et hypotetisk problem. Afsnit A's split-raekke stod indtil
> 06-10 kl. 00:50 som `` \`lineup\` **eller** \`formation\` ``, og **enhver
> parse laeste `opstilling -> lineup`** — det var kilden til koordinatorens
> eget `get_opstillinger -> get_lineups`-forslag. Raekken har nu
> `**se A5b**` i maalkolonnen, saa den ikke kan parses.

**ALLE FEM AF ANDROIDS ORD STOD TIDLIGERE I A2 ELLER A4** som "afgjort af
serveren", fra kl. 22:45 og 23:10. De er **fjernet derfra 06-10 kl. 00:05** og
staar nu kun her, med serverbeviset bevaret i kolonnen "de to".

**Grunden til at de ikke blot fik en note:** disse tabeller bliver PARSET af tre
sessioner. En tvetydig post i en maskinlaest tabel er en fejl, uanset hvad
prosaen ved siden af siger — et vaerktoej laeser raekken, ikke advarslen. Et ord,
der ikke kan afgoeres, maa derfor ikke staa i en mapping-tabel overhovedet.

**Disse har to plausible engelske ord i DENNE kodebase**, og det er
ambiguitetsreglen fra afsnittet oeverst: et vaerktoej skal springe navnet over,
ikke vaelge.

| dansk | de to | hvorfor listen ikke kan afgoere det |
|---|---|---|
| maerke | `mark` · `badge` · `marker` | serveren har BAADE `badges` og `marker`. `KortMaerke` er sandsynligvis `mark`, men det er kaldestedet der ved det |
| tilbage | `remaining` · `back` | `KampTilbage` = kampe der RESTERER. `gaaTilbage` = navigation. Samme ord, to retninger |
| skift | `change` · `substitution` | `skiftAdgangskode` er `change`. Men **`skifte` er en HAENDELSESTYPE** (en udskiftning), og den maa ikke blive `change` · serveren: `PositionChangeIn` (9 forekomster), `_position_change_fra_lager` · **ogsaa i SERVEREN**: `beregning.py`s `ind`/`skifte`/`ud` i `_HAENDELSE_RANG` er en udskiftning, mens `skift_adgangskode` er `change` (Backend 06-10 kl. 00:05) |
| tal | `number` · `figure` · `stat` | `nummer` ejer allerede `number` (A5). `HoldTal` er statistik, ikke numre · serveren: `activity_types.match_number_prefix` |
| aeldre | `older` · `legacy` | `AeldreNoegle` i `CacheSkema`. Android har `legacy_type` — to platforme kan have valgt forskelligt |
| ord | `word` | maalvaerdien er nok klar, men se delstreng-faelden ovenfor FOER den bruges |
| **opstilling** | `lineup` · `formation` | **19 navne hos Android, den stoerste enkeltgevinst.** Serveren har SELV splittet det: `match_lineup` er TABELLEN, `match_lineup.formation` er KOLONNEN. Staar i afsnit A's egen split-tabel, men maa derfor ikke staa i mapningen |
| **maal** | `goal` · `target` | maalt: `goal` **42**, `target` **12** i serveren. Afsnit A afgoer SCORINGS-betydningen (`follow_goal_notifications`); maal som *maaling/maalsaetning* er en anden ting |
| **traek** | `pull` · `draw` · `feature` | `pull` 1, `draw` 1 — og `feature` 27, som tilhoerer `funktion`, ikke `traek`. Beviset er altsaa svagt OG tvetydigt |
| **raekke** | `division` · `row` | **51 navne, maalt af Android.** DBU-raekken er `division`; `GpsRaekke`/`BaenkRaekke`/`HistorikRaekke` er `row`. Se afsnittet ovenfor · serveren: `dbu_groups.division_name` |
| positioner | `positions` · `position` | mappingens post er dansk FLERTAL mod engelsk ENTAL · serveren: `position_minutes.position` (flertalsreglen: `positions`) |
| typer | `types` · `type` | samme · serveren: `activity_types.legacy_type` (flertal: `types`) |
| **plads** | `slot` · `position` | **BEGGE er godkendt engelsk, og begge er serverens egne lineup-ord** (`slot_id`, `slots`, `start_slots` mod `position_minutes.position`). `valgtTomPlads` er en tom PLADS i opstillingen; `indPaaNyPlads` (`LineupEngine.kt:434`) er en spiller, der kommer ind paa en ny POSITION. Maalt 06-10 kl. 05:25: 7 navne hos Android |
| **holdkort** | `teamsheet` · `team_card` | **maalt 06-10 kl. 05:50, efter at A19 havde afgjort den forkert TO gange.** DBU's officielle holdkort: serveren har `dbu_teamsheets`, `dbu_club_teamsheets` og `@router.get("/teamsheet-players")`; iOS' `holdkortSpillere()` henter netop den. **Men statistik-kortet i brugerfladen er ogsaa `holdkort`:** Androids `object TeamCard` (`core/stats/PositionBane.kt:108`, var `object HoldKort`, omdoebt i `6ab9ec7`) og iOS' `enum TeamCard` (`Core/Stats/StatsLogik.swift:435`). **To platforme valgte `TeamCard` uafhaengigt af hinanden** — det er et argument, ikke en fejl. 25 forekomster hos Android staar paa den forkerte side; de 19 paa stats-siden er korrekte |
| **side** | `page` · `side` | **en NY form: det danske ord kolliderer med et GODKENDT ENGELSK ord, ikke med en anden kandidat.** Dansk `Side` er en side/skaerm (`HoldkortSide`, `baggrundSiden`); engelsk `side` er holdets side, som serveren har i `match_sides`/`"side"`/`"home"`/`"away"` og klienterne allerede bruger (`TeamSide`, `assistSide`, `scorerSide`). `error.main.page_gone` = *"Siden er ikke laengere tilgaengelig"* er web-siden. Et vaerktoej kan ikke skelne `HoldkortSide` fra `TeamSide` paa strengen. Maalt 06-10 kl. 06:10, se A21 |
| **udviste** | `sent_off` · `sin_binned` | arver `udvisning`s tvetydighed: `beregning.py` skelner *"midlertidig udvisning"* (10 min) fra *"direkte udvisning"* (roedt kort), og `udviste` er boejning af samme. 5 navne hos Android (`egneUdviste`). Kan ikke afgoeres foer `udvisning`. Se A23 |
| **kode** | `code` · `password` | `adgangskode` ejer allerede `password` (A, serverens `password_hash`). Men `indPaaNyPlads(kode: String, ...)` er en PLADS-kode, ikke en adgangskode — samme fil, samme linje. `nyKode`/`kodeOk` kan vaere begge, og kun kaldestedet ved det. 6 navne |
| **udvisning** | `sin_bin` · `dismissal` | **maalt 06-10 kl. 04:55 i jeres egen kode, ikke gaettet.** `beregning.py:322` *"en midlertidig udvisning"*, `:485` *"10 minutters udvisning"* — en tidsbegraenset bortvisning. Men `:329` *"en direkte udvisning"*, som er **roedt kort**. Eet dansk ord, to fodboldbegreber, praecis `opstilling`-formen. Serverens `format_rules.red_card` daekker KUN den anden. Rammer `_udvisning_slut_ms` og `_udvisning_minutter` (`live.py:438`) |

**`skift` er bekraeftet paa ALLE TRE SIDER.** Android fandt de 8 klientnavne;
Backend maalte uafhaengigt, at deres `beregning.py` har samme to betydninger, og
at `skift_adgangskode -> change_password` (batch 2) var kontekstuelt rigtig.

Det flytter bevisklassen: ordet er ikke tvetydigt *i klienterne*, det er
tvetydigt **i projektet**. Et vaerktoej, der afgoer det pr. platform, vil
afgoere det forskelligt tre steder.

**`skift` er den farligste**, og grunden er den samme som `kort`s to betydninger:
et af de to er en haendelsestype i data, og en forkert omdoebning dér er en
datamigrering, ikke en omdoebning.

## A5c. ET ANTAL NAVNE MED ORDET ER IKKE ET ANTAL NAVNE ORDET KAN OMDOEBE

**Tilfoejet 06-10 kl. 00:15, efter at Android bad om "de 8 DBU-navne" som et
navne-niveau-tillaeg. De findes ikke som et rent saet.**

Jeg rangerede `raekke` foerst i A5 med begrundelsen *"8 navne"*, hentet fra iOS'
`frigoer-flest-navne.md`. **Maalt i iOS' eget trae:**

```
KampRaekke      Core/Design/KampKomponenter.swift:149   struct ... : View     ROW
HoldRaekke      Core/Design/KampKomponenter.swift:80    struct ... : View     ROW
KampeRaekke     Core/Kampe/KampeLogik.swift:4           : Identifiable        uklar
RaekkeKamp      DBURepository+Datainput.swift:4         : Decodable       DIVISION
```

**Mindst halvdelen af iOS' otte er raekker i en liste**, ikke DBU-raekker. Og
`KampRaekke` staar paa Androids `row`-liste OGSAA — samme navn, samme betydning,
i to traeer.

**iOS' tal var rigtigt.** Deres doc siger praecist, hvad det taeller: *"navne med
praecis ÉT blokerende ord"*. Den paastaar ikke, at ordet er entydigt. **Jeg laeste
et ANTAL som en DOM** — og rangerede derefter ordet foerst, netop fordi tallet var
stort.

```
jeg laeste              jeg sluttede                 det rigtige maal
8 navne har ordet       8 navne kan omdoebes          ordets BETYDNING pr. navn
en hyppig kandidat      en vigtig afgoerelse          om der er EEN afgoerelse
```

**Konsekvensen for metoden:** et navne-niveau-tillaeg kan ikke bo i denne fil.
Udleverede jeg "de 8", havde Android faaet `KampRaekke -> MatchDivision` for et
navn, der hos dem er en raekke.

**Hver platform klassificerer sine egne navne for de seks ord i A5b.** Listen
afgoer, hvad ordet HEDDER i hver betydning (`division` og `row`); hvilken
betydning et konkret navn har, kan kun maales dér, hvor navnet bor.

## A6. NI ORD MERE — og denne gang med serverens FOREKOMSTTAL

**Tilfoejet 06-10 kl. 00:30.** Udgangspunktet er maalt, ikke valgt: af de **66
ord, `ORD-ENGELSK-DANSK.md` klassificerer som danske**, har 21 et maal, 5 er
bevidst tvetydige (A5b) — og **40 har intet maal.** Et ord, der er klassificeret
dansk uden en maalvaerdi, kan ikke omdoebe noget.

**Kolonnen `n` er antal forekomster i `backend/app/` maalt med SEGMENTgraense**,
ikke som delstreng. Den staar der, fordi styrken af et bevis er forskellen paa
"serveren har afgjort det" og "serveren har brugt ordet een gang".

| dansk | engelsk | n | serverens bevis |
|---|---|---|---|
| kampdato | se nedenfor | **5** | **TO SVAR, og det er ikke en fejl i listen** — se afsnittet om ledning mod navn |
| frisk | `fresh` | **8** | `fresh_kampnrs`, `refresh_match_now` |
| slip | `release` | 3 | laasens modsaetning til `tag` |
| handling | `action` | 2 | **dansk ACTION, ikke "haandtering"** — staar paa farligste-listen |
| tom | `empty` | 2 | `gps_file_empty` |
| fri | `free` | 1 | svagt, men entydigt |
| prognose | `forecast` | 1 | svagt, men entydigt |
| registrer | `register` | 1 | `register_team_events`. Bydeform |

**De tre oeverste er afgjort. De seks nederste er HINTS**, og forskellen staar i
tallet frem for i en overskrift. Et ord med `n = 1` er ikke "afgjort af
serveren" — det er et forslag, serveren tilfaeldigvis er enig i.

### RETTET FEM MINUTTER SENERE: to fejl i tabellen ovenfor

**1. `kampnr -> match_number` er FJERNET.** Jeg skrev *"det er `#156`s egen
kolonne"*. Maalt paa commit'ens `+`-linjer: `kampnr` er **ikke** blandt `#156`
trin 1's dual-keys. De faktiske par er:

```
navn -> name / username / feature_key     klub_praefiks -> club_prefix
kampdato -> date                          hjemme -> home        ude -> away
score_hjemme -> home_score                score_ude -> away_score
holder_navn -> holder_name                deltager -> participant
tid_sek -> time                           beregnet_af -> calculated_by
```

**Hvorfor jeg trode det:** mit foerste grep var
`git show e2c714d | grep -oE '"(kampnr|puljeid|[a-z_]*nr|spillested|[a-z_]*navn)"'`
— altsaa en **gaettet ordliste mod hele `git show`-outputtet**, inklusive
fjernede linjer, kontekstlinjer OG commit-beskeden. Den maalte ikke, hvad
commit'en gjorde; den bekraeftede, hvad jeg havde skrevet i soegningen.

> Samme fejl som *"6 danske filnavne — der var fjorten"*: **min grep var en
> denylist.** En denylist kan kun bekraefte det, man allerede har taenkt paa.

`match_number` og `match_number_prefix` findes i serveren, men de er **en anden
kolonne** — et praefiks til at generere numre. `kampnr` har i dag **ingen**
engelsk alias paa ledningen, og hoerer derfor i A3, ikke her.

**2. `kampdato -> match_date` var forkert paa den anden side af graensen.**
`_parse_match_date` er et FUNKTIONSNAVN (Backends batch 5). `#156` sender
`"date"` paa ledningen. Rettet til `date`.

> Jeg maalte en funktion og konkluderede om en JSON-noegle. Et egenskabsnavn, et
> kolonnenavn og en wire-noegle er tre forskellige strenge — og i dette projekt
> er de bevidst forskellige.

### To ord blev AFVIST, fordi mit eget grep loeb paa delstrenge

```
loft -> cap     mit grep fandt 'unescape'   unes-CAP-e
kant -> wing    mit grep fandt 'wingback'   een token, ikke et segment
linje -> line   mit grep fandt 'deadline', 'discipline'
lager -> store  mit grep fandt 'restore', 'restored'
```

Med segmentgraense: **`cap` 0 · `wing` 0 · `edge` 0 · `button` 0.** `line` og
`store` har 7 og 9 ægte forekomster, men ikke i en sammenhaeng der afgoer ordet.

**Det er sjette gang i nat, at delstreng-faelden ramte en maaling** — efter
`hold` (C2), `ord` i `password`, og Androids `kropper`. Og denne gang var det
paa den **ENGELSKE** side af pilen, hvor jeg ikke havde ledt efter den.

```
maal paa DANSK side    hold → Indhold, beholdt, ophold
maal paa ENGELSK side  cap  → unesCAPe        <- NY, og lige saa stille
```

**Reglen: begge sider af pilen skal maales med segmentgraense.** Et `grep` paa
`[a-z_]*ord[a-z_]*` finder `password`; et `grep` paa `[a-z_]*cap[a-z_]*` finder
`unescape`. Der er ingen forskel paa de to fejl, og jeg havde kun skrevet den
ene ned.

## A7. SYV ORD FRA ANDROIDS BLOKERINGSLISTE — maalt 06-10 kl. 00:50

**Kilden er Androids egen optaelling** fra `#85`s springe-liste: hvilke ord
blokerer flest af DERES 1738 sprungne navne. Det er en bedre prioritering end
iOS' doc alene, fordi den er maalt paa de navne, der faktisk staar tilbage.

| navne | dansk | engelsk | n | serverens bevis |
|---|---|---|---|---|
| **7** | felt | `field` | **57** | `field_key`, `nullable_fields` |
| 9 filnavne | generer | `generate` | — | **Backends maaling 02:52: eet konsistent betydning i ~15 kaldesteder paa tvaers af 9 filer** — "genererer testvektorerne". Men FILNAVNENE flyttes ikke paa den: se kaeden i KAEDER.md |
| **5** | fjern | `remove` | 1 | **Afgjort paa KOLLISIONEN, ikke paa tallet.** `slet` ejer allerede `delete` (36 forekomster), og de to er forskellige operationer i koden: `_fjern_token` fjerner et token fra en liste, `slet_stoevne`/`slet_min_konto` sletter en raekke. `remove` har n=1, men det er det ENESTE ord, der ikke kolliderer |
| **5** | kampprogram | `fixtures` | 9 | **SERVEREN HAR RUTEN:** `dbu.py:497` `@router.get("/fixtures")`, og `db.py:2249` skriver selv `dbu.py::get_kampprogram (/fixtures)`. `#171`s moenster — ruten blev engelsk, `def`'en blev ikke. Se afsnittet nedenfor |
| **2** | nu | `now` | 2 | **TO RUTER:** `dbu.py:779` `@router.post("/standings/sync-now")` og `dbu.py:968` `@router.post("/sync-now")`. Backend fandt den foerste som tie-break; den anden maalte jeg. Frigiver `tjek_kamp_nu` og `sync_nu` |
| **8** | ekstra | `extra` | **51** | `has_extra_time` |
| **8** | sidste | `last` | **29** | `last_sync`, `last_seen` |
| **8** | ryd | `clear` | **18** | bydeform, som `marker` og `placer` |
| **8** | miljoe | `environment` | **10** | `environment`, ikke `env` (n=1) |
| **8** | baggrund | `background` | 4 | |
| **7** | lokal | `local` | 2 | svagt, men entydigt |

**`fortryd -> undo` er UDELADT med n=1.** Ordet er entydigt, men eet enkelt
serverbevis er ikke nok til at kalde det *afgjort af serveren*, og der findes
ingen anden kilde. Det hoerer i afsnit B, hvis nogen vil foreslaa det.

### `cache` og `total` er ENGELSKE — de hoerer ikke i denne fil

Android har dem som blokerende ord (8 hver), fordi de staar som *ugennemgaaede*.
Maalt: `cache` **31** og `total` **52** forekomster i serveren, brugt som
engelske ord. **De skal GODKENDES i `ORD-ENGELSK-DANSK.md`, ikke oversaettes.**

Det er overdetektionens anden form: ikke et fragment som `repo` eller `dsl`, men
**et rigtigt engelsk ord, der ogsaa staves saadan paa dansk.** En ordbogsproeve
siger "engelsk" og har ret; godkendelseslisten siger "ikke godkendt" og har
ogsaa ret. Kun en maaling af BRUGEN afgoer det.

### `dbu` blokerer 17 navne og skal ALDRIG omdoebes

Det er en **forkortelse for en organisation** (Dansk Boldspil-Union), ikke et
dansk ord. Samme klasse som `repo`, `kode`, `dsl`, `hid` i metodeafsnittets
overdetektionsliste — men stoerre end dem alle, og derfor vaerd at navngive.

**`dbu` er det ord, der blokerer næstflest navne hos Android, og den rigtige
handling er at fjerne det fra kandidatlisten**, ikke at finde et maal til det.

### `kampdato` har TO rigtige svar: `date` paa ledningen, `matchDate` som navn

**Androids maaling 06-10 kl. 01:00 rettede posten anden gang.** Jeg havde den
foerst som `match_date` (maalt paa et FUNKTIONSNAVN), saa som `date` (maalt paa
`#156`s wire-noegle). **Begge var halve svar.**

```
paa LEDNINGEN   "date": k["kampdato"]        #156 trin 1 — bindende
som NAVN        kampDato -> matchDate        SAMMENSAT via camelCase
                kampdato -> date             posten vinder, og taber "kamp"
```

**To navne for eet begreb**, hvis posten bruges paa navne. Og `@SerialName` /
`CodingKeys` fryser noeglen i forvejen, **saa navnet er frit** — der er ingen
grund til at lade wire-noeglen diktere det.

Android har haandnavngivet `kampdato -> matchDate`. Og de maalte, at det er den
**eneste** af listens sammensatte poster, hvor SAMMENSAT-reglen giver et bedre
svar end posten: `holdkort` giver det samme, og `tidslinje`, `adgangskode`,
`spillested` kan slet ikke deles.

> Et kolonnenavn, en wire-noegle og et egenskabsnavn er tre forskellige
> strenge — og i dette projekt er de bevidst forskellige.

Det staar allerede i metodeafsnittet. **Jeg har nu brudt det tre gange paa
samme post**, og det er derfor raekken ovenfor peger hertil frem for at give
eet svar.

### RETTELSE: "alle tre sessioners vaerktoejer" var MIN slutning, ikke en maaling

Jeg skrev om split-raekken, at `opstilling -> lineup` havde staaet som en
afgjort post i **alle tre sessioners vaerktoejer**. Android maalte det mod
`41945e6`, `3e70ad6` og `4fe49a2`: **hos dem har den aldrig vaeret der.**

Grunden er et `"eller"`-filter, de skrev ind da parseren blev bygget — og
**samme filter holder ogsaa `maal`, `traek`, `raekke`, `skift` og `tal` ude af
A5b's mapping-formede raekker.** Den beskyttelse var ikke forudset for A5b; den
virkede der af sig selv.

**Mit "alle tre" var samme fejl som resten af natten:** jeg maalte, at raekken
KAN parses, og konkluderede om tre konkrete parsere, jeg ikke havde maalt. Den
rigtige saetning er *"ethvert parse, der tager den foerste backtick-gruppe"* —
og det er praecis, hvor maalingen stoppede.

**Og filteret er den rigtige loesning, ikke min `**se A5b**`-markering.** Et
filter paa `"eller"` / `·` virker paa hver fremtidig tvetydig raekke, uden at
nogen skal huske at markere den. Jeg beholder markeringen, fordi den ogsaa
daekker en parser uden filteret — men et filter er det, der skaleres.

## A8. SEKS ORD MERE — Backends kandidater, maalt 06-10 kl. 01:05

**Backend flagede dem tilbage frem for at gaette**, efter batch 6. Maalt med
segmentgraense paa BEGGE sider, og med de faktiske linjer laest.

| dansk | engelsk | dansk n | engelsk n | serverens bevis |
|---|---|---|---|---|
| gyldig | `valid` | 21 | **17** | `resultat["valid"]`, `valid=True` — allerede en JSON-NOEGLE |
| resultat | `result` | 51 | 10 | `result = []`, `result.append`, `result.extend` |
| spillet | `played` | 13 | 8 | `played_kampnrs`, `played = [m for m in matches …]` |
| notifikation | `notification` | 17 | 13 | `android_notification` |

**`resultat["valid"]` er vaerd at se paa:** serveren har i forvejen et dansk
variabelnavn med en ENGELSK noegle indeni. Det er `#124`s blandede tilstand i
eet udtryk, og det er grunden til at `gyldig -> valid` ikke er et gaet.

### To blev AFVIST

```
relevant   dansk 28, engelsk 28   SAMME TAL — det er samme ord i begge sprog
effektiv   dansk 7,  engelsk 0    INTET bevis for 'effective'
```

**`relevant` er `cache`/`total`-klassen:** et rigtigt engelsk ord, der ogsaa
staves saadan paa dansk. Det skal **GODKENDES** i `ORD-ENGELSK-DANSK.md`, ikke
oversaettes. At de to tal er identiske er selve beviset — hvert dansk traef ER
et engelsk traef.

`effektiv` har ingen maalvaerdi i serveren og hoerer i A3.

### Og `maal` ramte NUL hele def-navne

Backend maalte det: alle fire forekomster er blokeret af **andre** uafgjorte ord
(`effektiv`, `beregn`, `tidslinje`, `hvis`, `relevant`, `notifikation`). Saa
A5b's tvetydighed kostede ingenting denne runde — **men den vil koste, saa snart
de oevrige ord afgoeres.** Vaerd at vide, naar `notifikation` nu er afgjort.

## A9. TO ORD, og hvorfor koordinatoren IKKE laengere laver kandidatlister

**Maalt 06-10 kl. 01:20** paa de ord, der blokerer flest af Backends resterende
navne — 148 navne er eet ord fra at kunne omdoebes, fordelt paa 121 ord.

| dansk | engelsk | n | serverens bevis |
|---|---|---|---|
| byg | `build` | **13** | |
| beregn | `calculate` | 3 | `calculated_by` i `sessions.py` — og det er `#156`s EGEN dual-key for `beregnet_af` |

**Tre blev ikke afgjort:**

```
fjern         remove n=1       hint, ikke en afgoerelse
kampprogram   fixtures n=3     men 'fixtures' kan vaere PYTEST-fixtures. Umaalt
lager         store n=9        verbet 'store' mod navneordet 'storage' — og ordet
                               staar paa farligste-listen (fra lager, ikke oel)
```

`kampprogram` blokerer flest navne af alle (5), og **den er netop derfor ikke
afgjort paa et tal.** Tre `fixtures` i en Python-kodebase er lige saa
sandsynligt testopsaetning som fodboldprogram.

## BESLUTNING: koordinatoren sender ord, ALDRIG kandidatlister

**Natten 05/06-10 fejlede koordinatorens kandidat-vaerktoej NI gange, paa en ny
maade hver gang.** Ingen af dem meldte en fejl; alle gav en plausibel liste.

```
prosa laest som data            7 falske mappingpar, paa -> et overskrev paa -> on
afsnit A's split-raekke         opstilling -> lineup som AFGJORT
antalskolonnen i A5             nul raekker laest
inkonsistent fed antalskolonne  fire af fem nye raekker faldt bort
delstreng paa DANSK side        ord fandt password
delstreng paa ENGELSK side      cap fandt unescape, wing fandt wingback
funktionsnavn mod wire-noegle   kampdato -> match_date
gaettet ordliste mod git show   kampnr "er blandt #156's 23" — den var ikke
split('_') tabte underscore     hvert _private navn saa aendret ud
```

**Den sidste er den, der afgjorde det.** Efter rettelsen gav vaerktoejet 25
kandidater. Laest igennem: syv var identitets-omdoebninger (`fetch_status ->
fetch_status`), resten hybrider med `lager`, `tag`, `nu`, `min`, `side`, `op`
— **og tre var praecis de faelder, Backends egen agent havde fanget selv:**

```
_kort_navn       kort betyder SHORT her, ikke card
_saet_pause      et sync-jobs pause-gate, ikke en halvlegspause
klub_praefiks    SQL-kolonne OG JSON-noegle — ville braekke #156's dual-key
```

**Vaerktoejet foreslog altsaa igen det, en anden havde afvist to timer foer.**

### OPDATERET 03:35: en PLATFORM maa tilfoeje et ord, naar beviset holder

**A9 sagde, at koordinatoren afgoer ord og platformene sender tal. Det er nu for
stramt, og praksis er allerede en anden.**

Backend tilfoejede `seneste -> latest` til afsnit B (18c2d44) med 29 maalte
forekomster i `app/`, een betydning, og tre eksempler skrevet paa posten. Jeg
efterproevede: `latest` 9, `seneste` 52 i hele `backend/` — altsaa to
intervaller, og begge rigtige.

**Posten var god. At den skulle gennem mig, ville have kostet en rundtur og
intet andet.**

```
EN PLATFORM MAA tilfoeje et ord, naar ALLE tre holder:
  1. serverbevis med SEGMENTgraense paa begge sider, eller en RUTE
  2. intervallet staar paa posten — hvad blev maalt, hvor
  3. eet betydning, og eksempler nok at efterproeve paa
KOORDINATOREN afgoer stadig:
  et ord med TO plausible maal                     -> A5b
  et ord, hvor platformene kan vaelge forskelligt   -> faelles beslutning
  en RETNING (n=1 mod en kollision, fx fjern -> remove)
```

**Og grunden til at graensen kan loesnes er maalt:** Backend fangede
koordinatorens fejl ni gange i nat og producerede nul daarlige ord. Et
flaskehals gennem mig ville ikke have fanget noget, og det ville have
forsinket alt.

### Hvad koordinatoren goer i stedet

```
SENDER     ordet, dets maalvaerdi, forekomsttallet, og hvor beviset staar
SENDER     hvilke ORD blokerer flest navne — det er en maaling af listen,
           ikke af koden
SENDER IKKE  en liste over navne, der kan omdoebes
```

**Den form, der virker, er Backends:** en agent genererer kandidater med
reglerne indlejret, og et menneske efterproever hver enkelt mod kaldestedet,
foer noget koeres. Seks batches, nul hybrider i koden.

Androids formulering gaelder ogsaa her: *en melding er et SIGNAL om, hvor der
skal maales — aldrig et facit.* Jeg havde skrevet det til dem tre gange, mens
mit eget vaerktoej lavede facitter.

## A10. HVAD LISTEN ER FOR — og de 418 ord, der IKKE hoerer paa den

**Afgjort af koordinatoren 06-10-2026 kl. 02:33, paa Androids forslag.** Det er en
metodebeslutning, ikke en produktbeslutning.

Androids maaling af deres eget trae efter 183 omdoebninger:

```
core/-navne i alt                      2018
blokeret af et udaekket ord            1417
  ALLEREDE AFGJORT (A5b/fjendeliste)     32 ord ->  87 navne   intet at goere
  ENGELSK-AGTIG, ikke godkendt endnu    131 ord -> 175 navne
  UAFGJORT DANSK                        566 ord -> 817 navne

418 af de 566 frigiver PRAECIS EET navn      = 74 %
top 10 frigiver 58 af de 817
```

### Beslutningen: et ord, der optraeder EEN gang, hoerer ikke paa listen

**De 418 bliver ikke poster her. De bliver navne, hver platform afgoer paa
kaldestedet** — A5c's form anvendt bredt.

Grunden er, hvad en listepost ER: en **faelles** beslutning, fordi tre platforme
skal bruge samme engelske ord for samme danske begreb. **Et ord, der kun
forekommer i eet navn eet sted, har ingen faellesmaengde at vaere enig om.** Saa
posten koster en koordinering og giver ingen.

```
PAA LISTEN      ord, flere platforme bruger — eller hvor serveren har
                afgjort det, saa klienterne SKAL foelge
PAA KALDESTEDET et ord i eet navn. Den der ejer navnet, navngiver tingen
```

**Og det er samme konklusion, koordinatoren selv naaede 05-10**, da et forslag
om at skrive 349 ord blev afvist: *et ord, der optraeder een gang, er ikke en
ordforraads-beslutning — det er navngivningen af den ene ting.* Androids tal
goer den skarpere, ikke mildere: forholdet var 42 %, nu er det 74 %.

### TRUKKET TILBAGE kl. 02:43: "egne engelske sammensaetninger" findes IKKE

**Afsnittet stod her i to minutter paa en upaavist saetning. Android rettede sig
selv, og jeg havde skrevet den ind uden at slaa den op.**

```
paastanden   "datainput er data + input, begge engelske og begge GODKENDTE"
maalt        data JA · input NEJ · prefs NEJ
maalt bredt  NUL navne i core/ er blokeret af en sammensaetning af to
             godkendte engelske ord
```

**Kategorien findes ikke.** Og fejlen er lige saa meget min: jeg tog en saetning
fra en besked fuld af maalinger og skrev den i en delt fil uden at maale den.

**Androids formulering, og den er ny:**

> Et instrument, man har kontrolleret, beskytter kun de TAL, der kom ud af det.
> En saetning ved siden af tallet er lige saa usikker som enhver anden saetning.

De havde kontrolleret deres instrument — `lineup`-spoegelserne fangede de selv —
og sendte saa en upaavist saetning i samme besked. **Saetningen laante tallets
trovaerdighed.**

**Og den modsatte halvdel er min:** jeg laeste en besked, hvor alt andet var
maalt, og lod det daekke den ene saetning, der ikke var. Et relay har samme
pligt som en maaling — og *"relayér hvor der skal maales, ikke hvad der skal
rettes"* gaelder ogsaa, naar man modtager.

### Det RIGTIGE fund: "engelsk-agtig" er heuristikkens laek, ikke en kategori

Androids gruppe paa 131 ord / 175 navne er **ikke en inddeling.** Den er
ordbogens laek, maalt paa de 30 ord, der frigiver mindst to navne:

```
DANSK, som ordbogen siger JA til   ~12 ord   ca. 40 % af gruppen
  mangler 5 · del 3 · af 3 · lille 2 · rang 2 · stilling 2
  ikon 2 · ur 2 · slags 2 · rod 2
STOEJ                                2 ord   iv · iso
AEGTE ENGELSK, ikke godkendt       ~16 ord
```

**Listen siger det allerede om heuristikken** — *"ordbogen laekker: er · alt ·
gang · mange · loft · mine · tag · slip · art · hold · by · for"* — og gruppen
**arvede laekken og praesenterede den som en kategori.** Androids ord: *jeg satte
et navn paa et heuristik-udfald og sendte navnet videre som en inddeling.*

Og deres egen gennemlaesning skred ogsaa: `ur`, `slags` og `rod` laa foerst i den
engelske bunke. **Det er ikke et argument for et bedre filter — det er
argumentet for, at ordene laeses ét for ét.**

### De 13 aegte engelske er godkendt, og ÉT var en faelde

Maalt mod serveren med segmentgraense:

```
selected 136 · slots 70 · platform 67 · started 66 · trend 46
participated 30 · kickoff 8 · theme 5 · offline 2 · basis 2
meter 1 · neutral 0 · markdown 0
```

`neutral` og `markdown` har nul serverbevis, men ingen afvigende dansk
betydning. **De 13 er tilfoejet til `ORD-ENGELSK-DANSK.md` (217 -> 230).**

**`motor` blev AFVIST, og den var naer at slippe igennem.** Serveren har den eén
gang, og ordet findes i begge sprog — men Androids brug er dansk:

```
LiveMotor · OpstillingMotor · Motoren · MOTORENS
```

**Den bestemte form `Motoren` afgoer det:** et engelsk ord boejes ikke med dansk
bestemt artikel. `motor` betyder `engine` dér, og per A10 hoerer et ord i tre
navne paa **kaldestedet**, ikke paa listen. Android navngiver det.

### Og instrumentreglen gaelder BEGGE veje

Androids foerste udgave af listen havde `lineup` oeverst med 12 navne — **og alle
12 var faerdige.** `opstilling -> lineup` bor i deres `PLATFORM_ORD` efter A5c,
men `lineup` staar ikke i den faelles godkendelsesliste, saa `Lineup`,
`LineupSlot`, `fromLineup` blev meldt som blokeret af et udaekket dansk ord.

Tolv saadanne spoegelser, 34 navne-traef — **alle tolv engelske ord, vaerktoejet
selv havde indfoert.**

> Et vaerktoej, der ikke kan genkende sin egen udgang, melder den som
> resterende arbejde.

Det er idempotensfejlen i en anden form: ikke *omdoeber to gange*
(`PositionMinute -> PositionMinutes`), men *taeller sig selv som uafsluttet*. Den
foerste flytter navnet; **den anden flytter TALLET** — og tallet var det, der var
bestilt.

**Reglen fra A9 gaelder derfor begge veje: den der MAALER, kontrollerer sit
instrument foer tallet forlader huset.** Koordinatoren sender ord, platformene
sender tal — og begge skal proeve deres vaerktoej mod dets eget output.

### Og ni poster var usynlige for en parser indtil kl. 02:33

```
2 celler med /      "til / paa"  ·  "valg / udvalgt"     -> delt i to raekker hver
6 celler med aeoeaa haendelse · noegle · stoevne · aarsag · traening
                                                         -> normaliseret
1 celle med ()      "maal (scoring)"                     -> peger nu paa A5b
```

**`stoevne` og `valg` stod i Androids top-20 over UAFGJORTE ord** — og de var
afgjort hele tiden. Projektet skriver dansk uden diakritter i identifikatorer
(`#124` maalte: kun 36 navne med ae/oe/aa direkte), saa noeglerne skal ogsaa
skrives saadan. En parser, der ikke normaliserer, missede seks poster.

### Jeg afviste `kampprogram` TO GANGE paa et antal uden at laese linjerne

**Maalt kl. 03:05, og det var det modsatte af min antagelse.**

```
app/dbu.py:497        @router.get("/fixtures")          <- RUTEN
app/db.py:2249        # dbu.py::get_kampprogram (/fixtures)
app/dbu.py:796        resource="fixtures"
app/dbu_sync.py:1279  "pool %s's fixture fetch dropped ..."
tests/*               seks forekomster — AEGTE pytest-fixtures
```

**Fodbold-betydningen ligger i `app/`. Pytest-betydningen ligger i `tests/`.**
Og ruten afgoer ordet paa ledningen: serveren svarer allerede paa `/fixtures`,
og `test_route_navngivning.py:57` har `"fixtures"` paa sin liste over godkendte
rutenavne.

**Begge mine afvisninger lyder saadan:**

> Ni `fixtures` i en Python-kodebase er lige saa sandsynligt pytest-opsaetning
> som fodboldprogram.

Det var rigtigt om TALLET og forkert om koden. **Jeg maalte, at ordet forekom
ni gange, og konkluderede om hvad det betyder** — nattens egen fejlklasse, i
det afsnit hvor den staar beskrevet.

**Og `schedule` var ikke et alternativ:** alle seks forekomster er
cron-scheduleren (`dbu-scheduler`, `scheduler-process`, `purge_expired`-
intervallet). Ordet er optaget af et andet begreb, saa der var kun eet svar
hele tiden.

**Backend afviste den paa samme grundlag som mig**, og med min egen regel:
*"forcing one would be exactly the n=1-style guess you're avoiding."* De havde
ret i at ikke gaette — **og ingen af os slog ruten op.** Det var ikke et gaet,
der manglede; det var en maaling.

### METODE: led efter RUTEN foerst — tre gange i nat var den beviset

**Maalt kl. 03:12.** Tre ord blev afgjort af en rute, og i alle tre tilfaelde var
ruten der hele tiden:

```
#171          ruten blev /me, men `def mig` og `def admin_mig` var danske
kampprogram   @router.get("/fixtures")              — jeg afviste ordet TO gange
nu            @router.post("/standings/sync-now")
              @router.post("/sync-now")             — to, ikke een
```

**En rute er det staerkeste bevis, listen kan have**, og staerkere end et
forekomsttal:

```
en rute        er en KONTRAKT. Klienterne kalder den; ordet er afgjort
et kolonnenavn er serverens valg, men internt
et forekomsttal siger kun at ordet BRUGES — ikke hvad det betyder
```

Og `test_route_navngivning.py` har en **godkendelsesliste over rutenavne**, som
er en faerdig liste af engelske ord, ingen havde brugt som kilde.

**Hvorfor det blev overset tre gange:** vi maalte alle sammen ord i
IDENTIFIKATORER og glemte, at ruterne er den ene del af kodebasen, hvor
engelsken allerede ER afgjort — fordi en klient kalder den.

**Reglen: foer et ord afvises som tvetydigt, saa grep efter det i en
`@router`-dekorator og i rutenavne-godkendelseslisten.** Det tager fem sekunder
og er den eneste kilde, der er bindende.

## A11. SYV ORD FRA ANDROIDS "TUNGESTE 15" — og metoden er IKKE loebet toer

**Maalt kl. 03:19.** Android meldte, at `#124` trin 2 var loebet toer for `core/`:
0 navne kan omdoebes rent, 1539 af 2018 springes over, og 527 af 774 ord laaser
praecis eet navn. **Deres konklusion var, at ordlistemetoden er spent.**

De foreslog samtidig, at `delt`/`delte` og `foer`/`efter` *"ikke sikkert hoerer i
en ordliste"*, fordi engelsk vender sammensaetningen frem for at oversaette ord
for ord.

**Maalt: det gaelder ingen af dem. Alle syv oversaetter direkte.**

| navne | dansk | engelsk | n | serverens bevis |
|---|---|---|---|---|
| **8** | spilletid | `play_duration` | **63** | `play_duration_min` |
| **5** | kategori | `category` | **101** | `activity_types.category` |
| **5** | kilde | `source` | **53** | `source = 'confirmed'` |
| **6** | egne | `own` | **51** | |
| **7** | delte | `shared` | **98** | `shared_matches`, `shared_tournaments` |
| **6** | delt | `shared` | **98** | samme — men **KUN paa et helt ord** |
| **6** | efter | `after` | **21** | |
| **7** | foer | `before` | **11** | men **KUN paa et helt ord** |

**50 af Androids egne navne**, efter deres egen optaelling.

### Hvorfor de SAA strukturelle ud: delstreng-faelden, tredje gang i nat

```
delt   -> attendanceDeltog · Deltagelse     delt er delstreng af DELTOG/DELTAGELSE
foer   -> FoererFarve · FoererPille         foer er delstreng af FOERER
```

**`deltog` er "deltog", `deltagelse` er "deltagelse", og `foerer` er "foerer" —
tre andre ord.** De aegte forekomster oversaetter uden videre:

```
delteVeoLinks  -> sharedVeoLinks      aktivFoer   -> activeBefore
delteJson      -> sharedJson          foerLogUd   -> beforeLogout
harDelteHaendelser -> hasSharedEvents efterFlyt   -> afterMove
```

**Saa `delt` og `foer` er vokabular, ikke struktur** — de skal blot bruges med
hele-ord-reglen, praecis som `hold` og `ord`.

### Hvad der STAAR af deres konklusion

Metoden er ikke spent, men **halen er stadig lang**, og A10's beslutning staar:
527 ord, der laaser eet navn hver, hoerer paa kaldestedet. **Forskellen er, at
de 25 TUNGESTE stadig kan afgoeres** — og syv af de femten blev det her.

```
tilstand   C3 afgoer den: status ved deltagelse, state ellers. Kontekst
klubber    falder ud af `klub -> club` plus boejningsreglen
kampnr     i A3 — ingen engelsk alias paa ledningen
fortryd    `undo` har n=1. Et forslag, ikke en afgoerelse
skal · kan · ud   modalverber og praepositioner — DE er strukturelle
```

**Af de femten var syv afgoerlige, fire afgjort andetsteds, og tre reelt
strukturelle.** Een — `fortryd` — er stadig et forslag.

### Og lektien er ikke om ordene

**Android maalte rigtigt og foreslog en forkert aarsag**, og jeg accepterede
naesten rammen i stedet for at maale ordene. Det er anden gang i nat med samme
par: foerst `datainput`, nu denne.

> Et instrument, man har kontrolleret, beskytter kun de TAL, der kom ud af det.

Deres tal — 774 ord, 527 med eet navn, 13 % i de 25 tungeste — var rigtige.
**Forklaringen paa HVORFOR de tungeste ikke kunne afgoeres var ikke maalt**, og
den var forkert for syv af dem.

## INGEN_FLERTAL — 24 noegler, boejningsreglen ALDRIG maa roere

**Tilfoejet 03:41, fordi Androids vaerktoej lavede `Kampfoerer -> MatchBefores`.**

```
INGEN_FLERTAL
  PRAEPOSITIONER  efter · foer · fra · paa · til
  BYDEFORMER      bekraeft · beregn · byg · fjern · foelg · gem · hent
                  indlaes · nulstil · registrer · ryd · saet · slet · slip
                  tjek · vaelg · vis
                  filtrer · soeg
```

**Ingen af de 24 har et flertal.** Boejningsreglen maa derfor aldrig forsoege at
laese et `-er`/`-r` som en flertalsendelse paa dem.

### UDVIDET til 24 kl. 05:38 — og den foerste var MIN egen A17-post

Android maalte to boejningsfejl, deres vaerktoej lavede:

```
SoegeFiltre  ->  SearchesFilters     `soege` boejet som flertal af `soeg`
filtrer      ->  filterses           `-r` laest som flertal af `filtre`
```

**Den anden er en konsekvens af A17.** Jeg tilfoejede `filtre -> filters`, fordi
ruten `/filters` afgjorde den — og `filtrer` er den danske BYDEFORM («filtrer
listen»), som ser ud som `filtre` plus et flertals-`r`. Posten var rigtig;
**den aabnede et hul, jeg ikke maalte, da jeg lagde den ind.**

```
INGEN_FLERTAL, tilfoejet
  BYDEFORMER      filtrer · soeg
```

Android afgjorde begge paa **ERKLAERINGEN**, ikke paa ordet: `fun filtrer(...)`
er et verbum, `data class SoegeFiltre(` er navneordet "soegefiltre". Det er den
rigtige maade, fordi de to former er identiske som strenge og kun erklaeringen
skiller dem.

**Reglen, der foelger for mig:** naar du tilfoejer en post, hvis dansk ord har en
BYDEFORM der ligner ordet plus `-r` eller `-er`, saa laeg bydeformen i
INGEN_FLERTAL i SAMME tur. `filtre`/`filtrer` er det foerste maalte par;
`vaelge`/`vaelger` og `beregne`/`beregner` har samme form, og de to staar der
allerede — ved et tilfaelde, fordi de kom ind som bydeformer fra starten.

### Hvorfor en "KUN paa et helt ord"-markering IKKE var svaret

Android spurgte, om hele-ord-forbeholdet kunne faa en maskinlaesbar form. **Jeg
maalte det foerst, og svaret er nej — fordi forbeholdet gaelder naesten alt:**

```
100 af 118 mapping-noegler er DELSTRENG af et laengere ord i de tre traeer
  afbud   -> afbudsaarsag · afbuddet       aktive -> aktiveret · deaktiveret
  beregn  -> beregning · beregnet          delt   -> deltog · deltagelse
  felt    -> felter · feltnavne            foer   -> foerste · foerer · udfoer
  fri     -> frisk · friendly · valgfri    egne   -> beregnet · regnet
```

**En markering paa 100 af 118 poster er ikke en markering — det er en
standardregel.** Hele-ord-matchning ER standarden, og den haandhaeves af
camelCase-/snake_case-opdelingen, ikke af en kolonne.

### Fejlen laa et andet sted: boejningsreglen, ikke mapningen

```
Kampfoerer   deles som Kamp + foerer
             `foerer` er IKKE en noegle   -> navnet skal SPRINGES OVER
             boejningsreglen gjorde i stedet: foerer -> foer + FLERTAL
```

**En praeposition har ikke flertal.** Androids egen diagnose er praecis:

> `VERBESTAMMER` fanger `vaelger` — men `foer` er ikke en verbestamme. Hver gang
> stod vagten ét lag for hoejt.

**Og blokken fanger mere end `foer`.** `gemmer` stod paa Androids liste med fire
navne: boejningsreglen ville laese den som flertal af `gem`, men `gemmer` er
*"gemmer"* — tredje person eller et gemmested. Begge er forkerte som `saves`.

### Reglen, saa den kan kodes

```
1. del navnet paa camelCase/snake_case
2. hver DEL slaas op som et HELT ord i mapningen
3. findes delen ikke, og er den en boejning:
      er stammen i INGEN_FLERTAL?   -> SPRING NAVNET OVER
      ellers                        -> boejningsreglen maa proeve
4. springes en del over, springes HELE navnet over
```

**Og prisen for en for bred spaerring er maalt:** Android proevede reglen i begge
retninger, fordi 11 rigtige navne ellers ville falde tavst — `haendelserFoer`,
`halvlegFoer`, `harDelteHaendelser`, `deltPush`. De er nu modproever i deres
selvproeve.

> Et forbehold, der kun staar i prosaen ved siden af posten, findes ikke for det
> vaerktoej, der laeser posten.

Det er Androids saetning, og den er grunden til, at denne blok er en **blok** og
ikke en saetning i en kolonne.

## A12. `lager` — afgjort ved at LAESE koden, ikke ved at vaelge mellem to tal

**Rute-metoden svarede ikke:** Backend maalte NUL forekomster af `lager` i
`@router`-dekoratorer og NUL i `test_route_navngivning.py`s godkendelsesliste.
Saa afgoerelsen var koordinatorens, og jeg havde afvist ordet paa
`store` 9 mod `storage` 0 — altsaa paa et tal.

| navne | dansk | engelsk | n | hvad kaldestedet siger |
|---|---|---|---|---|
| **10** | lager | `storage` | 0 | **lagerets REPRAESENTATION**, ikke verbet "at lagre". Se nedenfor |

### Hvad de ti funktioner faktisk goer

```python
_slot_til_lager(s: SlotIn) -> dict
    {"spiller_navn": s.player_name, "troejenr": s.shirt_number}
_slot_fra_lager(s: dict) -> dict
    {"player_name": s.get("spiller_navn"), "shirt_number": s.get("troejenr")}
```

**Det er ikke "til lagring". Det er dansk-noegle <-> engelsk-noegle.** API-modellen
er engelsk; det GEMTE dokument (`kamp_opstilling`) har danske noegler, og de ti
funktioner er graenselaget.

`lager` er altsaa et **navneord** om den gemte form — `storage`, ikke `store`.
Og `n = 0` i serveren betyder her ingenting: ordet beskriver et begreb, serveren
ikke har haft et engelsk navn til.

### Og de ti navne er IKKE gaeld — de er en bevidst isolering, der er vogtet

Jeg var paa vej til at skrive et kort om, at `kamp_opstilling`s gemte noegler er
danske og udaekkede. **Svaret stod i koden, adresseret til en koordinator
04-10:**

> Lagerformatet var ALDRIG vogtet af `#116`, kun af `#128`s egne
> Pydantic-feltnavne, som nu tilfaeldigvis ogsaa var lagerets navne. Den
> reelle, NYE risiko er at DENNE oversaettelse kan fejle stille, uden at `#116`
> ser det — **derfor pinning-testen**, ikke en udvidelse af `#116` selv.

Maalt: `test_opstilling_lager_remap.py` findes, og `test_feltnavne.py` har
`test_hvert_kendt_dansk_felt_findes_i_virkeligheden` plus
`test_intet_ukendt_feltnavn`.

**Saa der er intet hul.** De danske noegler er indesluttet bag et graenselag,
risikoen er navngivet, og en test holder den. **At omdoebe de ti funktioner er
derfor den mindst vaerdifulde omdoebning i projektet** — navnene siger allerede
praecis, hvad de goer, og `til_lager`/`fra_lager` er et par, en laeser forstaar.

### Lektien er min

**Jeg naaede at maale fire gange og var naer at oprette et kort om noget, der
var spurgt om og besvaret for to dage siden** — i en kodekommentar skrevet til
en koordinator.

```
jeg maalte   @router (nul) · test_route_navngivning (nul) · #141 · #156 · #114
jeg missede  kommentaren LIGE OVER funktionen
```

Det er samme form som *"filnavne i et repo -> der findes intet svar -> OGSAA
kortets kommentarer"*: **jeg soegte i kort og i ruter, og svaret laa i koden.**


## A13. `efternavn` og `fornavn` — to SAMMENSATTE ord, der skal staa som hele noegler

**Fundet kl. 03:58 ved at foelge Androids hint om `efter`.** De meldte, at et bart
`efter` som HELE navnet blev `after`, og det fik mig til at maale serverens
`efter`-forekomster.

```
app/account.py:144    efternavn = body.last_name.strip()
last_name   22 forekomster      first_name  22
efternavn   11 (dansk)          fornavn     13 (dansk)
```

| navne | dansk | engelsk | n | serverens bevis |
|---|---|---|---|---|
| **11** | efternavn | `last_name` | **22** | `account.py:144` siger det ORDRET: `efternavn = body.last_name` |
| **13** | fornavn | `first_name` | **22** | samme par, samme fil |

### Hvorfor de maa staa som HELE noegler

**SAMMENSAT-reglen ville dele dem forkert:**

```
efternavn  ->  efter + navn  ->  after_name      FORKERT
fornavn    ->  for   + navn  ->  for staar paa den UAFGOERLIGE liste
                                 -> navnet blokeres (ikke forkert, men tabt)
```

`efternavn` er ikke "navnet efter" — det er **slaegtsnavnet.** Og `for` i
`fornavn` er ikke praepositionen; det er praefikset *for-*.

**Mekanismen findes allerede:** laengste-match-foerst mod mapningens egne
noegler. Staar `efternavn` som en noegle, naas `efter` aldrig — praecis som
`aktivitetstyper` forhindrer, at `aktivitet` og `typer` deles hver for sig.

**Saa `efter -> after` fra A11 er uaendret rigtig.** Den temporale betydning er
den rigtige i `row_efter`, `positioner_efter`, `efterregistreret`. Det var
sammensaetningen, der manglede en noegle — ikke ordet, der manglede en
afgoerelse.

### Og de to er ikke de eneste af den slags

```
bagefter · derefter · efterlod       `efter` som DELSTRENG, ikke som del
```

De er daekket af hele-ord-reglen, fordi camelCase/snake_case ikke deler dem.
**Men `efternavn` ER et snake_case-delbart ord**, og det er forskellen: en
delstreng er sikker, en sammensaetning er ikke.

> Hele-ord-reglen beskytter mod DELSTRENGE. Den beskytter ikke mod en
> SAMMENSAETNING, hvis dele hver for sig staar i mapningen.


## A14. `forventet`, og to ord der slet ikke er danske

**Maalt 04:19 paa de ord, der blokerer flest af Backends resterende def-navne.**

| navne | dansk | engelsk | n | serverens bevis |
|---|---|---|---|---|
| **2** | forventet | `expected` | **12** | `expected_status`, `expected_name` — ved siden af `forventet_status` |

### `init` og `dev` stod i INGEN af de to lister

```
init   godkendt engelsk?  NEJ   overdetektion?  NEJ
dev    godkendt engelsk?  NEJ   overdetektion?  NEJ
```

**De blokerede tre def-navne hver**, og de er ikke danske:

```
init   engelsk forkortelse for initialize — universel i kode
dev    forkortelse for development/developer, som repo · api · dsl
```

`init` er **tilfoejet til GODKENDT ENGELSK**. `dev` er tilfoejet til
OVERDETEKTION, hvor `repo`, `dsl`, `hid`, `api` og `json` allerede staar.

**Det er samme hul som `dbu` og `veo` kl. 01:30:** et ord, der ikke er dansk,
men som ingen har sagt noget om, blokerer navne — og hverken ordbogen eller
godkendelseslisten kan afgoere det, fordi det er en **forkortelse.**

### Og to "blokeringer" var min egen maalings graense

```
minutter       flertal af `minut -> minute` — BOEJNINGSREGLEN daekker den
traeningstype  traening + type — SAMMENSAT daekker den
```

Mit maaleskript deler paa `_` og slaar hver del op. **`traeningstype` er ét
token uden underscore**, saa den fandt ingen del i mapningen og meldte ordet som
blokerende. **Det er ikke en blokering; det er at mit instrument ikke kan det,
reglerne kan.**

> Et instrument, der er svagere end reglen, melder reglens arbejde som
> resterende.

Det er samme form som Androids `lineup`-spoegelser: deres vaerktoej kunne ikke
genkende sin egen udgang. Mit kan ikke genkende SAMMENSAT-reglens.

**Saa de to oeverste i min egen blokeringsliste var stoej.** De rigtige
toppositioner er `raekker`/`raekke` (A5b, korrekt blokeret) og de ord, der
staar her.


## A15. TRE MAALINGER paa Androids fund — og en graense, der IKKE skal flyttes

**Maalt 04:35.**

### 1. Androids dual-key-risiko er lukket VED KONSTRUKTION

Android maalte, at *"laes den nye, den gamle som noedfald"* **ikke er en
adfaerdsvagt** i kotlinx: begge navne peger paa samme egenskab, saa **den SIDSTE
noegle i inddata vinder**, uanset hvilken vej annotationerne vender. Deres test
blev groen efter en sabotage, fordi den maalte JSON-raekkefoelgen.

> Baerer de to noegler nogensinde forskellige tal, afgoeres svaret af noeglernes
> RAEKKEFOELGE i serverens JSON — noget ingen af siderne har valgt bevidst.

**De kunne ikke maale det; det kraever serveren. Maalt:**

```
"afbud_by_aarsag":                 afbud_by_aarsag
"absence_by_reason":               afbud_by_aarsag          SAMME variabel
"kamp_minutes":                    kamp_minutes
"match_minutes":                   kamp_minutes             SAMME
"position_totals_by_team_kampe":   position_totals_by_team_kampe
"position_totals_by_team_matches": position_totals_by_team_kampe
load_weekly:  entry["matches_count"] = entry["kampe_antal"]  en TILDELING
```

**Hvert par kommer fra EEN kilde. De kan ikke afvige.** Saa risikoen er aegte i
princippet og lukket i praksis.

**Men den er lukket af en IMPLEMENTERING, ikke af en regel.** Skrives et
fremtidigt dual-key-par fra to udtryk, afgoer JSON-raekkefoelgen svaret — og
ingen test paa nogen af siderne vil se det.

```
REGEL   et dual-key-par skal skrives fra SAMME udtryk, aldrig fra to
```

### 2. `matches_count` er noeglen; `matchCount` er navnet, og det er Androids

De spurgte, om `matches_count` skal styre Kotlin-navnet. **Nej.** Noeglen er
serverens og er udrullet; navnet er deres, og engelsk saetter ental foran
`Count`.

**De er ikke afledt af hinanden** — det er netop noegle/navn-skellet, hele
projektet hviler paa. `@SerialName` fryser noeglen, saa navnet er frit.

### 3. RETTELSE: A11's ordstillingsregel hvilede paa EET eksempel

Jeg afgjorde `_antal_perioder -> period_count` ud fra `halves_count` (30
forekomster) og kaldte det *"serverens egen form"*. **Maalt nu:**

```
group_count      ENTAL          session_count   ENTAL
halves_count     FLERTAL        matches_count   FLERTAL   (ny, #180)
trainings_count  FLERTAL        (ny, #180)
```

**Serveren er BLANDET**, og de to nye flertalsformer er dem, `#180` selv lagde
ind efter min regel. **Reglen er rigtig om POSITIONEN** — navneordet foerst,
`count` sidst — **og siger intet om TALLET.** Jeg praesenterede `halves_count`
som konventionen; den var eet af fem.

### Og en graense, der IKKE skal flyttes: vektorernes egne danske noegler

```
logic/vectors/*.json   "gult_kort_min_sek" m.fl. — DANSKE noegler
klienterne             fem referencer, ALLE i testfiler
serveren               "gult_kort_min" staar EEN gang: i et migrations-par
                       i db.py, ved siden af "yellow_card_minutes"
```

Vektorernes noegler er **deres eget ordforraad**, ikke serverens ledning. Og at
omdoebe dem kraever: regenerering af alle vektorfiler **plus** samtidig
aendring i tre platformes tests.

**Det er samme klasse som lagerformatet i A12** — en bevidst isolering, hvor
dansk er indesluttet bag en graense. Og den naeste, der grepper efter danske
noegler, vil finde dem: **de er ikke gaeld.**


## A16. `oversigt` — afgjort af en RUTE, og fire defekter i mit eget instrument

**Maalt 06-10 kl. 04:51-04:56 paa `PlayerData_Backend@origin/master`.**

| dansk | engelsk | bevisklasse |
|---|---|---|
| oversigt | `overview` | **RUTEN.** `dbu.py:917` `@router.get("/player-overview")` og `dbu.py:918` `def get_spilleroversigt(...)` — handleren for ruten. Frigiver `get_spilleroversigt` og `compute_spilleroversigt` til `get_player_overview` / `compute_player_overview` |

Det er fjerde ord afgjort paa en rute (`#171` -> `/me`, `kampprogram` ->
`/fixtures`, `nu` -> `/sync-now`), og **igen havde ruten ligget der hele
tiden.** En rute er en kontrakt: den er staerkere end et forekomsttal, fordi
to klienter allerede kalder den.

### Men det vigtigste i dette afsnit er instrumentet, ikke ordet

Min foerste maaling meldte **117 blokerende ord**, med `traeningstype`,
`minutter`, `init` og `dev` oeverst. Alle fire er **reglens eget arbejde**,
ikke resterende arbejde:

```
traeningstype   SAMMENSAT: traening + s + type   — type er godkendt engelsk
minutter        BOEJNING af minut -> minute      — raekke 557 i denne fil
init            GODKENDT ENGELSK siden A14
dev             OVERDETEKTION siden A14
```

**Fire defekter, og hver af dem fik instrumentet til at melde for meget:**

```
1  ingen boejningsregel          minutter, raekker meldt som ukendte
2  SAMMENSAT krav: ALLE dele i   traeningstype fejlede paa sin engelske hale
   mapningen
3  laeste /usr/share/dict/words  de 231 godkendte ord var usynlige
   i stedet for projektets liste
4  sliced ved '## 2.' og kaldte  26 af 231 ord laest
   det listen
```

Efter rettelserne: **48 navne kan omdoebes nu**, 188 er helt engelske, 13
venter paa A5b. Og `minutter` var stadig med — fordi `minut -> minutter`
**fordobler konsonanten**, og min boejningsregel kan ikke se en stammeaendring.
Det er praecis Androids `regel -> regler`, og graensen staar nu maalt to steder.

**Formen er dagens, paa et vaerktoej i stedet for en maaling:** et instrument,
der er svagere end reglen, melder reglens arbejde som resterende. Og det er
samme familie som `check-parent-cards.sh`s usorterede hentning og Androids fem
"ligner dansk"-maalere — en kontrol, der altid siger det samme, ligner en
kontrol, der virker.

**Konsekvensen hvis den ikke var fanget:** jeg havde sendt Backend en liste med
fire ord, som listen allerede havde afgjort, og bedt dem maale dem igen.

## A17. FIRE ORD TIL ANDROIDS BLOKERINGSLISTE — to paa en RUTE

**Maalt 06-10 kl. 05:20-05:25 paa `PlayerData_Android@origin/HEAD`**, efter at
Android havde konsumeret A16 og omdoebt seks navne (`d3ff7cc`).

```
INTERVAL  2292 deklarationer i 199 filer (app/src/main + core)
          548 helt engelske · 524 kan omdoebes NU · 101 venter paa A5b
          960 navne blokeret af EET ord, fordelt paa 578 ord
```

| dansk | engelsk | bevisklasse |
|---|---|---|
| filtre | `filters` | **RUTEN:** `@router.get("/filters")`. Dansk flertal af et godkendt engelsk ord — posten er derfor `filtre`, ikke `filter` |
| indstillinger | `settings` | **RUTEN:** `@router.put("/settings")` |
| regel | `rule` | serveren: `age_rules`, `format_rules`, `form_rules`, `rules` — fire tabeller |
| ud | `out` | serveren: `live.py:221` `out_name: str = ""`, og kommentaren ved siden af goer det utvetydigt: det er **udskiftningens** spiller, der gaar UD. `logUd` -> `logOut` |
| regler | `rules` | flertal af ovenstaaende, men stammen aendrer sig (`regel` -> `regler`), saa boejningsreglen naar den ikke. Serveren: `age_rules`, `format_rules` |

**`filtre` og `indstillinger` er femte og sjette ord afgjort paa en rute**, og
som de fire foer dem havde ruten ligget der hele tiden. En rute er en kontrakt:
to klienter kalder den allerede, saa valget er truffet og kan ikke forhandles.

### `regel` er den, der kraever en note — og det er Androids egen

`regel -> regler` **aendrer stammen** (`regel`, ikke `regelr`), og
boejningsreglen kan ikke se en konsonant- eller vokalaendring. Samme klasse som
`minut -> minutter`, der fordobler.

```
regel   ->  rule        posten
regler  ->  rules       SKAL staa eksplicit, boejningen naar den ikke
```

Det er anden gang samme graense rammer, og begge gange blev den fundet af
Android i deres eget trae. **En boejningsregel, der kun haandterer suffikser, er
ikke en boejningsregel for dansk** — den er en for de regelmaessige.

### To gik til A5b i stedet, og den anden var en overraskelse

`plads` var jeg ved at afgoere som `slot`, fordi serverens lineup-ord er
`slot_id`/`slots`/`start_slots`. **Men `position` er ogsaa serverens, ogsaa
godkendt, og ogsaa et lineup-ord** — og `indPaaNyPlads` handler om en position,
mens `valgtTomPlads` handler om en slot.

Og i SAMME linje stod den anden: `indPaaNyPlads(kode: String, ...)`.
**`kode` er her en plads-kode**, mens `adgangskode` allerede ejer `password`.
Et ord kan altsaa vaere afgjort i ét sammensat ord og uafgjort alene.

**Det var kaldestedet, der viste begge** — ikke tallet, og ikke serverens
vokabular. Havde jeg afgjort `plads -> slot` paa serverbeviset alene, havde
`indPaaNyPlads` faaet et navn, der beskriver det forkerte begreb.

### `ud` er TO BOGSTAVER — den farligste post paa hele listen

Maalt i Androids trae, ord der BEGYNDER med `ud` og intet har med `out` at goere:

```
udkast        draft          udvisning     sin bin / dismissal (A5b)
udtaget       selected       udled         derive
udfoer        execute        udbredelse    distribution
```

En mekanisk erstatning af `ud -> out` ville give `outkast`, `outtaget`,
`outvisning`, `outled` — og **hver enkelt ville kompilere.** Det er praecis
`hold -> team`-faelden fra afsnit C2, men paa to bogstaver i stedet for fire,
saa den rammer bredere.

```
TILLADT    ud som et HELT ord i en camelCase-/snake_case-opdeling
           logUd -> logOut · ud -> out
FORBUDT    ud som delstreng. ALDRIG
```

Og posten er kun vaerd at have, fordi `logUd` og `ud` er hyppige nok (5 navne).
**Er du i tvivl om dit vaerktoej respekterer ordgraenser, saa spring denne
post over** — fem navne er ikke vaerd en tavs oversaettelse af `udkast`.

## A18. iOS' 237-liste er 60 % AFGJORT ALLEREDE — og fire nye ord

**Maalt 06-10 kl. 05:25.** iOS committede
`docs/omdoebning-124/frigoer-flest-navne.md` med 237 navne, der er ÉT ord fra at
kunne omdoebes. **Dokumentets eget hoved siger 167 ord; den committede TABEL
lister 60 ord / 130 navne** og slutter midt i listen ved `udskiftning`. Tallene
nedenfor har derfor naevneren 130, ikke 237.

```
INTERVAL  60 ord / 130 navne — den committede tabel, ikke hovedets 167/237
          maalt mod mapningens 126 poster, A5b's 15 og 239 godkendte ord

ALLEREDE AFGJORT i den nuvaerende liste    30 ord /  78 navne   60 %
staar i GODKENDT ENGELSK, lad staa          3 ord /   5 navne
i A5b, kan IKKE afgoeres                    7 ord /  18 navne
MANGLER fortsat                            20 ord /  29 navne
```

**Grunden er ikke, at ordene mangler. Dokumentets hoved siger det selv:
`ordlisten ea21395`** — og det er submodul-pinnen, der staar **23 commits bagud**
(`PlayerData_Backend#177`). iOS laeser en liste fra i formiddags.

De ord, deres doc kalder uafgjorte, og som er afgjort nu:

```
navn -> name        faelles -> shared     er -> is          klub -> club
aktivitet -> activity  tjek -> check      tider -> times    nulstil -> reset
minut -> minute     logik -> logic        holdkort -> team_card
troeje -> shirt     tidslinje -> timeline straffe -> penalty
personlige -> personal  nummer -> number  notifikations -> notification
klubber -> club (boejning)
```

**Det er `#177`s pris, maalt.** Ikke et argument om arkitektur — 78 navne, som
to sessioner kunne have omdoebt, men som ser blokerede ud fra iOS' side.

### Fire ord afgjort af de 20, der faktisk manglede

| dansk | engelsk | bevisklasse |
|---|---|---|
| glemt | `forgot` | **RUTEN:** `@router.post("/forgot-password")`. `glemtAdgangskode` -> `forgotPassword`. **Syvende ord afgjort paa en rute** |
| udskiftning | `substitution` | serveren: `"substitutions"`, og `substitution` staar i GODKENDT ENGELSK. Begge sider stemmer |
| paakraevet | `required` | serveren: `"required"` |
| vaerdi | `value` | serveren: `fixed_value`, `legacy_value`, `field_value_legality` · `value` er godkendt |

**Og `stat` er ikke dansk** — den hoerer i GODKENDT ENGELSK. `stats` stod der
allerede; `stat` gjorde ikke. Praecis samme hul som `total`/`totals` 06-10 kl.
01:40, og det er anden gang en entalsform mangler ved siden af sin godkendte
flertalsform. **Naar du godkender et flertal, maal om entallet ogsaa skal med.**

### Tre jeg IKKE afgjorde, og hvorfor

**`trin`** ser afgjort ud: serveren har `step_balance_l`/`step_balance_r`. Men
det er GPS-SKRIDT, og `KontoTrin` er et trin i en guide. **Samme ord,
forskellig model** — faelden fra `TraeningsType`/`activity-types` 04-10. Lad
kaldestedet afgoere.

**`kladde` og `udkast` er BEGGE "draft"** — `kladde` hos iOS (1 navn), `udkast`
hos Android (5). To danske ord mod ét engelsk er tilladt, naar de er synonymer,
men det skal besluttes og ikke opstaa: afgoer om de daekker samme begreb i
koden, foer begge faar `draft`. Det er `fjern`/`slet`-kollisionen igen, bare
uden en maaling endnu.

**`markering`** har intet serverbevis, og `marker` i listen er BYDEFORMEN
(«marker dette»), ikke navneordet. `periodeMarkering` kan vaere `marker` eller
`marking`, og de to betyder ikke det samme.

## A19. RUTERNE SVEJET SYSTEMATISK — og `holdkort` var forkert

**Maalt 06-10 kl. 05:33.** De syv foregaaende rute-afgjorte ord blev fundet
ét ad gangen. Ruten og dens handler staar i PAR i kilden, saa klassen kan
udtoemmes paa én maaling:

```
INTERVAL  96 rute/handler-par i backend/app/ @origin/master
          25 par hvor handler OG rute hver har ord, den anden ikke har
```

### Fem nye ord, laest direkte af kontrakten

| dansk | engelsk | ruten |
|---|---|---|
| bekraeftelse | `confirmation` | `@router.post("/resend-confirmation")` paa `send_bekraeftelse_igen` |
| stilling | `standings` | `@router.get("/standings")` paa `get_stilling` |
| laas | `lock` | `@router.post("/lock")` paa `tag_laas` (`live.py:1176`) |
| traeningstype | `training_type` | `@router.post("/api/training-types")` paa `add_traeningstype`. Ruten afgoer det SAMMENSATTE ord direkte, saa SAMMENSAT behoever ikke gaette |
| genskab | `restore` | `@router.post("/families/{family_id}/restore")` paa `genskab_familie`. Backend fandt den selvstaendigt samme nat og har udfoert den |

### Og to TIE-BREAKS til A5b — som IKKE flytter ordene ud af A5b

```
@router.get("/divisions")   paa  get_raekker          raekke     = division HER
@router.get("/lineups")     paa  get_opstillinger     opstilling = lineup   HER
```

**Det er de to stoerste enkeltposter paa hele listen** (Androids maaling: 51 og
19 navne). Men ruten afgoer kun den betydning, RUTEN handler om:

```
AFGJORT af ruten   DBU-raekken = division   ·  opstillingslisten = lineup
STADIG A5b         GpsRaekke/BaenkRaekke/HistorikRaekke  = row
                   match_lineup.formation                = formation
```

Begge bliver derfor i A5b med ruten tilfoejet som tie-break for den navngivne
betydning. **At flytte dem ud ville goere `GpsRaekke` til `GpsDivision`.**

### `holdkort -> team_card` var FORKERT, og beviskolonnen sagde hvorfor

Posten stod som afgjort med beviset *"ruten `get_team_card_players`"*.
**`get_team_card_players` er et FUNKTIONSNAVN, ikke en rute.** Den rigtige rute
og serverens egne tabeller siger noget andet:

```
@router.get("/teamsheet-players")      dbu.py:438
CREATE TABLE dbu_teamsheets            db.py:298
CREATE TABLE dbu_club_teamsheets       db.py:330
db.py:270  "Spillerens navn som det optraeder i DBU's egne holdkort
            (dbu_teamsheets.navn)"     <- serveren oversaetter ordet SELV
```

**Forekomster, maalt i alle tre traeer:**

```
            team_card   teamsheet
backend          6         83
Android         47         18
iOS             21         12
```

Serverens seks er: handleren selv, to kommentarer OM den, en test og
ratchet-filen. **Alt andet i serveren hedder `teamsheet`.**

Og klienterne **kalder ruten**: `DbuRepository.kt` og
`DBURepository+Datainput.swift` henter begge `/teamsheet-players` — og har
navngivet deres modeller `TeamCard`, `TeamCardPlayer`, `TeamCardPlayersResponse`
efter handleren. Samme begreb, og det engelske ord er laest af det forkerte sted.

**Posten er rettet til `holdkort -> teamsheet`.**

### MEN: de 68 eksisterende `team_card`-navne roeres IKKE i nat

47 hos Android, 21 hos iOS. At rette dem er mekanisk, men det er **arbejde, der
allerede er gjort, som skal gores om** — og prioriteringen af det er Mortens,
ikke min. Posten er rettet, saa intet NYT navn bliver forkert; de gamle staar
som kendt gaeld, til han har set regningen.

**Og fejlformen er projektets mest gentagne, nu inde i selve listen:** et
funktionsnavn blev laest som en kontrakt. Samme fejl som `kampdato -> match_date`
(jeg maalte `_parse_match_date`), som Backends `FaellesKamp`-flag (de maalte
Swift-egenskabens navn), og som `live_contract.py` findes for at forhindre.

> En rute er en kontrakt. En `def` er en implementering. De staar paa samme
> linje i kilden, og kun den ene binder.

## A20. RETTELSE AF A19: `holdkort` er TO BEGREBER, og jeg tog fejl to gange

**Maalt 06-10 kl. 05:42, under en time efter A19.** A19 rettede
`holdkort -> team_card` til `holdkort -> teamsheet` og kaldte de 68 eksisterende
navne for gaeld. **Begge dele var forkerte.**

### Ordet daekker to begreber, og de har hver sit rigtige engelske ord

```
1  DBU'S HOLDKORT          den officielle opstilling, synket fra DBU
   serveren   dbu_teamsheets · dbu_club_teamsheets · /teamsheet-players
   iOS        func holdkortSpillere() · HoldkortSpillereSvar   STADIG DANSK
   Android    Dbu*.kt, 25 forekomster, bruger team_card        <- GAELDEN

2  ET STATISTIK-KORT        et opsummeringskort i brugerfladen
   Android    object TeamCard   core/stats/PositionBane.kt:108
              var object HoldKort, omdoebt i 6ab9ec7
   iOS        enum TeamCard     Core/Stats/StatsLogik.swift:435
   begge      KampeSektion — nogletal, deltagelse, Fremgangsbar
```

**`object HoldKort -> object TeamCard` staar i Androids egen historik.** Det
danske ord for begge begreber ER `holdkort`, og `team_card` er det RIGTIGE
engelske ord for det andet. Et kort i en brugerflade er et `card`.

**Posten hoerer derfor i A5b**, ikke i mapningen. Praecis samme form som
`opstilling` (`lineup` for tabellen, `formation` for kolonnen) — og `opstilling`
staar i A5b af den grund.

### Hvad gaelden FAKTISK er

```
A19 sagde                 68 navne paa to platforme skal rettes
maalt                     25 forekomster, KUN hos Android, kun i Dbu*-filerne

Android stats-siden        19 forekomster   KORREKT, roer dem ikke
iOS i alt                  21 forekomster   17 er stats-kortet + tests,
                                            4 er deres egne plan-JSON'er
iOS' teamsheet-kode        STADIG DANSK     de faar det rigtige ord fra
                                            foedslen, ingen gaeld
```

**iOS har NUL fejlnavngivne teamsheet-identifikatorer.** Min A19-anbefaling til
Morten om at rette 68 navne paa to platforme ville have sendt iOS ud paa en
omdoebning af nul navne og Android ud paa at oedelaegge 19 korrekte.

### Og fejlen var den samme begge gange, inden for en time

```
A19   jeg maalte strengen team_card i tre traeer og konkluderede om BEGREBET
A20   jeg maalte, at ruten siger teamsheet, og konkluderede at ordet kun
      har den ene betydning
```

**Begge gange maalte jeg en streng og konkluderede om en brug.** Det staar
som nattens mest gentagne fejlklasse i koordinatorens CLAUDE.md, med seks
tidligere tilfaelde — og jeg lavede den to gange i traek paa samme post, hvoraf
den anden var mens jeg rettede den foerste.

**Hvad der ville have fanget den foerste gang:** at spoerge, hvor forekomsterne
BOR, foer jeg talte dem. `git grep -l` paa mappenavne tog tyve sekunder og
delte 44 forekomster i 25 + 19 paa to begreber.

> Et tal over forekomster af et ord er ikke et tal over navne paa ét begreb.
> Og et ord, to platforme har valgt UAFHAENGIGT af hinanden, er sandsynligvis
> rigtigt.

Det sidste er det staerkeste signal, jeg gik forbi: **Android og iOS valgte
`TeamCard` hver for sig til statistik-kortet.** To uafhaengige valg, der falder
sammen, er et argument — ikke en fejl, der skal rettes.

## A21. FEJLNOEGLERNE ER OGSAA EN KONTRAKT — syv ord, svejet systematisk

**Maalt 06-10 kl. 06:04.** `valider` blev afgjort af
`error.shared.invalid_halves_count`. Det peger paa en kontraktflade, jeg ikke
havde svejet: **serverens fejlnoegler staar PARVIS med deres danske
beskedtekst**, og klienterne sammenligner paa noeglen.

```
INTERVAL  53 distinkte error-noegler i backend/app/ @origin/master
          60 forekomster MED dansk beskedtekst ved siden af
```

Formen er den samme som rute/handler-parret i A19: to sider af samme linje, hvor
den ene er engelsk og bindende.

```python
ApiError(400, "error.account.wrong_password", "Forkert adgangskode")
                           ^^^^^                ^^^^^^^
```

### Syv ord

| dansk | engelsk | fejlnoeglen |
|---|---|---|
| ukendt | `unknown` | `error.sessions.unknown_training_type` = *"Ukendt traenings-type"* · `error.shared.unknown_tournament_for_group` = *"Ukendt staevne for denne pulje"* — **to noegler** |
| forkert | `wrong` | `error.account.wrong_password` = *"Forkert adgangskode"* · `error.password.wrong_current` = *"Forkert nuvaerende adgangskode"* — **to noegler** |
| ugyldig | `invalid` | `error.auth.link_invalid_or_expired` = *"Linket er ugyldigt eller udloebet"*. `invalid` staar i syv noegler i alt |
| udloebet | `expired` | samme noegle, anden halvdel. `expired` stod allerede i GODKENDT ENGELSK |
| forsoeg | `attempt` | `error.ratelimit.too_many_attempts` = *"For mange forsoeg"* |
| registrering | `registration` | `error.shared.tournament_has_registrations` = *"Staevnet har kampe med registreringer"* |
| registreringer | `registrations` | samme — eksplicit, saa boejningsreglen ikke skal gaette |
| fil | `file` | `error.live.gps_file_empty` = *"Filen indeholder ingen brugbare raekker"* |

**`registrering` er den tungeste:** 12 navne hos Android, 14 hos iOS
(`Registrering`, `RegistreringerSvar`, `PushRegistrering`,
`antalRegistreringer`). Den stod paa iOS' egen liste over resterende ord.

### `fil` har en delstrengsfaelde paa DANSK side

```
fil matcher ogsaa    filter · filtre · Filters · FilterMenu · DBUFiltre
```

Maalt: Androids 25 og iOS' 23 `fil`-traef er for de flestes vedkommende
`filter`. **De aegte er `cachefil`, `databasefil`, `eksportFil`, `DelbarFil`,
`filen`.** Og `filtre -> filters` staar allerede paa listen fra A17, saa de to
poster ligger oven i hinanden som strenge. **Helt ord, altid.**

### Og `side` gaar til A5b — en form, ingen tidligere post har

```
dansk  Side        = en SIDE/skaerm       HoldkortSide · baggrundSiden
engelsk side       = holdets SIDE         TeamSide · assistSide · scorerSide
```

**Det danske ord og det GODKENDTE ENGELSKE ord er den samme streng.** Serveren
har `match_sides`, `"side"`, `"home"`, `"away"` for holdsiden, og klienterne
bruger den allerede paa engelsk. Samtidig er `error.main.page_gone` =
*"Siden er ikke laengere tilgaengelig"* en web-side.

Et vaerktoej kan ikke skelne `HoldkortSide` fra `TeamSide` paa strengen, og en
mekanisk `side -> page` ville omdoebe holdsiden. **Posten kan derfor ikke staa i
mapningen overhovedet** — samme begrundelse som `opstilling` og `holdkort`, men
af en ny grund: ikke to engelske kandidater, men ét dansk ord der KOLLIDERER med
et godkendt engelsk.

### Hvad jeg IKKE afgjorde af de 60

```
udfyldes -> required     noeglen siger `required`, men dansk siger
                         "skal udfyldes" — en semantisk match, ikke et ordpar
tilgaengelig -> gone     noeglen er `page_gone`, teksten "ikke laengere
                         tilgaengelig". To forskellige udsagn
brugernavn -> username   error.auth.username_required = "Familienavn skal
                         udfyldes", men error.admin.invalid_credentials =
                         "Forkert brugernavn". Samme engelske ord til
                         familienavn OG brugernavn — maal kaldestedet
```

**De tre er fejlformen, metoden inviterer til:** noeglen og teksten staar paa
samme linje, saa det er nemt at laese dem som en oversaettelse. **De er en
PAASTAND og en BESKED om samme fejl** — ikke to udgaver af samme ord. Et ordpar
kraever, at det danske ord og det engelske ord betegner det samme, ikke blot at
de staar i samme `ApiError`.

## A22. `opret` var afgjort i en BESKED og aldrig skrevet ned

**Maalt 06-10 kl. 06:50, fordi `opret` blokerede 8 navne hos Android.**

Jeg skrev til Backend kl. 05:20, at `opret -> create` var *"din afgoerelse,
udfoert"*. **De udfoerte den. Jeg skrev den aldrig paa listen.**

```
verificeret i Backends trae @origin/master
  create_shared_match      (var opret_faelles_kamp)
  create_tournament        (var opret_stoevne)
  def opret_*              NUL tilbage
```

| dansk | engelsk | bevis |
|---|---|---|
| opret | `create` | Backends to handlere, omdoebt og udrullet. `create` stod allerede i GODKENDT ENGELSK |

**Konsekvensen var maalbar:** Android kunne ikke bruge ordet, fordi deres
vaerktoej laeser listen — ikke mine beskeder. 8 navne stod blokeret paa et ord,
der var afgjort for halvanden time siden.

> Et ord afgjort i en besked er afgjort for ÉN session. Listen er det eneste
> sted, der gaelder for alle tre.

Det er ottende gang i dette projekt, at noget baerende kun fandtes ét sted, og
foerste gang det var et ORD. Reglen staar nu: **afgoer du et ord i en besked,
skriv det paa listen i SAMME tur** — praecis som en maaling hoerer paa kortet og
ikke kun i et relay.

### Og en instrumentfejl i selve maalingen

Jeg ledte efter det nye navn som `def create_faelles_kamp` — altsaa mit GAET
paa resultatet, hvor kun ét ord var skiftet. **Backend havde omdoebt hele
navnet**: `opret` + `faelles` + `kamp` -> `create` + `shared` + `match`. Min
grep gav nul og modsagde deres melding, saa jeg maalte igen.

```
jeg soegte efter   mit gaet paa det nye navn
jeg burde soege    hvad der ER der   (git grep 'def create_\w+')
```

**Et nul, der modsiger en melding fra en session, der har maalt, er mit
instrument — ikke deres fejl.**

## A23. `kladde` og `udkast` er BEGGE `draft` — og det er besluttet, ikke opstaaet

A18 flagede kollisionen: to danske ord, ét engelsk maal. **Maalt i Androids
kode, og de er samme begreb i to roller:**

```
core/datainput/Kladde.kt:145    data class Kladde          OBJEKTET
core/datainput/Kladde.kt:35     data class KladdeHaendelse
features/datainput/
  DatainputModel.kt:111         UDKAST_OPHOLD_MS = 1_000L  MEKANISMEN
  DatainputModel.kt:382         udkastJob                  (autogem)
  MainActivity.kt:70            gemUdkastNu()
```

`Kladde` er kladden; `udkast` bruges om det at GEMME den. Begge er `draft` paa
engelsk, og sammenfoejningen skaber ingen navnekollision —
`Kladde -> Draft`, `gemUdkastNu -> saveDraftNow`, `udkastJob -> draftJob`.

| dansk | engelsk | note |
|---|---|---|
| kladde | `draft` | objektet |
| udkast | `draft` | samme begreb, brugt om autogem-mekanismen. **Bevidst sammenfoejning** |

**To danske ord mod ét engelsk er tilladt, naar de er synonymer** — men det
skulle BESLUTTES, ikke opstaa af to uafhaengige poster. Det er det, A18 bad om,
og her er maalingen bag.

### Og `udviste` arver A5b

`udviste` blokerer 5 navne (`egneUdviste`, `udviste`). Den er boejning af
`udvise`, som hoerer til `udvisning` — og **`udvisning` staar i A5b**, fordi
`beregning.py` skelner *"midlertidig udvisning"* (10 min) fra *"direkte
udvisning"* (roedt kort). `udviste` kan ikke afgoeres, foer den gor.

## A24. `oedelagt` afgjort paa syv kaldesteder — og `biometri` er en TREDJE fejlform

**Androids maaling 06-10 kl. 07:11**, efter at de tog de ni A9-ord. Otte
blev afgjort paa kaldestederne; `raekker` gik i A5b som forudsagt
(`gpsRaekker: List<GpsRowRaw>` er rows, `dbuRaekker` er divisions).

| dansk | engelsk | bevis |
|---|---|---|
| oedelagt | `corrupted` | **alle syv kaldesteder laest:** `cacheOedelagt`, `gpsOedelagt`, `tidslinjeOedelagt`, `veoLinksOedelagt` saettes ALLE, naar en gemt JSON ikke kunne AFKODES. `broken` findes ikke i materialet — der er ingen "oedelagt" i betydningen *"virker ikke"* |

**Og hvorfor den maaling er bedre end et valg**, med Androids egne ord:

> Havde jeg stoppet ved `cacheOedelagt` og `gpsOedelagt`, havde jeg faaet samme
> svar — men uden at vide, at jeg havde det.

Det er forskellen paa et rigtigt svar og et maalt svar, og den er ikke
akademisk: det foerste kan ikke efterproeves af den naeste, der laeser posten.

### `biometri` hoerer IKKE paa listen, og grunden er ny

```
biometriFejl       tillaegsord   -> biometricError
val biometri       navneord      -> biometrics
loginMedBiometri   navneord      -> loginWithBiometrics
```

**Formen skifter med POSITIONEN i navnet, ikke med betydningen.** En post
`biometri -> biometrics` giver `biometricsFejl`; en post `-> biometric` giver
`loginWithBiometric`. **Begge er forkerte halvdelen af gangene**, og et
ord-for-ord-opslag kan ikke se hvilken.

Det er ikke tvetydighed i A5b's forstand — der er ÉN betydning. Det er, at
**engelsk kraever to former af samme ord afhaengigt af position.**

**Listen har nu tre fejlformer ved siden af hinanden:**

```
ORDSTILLING   antal      engelsk vender sammensaetningen om
FLERTAL       kort       et dansk ord uden flertal taber sit tal
FORM          biometri   engelsk kraever tillaegsord ELLER navneord
                         afhaengigt af positionen i navnet
```

Android lagde den **heller ikke** paa navne-niveau: 12 navne er for mange at
afgoere i haanden uden en regel, og der findes ingen regel, der kan vaelge
formen. **Den staar som uafgjort**, og det er det rigtige svar — ikke en
mangel.

### Og den anden halvdel af `opret`-fejlen, som er deres

A22 beskrev, at jeg afgjorde `opret` i en besked og aldrig skrev den ned.
**Androids tilfoejelse er den, der goer fejlen alvorlig:**

> Vi kan ikke se, at et ord mangler. Et blokeret navn ser ud praecis som et
> navn, der venter paa en beslutning, der ikke er taget endnu. Der er ingen
> fejl at opdage — kun et tal, der ikke falder.

```
set fra koordinatoren   jeg glemte at skrive ordet ned
set fra sessionen       et navn er blokeret — som alle de andre blokerede
                        ingen fejl, ingen advarsel, bare et tal der staar stille
```

**Derfor er "skriv ordet paa listen i SAMME tur" den eneste side, der kan lukke
det.** Modtageren har ingen maaling, der kan opdage et ord, der mangler, fordi
et manglende ord og et uafgjort ord er samme tilstand hos dem.

Og de laeser listen **med vilje** frem for mine beskeder: *"en besked kan jeg
ikke efterproeve, og en liste kan jeg hente igen i morgen."*

## A25. RETTELSE til A19's raekke-tie-break: et PRAEFIKS siger KILDEN, ikke typen

**Android maalte mig 06-10 kl. 08:09, og de har ret.**

A19 tilfoejede `/divisions` som tie-break for `raekke`, og det er korrekt for
SERVERENS DBU-begreb. **Men jeg brugte den derefter til at klassificere
KLIENT-navne, og to af dem ramte forbi:**

```
jeg meldte   dbuRaekker · egneRaekker   = divisions   (fordi "dbu" og ruten)
maalt        begge returnerer KampeLogik.Raekke       = ROWS
```

### Og grunden er, at der findes TO `Raekke`-typer med samme stavning

```
core/dbu/DbuModeller.kt:32    data class Raekke(raekkeId, navn, rangering)
                              en DBU-RAEKKE (division)
core/kampe/KampeLogik.kt:23   data class Raekke(puljeid, kampnr, slags,
                              holdLabel, dato)
                              en VIST raekke i Kampe-listen
```

`KampeModel:272` `dbuRaekker = egne.flatMap { MatchesLogic.fromDbu(...) }` giver
den ANDEN — altsaa en vist raekke, bygget FRA DBU-data.

> **Praefikset `dbu` siger KILDEN, ikke typen.**

Det er en ny underform af nattens skelet, og den er lumsk, fordi praefikset
LIGNER en typeangivelse:

```
jeg laeste       et praefiks (dbu, egne, faelles)
jeg konkluderede hvad objektet ER
det rigtige maal RETURTYPEN
```

### Den rettede deling, otte navne, hver maalt paa sin returtype

```
ROWS        gpsRows (var gpsRaekker) · spillerRaekker · baenkRaekker
            dbuRaekker · faellesRaekker · egneRaekker
DIVISIONS   stillingRaekker · kampRaekker
            (begge baerer raekkeNavn/raekkeNoegle fra DBU)
```

**A19's rute-tie-break staar uaendret for SERVERENS navne** —
`fetch_divisions`, `_scan_divisions`, `get_divisions` var rigtige, og Backend
har udfoert dem. Fejlen var at tage den med over graensen til klientnavne.

### Og `Raekke` selv kan ikke omdoebes af et vaerktoej

To typer, samme stavning, modsatte betydninger. **Et vaerktoej, der matcher paa
stavning, kan ikke skelne dem** — og et forkert valg giver en kompileringsfejl i
bedste fald og en forkert type i vaerste.

Den hoerer i en haand-runde, hvor hver fil afgoeres for sig og compileren er
dommeren. **Den staar som uafgjort, ikke som resterende arbejde.**

### Hvad der gjorde, at den blev fanget

Jeg skrev til Android: *"maal det, for `GpsRaekke` er stadig en `row`."* De
maalte, **og advarslen var berettiget i en retning, jeg ikke havde regnet med**
— ikke at de to jeg ikke kendte var rows, men at de to jeg havde TILFOEJET var
det.

Det er sjette gang i nat, samme skelet: **jeg maalte eet lag og konkluderede om
det naeste.** Og de fem foerste blev fanget af en session, der laeste sin egen
kilde. Ogsaa denne.

## A26. NAAR DET ENGELSKE ORD ER TAGET: navnet faar sin ROLLE med

**Afgjort af koordinatoren 06-10-2026 kl. 10:04. Det er en navnekonvention, ikke
en produktbeslutning — samme slags som C3, og derfor ikke Mortens.**

### Problemet, maalt paa to platforme uafhaengigt

```
iOS      41 navne tilbage i #124 runde 3, og de ER blokerede af netop dette:
         Activity · Date · Color · Key · Group · Result · State · name · count
         -- det nye navn findes ALLEREDE som framework- eller egen reference
Android  open ER et Kotlin-noegleord (et bloedt), og dbu blokerede 17 navne
```

**Dette er IKKE A5b.** De to forveksles let, og forskellen afgoer, hvem der kan
loese det:

```
A5b    det DANSKE ord betyder to ting      side · min · kort · plads · kode
       -> kan ikke afgoeres paa listen, afgoeres paa NAVNET
A26    det ENGELSKE ord er allerede taget  open · date · state · name · count
       -> mapningen er RIGTIG, navnet skal bare baere sin rolle
```

A5b er en tvetydighed i kilden. **A26 er en kollision i maalet**, og den har et
mekanisk svar.

### Reglen

**Et navn er ikke et ord. Naar det engelske ord kolliderer, tager navnet sin
ROLLE med** — og rollen laeses paa kaldestedet, ikke paa listen.

Androids maalte forlaeg, 06-10 (`d9d6324`), og det er grunden til at reglen
staar her og ikke er et forslag:

```
aaben   fire Booleans      ->  isOpen        ikke open
aabn    en PendingIntent-val ->  openIntent  ikke open
```

> `aaben` og `aabn` er samme ord i to former. Begge er `open` paa engelsk —
> `isOpen` mod `open()` — modsat `biometri`, hvor engelsk kraever to
> FORSKELLIGE ord efter position.

**Mapningen `aaben -> open` er altsaa uaendret rigtig.** Det er navnet, der
faar `is`-praefikset, fordi feltet er en Boolean.

### De gaengse former, og de daekker de ni ord ovenfor

```
en Boolean            is / has / can     isOpen · hasCard · canEdit
et objekt af en type  <rolle><Type>      openIntent · matchDate · cardState
en maengde            <hvad>Count        registrationCount
en noegle             <hvad>Key          statusKey · absenceReasonKey
et resultat           <hvad>Result       syncResult
```

Formerne er ikke nye: **serveren bruger dem allerede** — `status_key`,
`absence_reason_key`, `halves_count`, `has_break`, `started_on_pitch`. A26 siger
kun, at klienterne bruger samme form, naar det nøgne ord er taget.

### Og raekkefoelgen er en del af reglen

Androids anden maaling, og den er den, der kostede dem mindst:

> Den blev gjort stavnings-unik FOERST, saa de to trin hver kunne ramme ét navn.

```
EET trin   aaben -> isOpen            to aendringer i een, og oversaetteren
                                      peger paa den forkerte halvdel
TO trin    aaben -> aabenUnik         kun stavningen
           aabenUnik -> isOpen        kun navnet
```

**Gaelder kun, naar den gamle stavning kolliderer med noget andet i filen.** Er
der ingen kollision, er eet trin rigtigt.

### Hvad reglen IKKE afgoer

**Hvilken rolle et konkret navn har.** Det staar paa kaldestedet, og det er
derfor der ikke foelger en liste med. Androids fire `aaben` var Booleans; den
femte var ikke, og **det var en maaling paa kaldestedet, ikke et gaet fra
stavningen.**

Og: et bloedt noegleord kan godt bruges som navn i Kotlin. Android proevede det
— *"prøvet ved at omdøbe og OVERSÆTTE"* — frem for at antage det. **Proev det,
foer du udelader et ord, fordi det ligner et noegleord.**

## A27. ELLEVE ORD FRA iOS' BLOKERINGSLISTE — seks paa serverens egne navne

**Afgjort 06-10-2026 kl. 10:30.** Grundlaget er iOS' egen maaling: de sendte de
30 ord, der blokerer flest `Core`-navne, **med forekomsttal** — ikke en liste
over navne. Det er den form, der kan efterproeves paa ét sted pr. ord.

```
INTERVAL  serverens 154 skemakolonner + ruterne + fejlnoeglerne i backend/app/
          hvert kandidatord LAEST i sin fulde kolonnekontekst, ikke taelt
```

### Seks afgjort af SERVEREN — ingen beslutning, kun efterproevning

| dansk | engelsk | iOS-blokeringer | serverens bevis |
|---|---|---|---|
| felt | `field` | 13 | `field_key` · ruterne `/sessions/nullable-fields`, `/live/nullable-fields` |
| tidslinje | `timeline` | 11 | `timeline_json` |
| spilletid | `play_duration` | 9 | `play_duration_min` — **ikke** `playtime`, som var mit foerste gaet |
| foelg | `follow` | 8 | `follow_match_end_notifications` · fejlnoeglerne `error.dbu.group_not_followed`, `error.shared.group_not_followed` |
| ekstra | `extra` | 7 | `extra_time` · `has_extra_time` |
| kilde | `source` | 7 | `source` · `source_filename` |

`spilletid` er den vigtigste af de seks: **jeg ville have skrevet `playtime`**,
og serveren siger `play_duration`. Et gaet havde givet to navne for samme
begreb.

### Fire ENTYDIGE — ét plausibelt engelsk ord, ingen server-kolonne

Serveren har **intet** navn for disse (hverken dansk eller engelsk), maalt:

```
laast · gemt · lokal · logik     nul kolonner, nul ruter, nul fejlnoegler
```

| dansk | engelsk | iOS-blokeringer |
|---|---|---|
| laast | `locked` | 9 |
| lokal | `local` | 8 |
| logik | `logic` | 7 |
| ny | `new` | 18 |

**`gemt` var den risikable, og den er MAALT, ikke antaget.** `gemme` betyder
baade *save* og *hide* paa dansk. Laest paa seks iOS-kaldesteder:

```
AdvarselStore.swift:86   "vi har gemt en kopi"            SAVE
AuthStore.swift:63       "tidspunktet cachen blev gemt"   SAVE
AuthStore.swift:197      guard let gemt = cache.fetch()   SAVE
```

**`gemt -> saved`**, og det foelger `gem -> save` i afsnit B. **Android skal maale
deres egne** — seks kaldesteder i ét traee er ikke to traeer.

### EN konvention: `fjern` er IKKE `slet`

```
slet -> delete    allerede i afsnit B
fjern -> remove   NY
```

**Begrundelsen er maalt, ikke sproglig.** iOS bruger begge ord, og de er ikke
synonymer i koden:

```
slet-former    140 forekomster   slettet · slet · sletning · slettede
fjern-former     74              fjern · fjernes · fjernet · fjerner
```

Engelsk skelner: **`delete` oedelaegger, `remove` tager ud af en samling.**
Serveren har kun `delete` (ruterne `/delete`, `delete-impact`, fejlnoeglen
`error.auth.account_deleted`), fordi den kun gor det foerste. **"Fjern fra en
liste" er et klientbegreb**, og derfor er det en navnekonvention — min at
afgoere, som C3.

### FIRE der IKKE kan afgoeres paa listen — til A5b

| ord | de to betydninger | hvorfor listen ikke kan |
|---|---|---|
| `gammel` | `legacy` (den udfasede form) mod `old`/`previous` (den forrige vaerdi) | serveren har `legacy_type`/`legacy_value` — men det er noget ANDET end "forrige vaerdi" |
| `maal` | `goal` (scoring, afsnit A) mod `target` (en maalvaerdi) | `goals` findes; `target` findes ikke paa serveren |
| `traek` | `pull` · `draw` · `feature` | `/api/features` findes, men det er funktionsflag — ikke det, `traek` daekker i klientkode |
| `ind` | `in` er **noegleord i BAADE Python og Kotlin** | A26 gaelder: navnet skal baere sin rolle |

**`gammel` er faelden her.** `legacy_type` ser ud som beviset, og det er det
ikke: en `gammelVaerdi` i en diff er ikke en *legacy* vaerdi. **Tjek
kaldestedet.**

### OG MIN EGEN MAALING FANGEDE EN FAELDE

Jeg taalte foerst `gammel -> old` som havende **to** server-kolonner. Laest:

```
old matcher   hold · holder_family_id · holder_navn · hold_label
```

**`hold` indeholder `old`.** Det er C2's substring-faelde — i mit eget
instrument, paa den engelske side denne gang.

> Et tal over forekomster er ikke et bevis. Laes navnene.

Det var det, der skilte de seks afgjorte fra de fire, der ikke kan afgoeres.

### Og de boejnings-moenstre, iOS bad mig melde SOM MOENSTRE

Disse er **ikke ord** — de er min splitters artefakter, og de skal ikke paa
listen:

```
matches · totals · minutter · total · stats
  flertals-/boejningsformer af ord, der ALLEREDE er godkendt engelsk
er · af · med · i · at · ikke · nu
  danske smaaord i MAALT DANSK, der rammer som delstrenge
```

**`minut -> minutter` dobler konsonanten**, og `matches` er flertal af det
godkendte `match`. Samme klasse som Androids `filterses`/`freshes`/`valids`.
**Et instrument, der melder dem som kandidater, taeller sin egen output.**

## A28. ET ORD GODKENDT I ÉT TAL ER GODKENDT I BEGGE — og `test` hoerer paa listen

**Afgjort 06-10-2026 kl. 10:40, fordi Android stod stille paa det.** `test`
blokerede **65 testklassenavne** — mere end de 114 oevrige udaekkede ord
tilsammen.

### `test` er GODKENDT ENGELSK, ikke en mapning

```
test · tests        GODKENDT ENGELSK      behold ordet, omdoeb RESTEN
```

Androids begrundelse, og den er rigtig:

> `test` er engelsk, det er suffikset paa hver eneste testklasse i hvert eneste
> sprog, og det er **ikke en oversaettelse** — det hoerer i GODKENDT ENGELSK,
> ikke i mapningen.

**Og det er samme skelnen som A-afsnittets `dbu`/`veo`-rettelse 06-10 kl. 01:30:**
et ord i "godkendt engelsk" lader navnet blive omdoebt rundt om sig
(`SpilletidTest -> PlayTimeTest`), hvor "skal ikke omdoebes" kan laeses som
*spring HELE navnet over.*

### REGLEN, fordi `test` ikke var det eneste

**Maalt systematisk paa de 245 godkendte ord:** flere staar kun i ét
grammatisk tal, og den manglende form blokerer navne, selv om ordet er afgjort.

```
links    STAAR      link     manglede      <- Androids fund
rules    STAAR      rule     manglede
goals    STAAR      goal     manglede
seconds  STAAR      second   manglede
filter   STAAR      filters  manglede      <- den anden retning
```

> **Et ord, der er godkendt engelsk i ental, er godkendt i flertal — og
> omvendt.** Listen beskriver ORD, ikke boejninger.

**Det er en regel og ikke en liste**, fordi en liste over de fem ovenfor ville
efterlade den sjette. Androids egne ord om samme form tidligere samme dag:
*"`filters`/`filter` — praecis samme form."*

### DEN ENE UNDTAGELSE, og den er MAALT

```
tags     STAAR i GODKENDT ENGELSK
tag      STAAR i MAALT DANSK      — "tag" (roof) og bydeformen af "at tage"
```

**`tag` maa IKKE afledes af `tags`.** Reglen gaelder altsaa med ét forbehold:

> ...**medmindre den anden form staar i MAALT DANSK.** Tjek det, foer du afleder.

Og det er ikke et hypotetisk forbehold: `tag` er ét af de ni ord i afsnittet
*"de laeser rigtigt og betyder noget andet"*. `mine` og `slip` er de to andre i
samme klasse, og de har ingen flertalsform paa listen at blive afledt fra — men
det er et tilfaelde, ikke en beskyttelse.

### Og mit eget instrument fandt fem falske, da jeg maalte reglen

Jeg ledte efter ord paa `-s`, hvis ental manglede:

```
alias -> alia      basis -> basi      status -> statu
families -> familie      vocabularies -> vocabularie
```

**Fem af ti traef var ikke ord.** `-s` er ikke en flertalsendelse; den er et
bogstav. Samme klasse som `minut -> minutter` (dobler konsonanten) og Androids
`filterses`/`freshes`/`valids`.

> En boejningsregel, der kun kender ét suffiks, producerer ikke-ord og melder
> dem som huller.

**Derfor staar de fem afledte ord i teksten ovenfor som MAALTE, ikke som
genererede** — hvert enkelt laest, foer det blev tilfoejet.

## A29. `valgfri` er ÉT ord — og sammensatte ord er en fejlklasse for sig

**Afgjort 06-10-2026 kl. 11:10, paa Androids fund.** Deres vaerktoej foreslog:

```
TypeValgfriTest  ->  TypeSelectionFreeTest      NONSENS
```

```
valg -> selection    RIGTIGT
fri  -> free         RIGTIGT
valgfri -> optional  og det er ET ord, ikke to
```

**Beviset stod i testens egen KDoc:** *"`Registration.type` er VALGFRI"*.

| dansk | engelsk |
|---|---|
| valgfri | `optional` |

### Fejlklassen, og den har ikke haft et navn

```
C2 (kendt)   et KORT dansk ord er DELSTRENG af et andet
             hold -> team giver Inteam, beteamt, Placeteamer
A29 (ny)     et SAMMENSAT dansk ord, hvis DELE hver er mappede,
             men hvis HELHED betyder noget andet
             valgfri = optional, ikke "selection-free"
```

**Forskellen er, hvor kontrollen skal sidde.** C2 loeses ved at matche paa hele
ord i en camelCase-opdeling. **A29 kan IKKE loeses paa den maade** — `valgfri`
ER et helt ord i opdelingen, og begge dele er lovlige mapninger. Den loeses kun
ved, at ordet staar paa listen som sig selv.

> Et sammensat ord slaar sammensat-reglen til, netop fordi delene er rigtige.

### Familien, vi allerede havde maalt uden at navngive den

C2's tabel indeholder praecis denne klasse, uden at kalde den noget:

```
indhold     content      ikke "in-team"
forhold     ratio        ikke "for-team"
ophold      stay         ikke "op-team"
beholdt     kept         ikke "be-teamt"
tilstand    state        ikke "til-stand"   (afgjort i C3)
```

**Fem var allerede fundet, og `valgfri` er den sjette.** De blev fundet én ad
gangen af den, der ramte dem — ikke af en liste.

### OG JEG BYGGEDE IKKE EN LISTE, med vilje

Jeg proevede. Mit udtræk af mapnings-ordene fra denne fil gav **937 ord**, hvoraf
hovedparten var PROSA — `afgjorde`, `advarslen`, `aldrig`, `alle`. Femte gang
samme fejl i dette arbejde, og denne gang inde i de indrammede blokke, hvor
reglen *"pars kun det indrammede"* skulle have beskyttet.

**En detektor, jeg ikke kan stole paa, skal ikke producere en liste.** Min egen
regel siger det:

> SEND ordet · maalvaerdien · forekomsttallet · hvor beviset staar
> SEND IKKE en liste over konkrete navne

**Saa dette afsnit er en REGEL plus seks maalte instanser — ikke et facit.**

### Hvad hver platform skal goere i stedet

**Rammer sammensat-reglen et navn, hvor resultatet lyder forkert: STANDSE og
meld ORDET.** Ikke navnet, ikke filen — ordet, dets rigtige engelske
oversaettelse, og hvor beviset staar.

Androids form er forlaegget: de laeste testens KDoc, saa at `valgfri` var ét
begreb, og standsede. **Tre af deres fire fund i dag kom fra at laese
kaldestedets dokumentation**, ikke fra en bedre ordliste.

## A30. FORMATET i denne fils tabeller er ÉN form — og min inkonsistens kostede en maaling

**06-10-2026 kl. 11:20.** iOS' planlaegger laeste **118 raekker**, hvor der var
**132**. De 14, den missede, var mine egne nyeste.

```
de 101 aeldre raekker   | pause  | `break` |        dansk UDEN backtick
mine 11 nye (A26/A27)   | `felt` | `field` |        dansk MED backtick
```

**iOS' parser laeste kun den foerste form**, saa `felt`, `kilde`, `ny`, `laast`,
`lokal`, `logik`, `ekstra`, `tidslinje` var **usynlige** — netop de ord, der var
nye nok til at blokere noget.

**Alle backticks er nu fjernet fra den DANSKE kolonne**, saa filen har ét
format. Maalvaerdien beholder sine, fordi den er en identifikator.

### Hvorfor retningen er den sikre

```
iOS      har rettet deres parser til at laese BEGGE former
Android  laeser den gamle form (de 101)
```

**Saa normalisering til den gamle form virker for begge.** Havde jeg i stedet
bedt dem acceptere backticks, skulle Android have aendret noget for at laese en
fil, de allerede kunne laese.

### Og det er SJETTE instans af samme form i dette arbejde

```
inkonsistent fed antalskolonne   fire af fem nye raekker faldt bort   (A5)
afsnit A's split-raekke          opstilling -> lineup laest som afgjort
prosa paa en datalinje           syv falske par, paa -> et vandt
split('_') tabte underscore      hvert _private navn saa aendret ud
-s som flertalsendelse           alia · basi · statu · familie        (A28)
backtick i EEN kolonne           14 raekker usynlige                  (denne)
```

**Hver gang var det et DOKUMENT, der skulle laeses af baade mennesker og
vaerktoej** — og hver gang kostede uensartetheden en maaling, ikke en fejl.

> Et dokument med to formater har ét format for hver laeser, og ingen af dem
> ser den anden.

### Reglen for den, der tilfoejer en raekke

```
| dansk ord | `engelsk_maal` | eventuelt bevis |
```

**Det danske ord UDEN backtick. Maalvaerdien MED.** Og efter en tilfoejelse:
taeller raekkerne, og saml tallet mod en platforms parser, foer afsnittet
meldes som brugbart.

## A31. TRE ORD, der blokerede tre filnavne — og `selvmaal`s faelde

**Afgjort 06-10-2026 kl. 12:30, fordi Backend standsede paa dem i `#176` del 2
frem for at gaette.** Seks af ni filnavne gik igennem; tre blev blokeret.

| dansk | engelsk | grundlag |
|---|---|---|
| kontrakt | `contract` | **serverens egne navne:** `contract/contract.json` i det delte repo, `sessions_contract.py`, `live_contract` |
| samme | `same` | entydig. Ét plausibelt engelsk ord |
| selvmaal | `own_goal` | entydig som ORD — **men se faelden nedenfor** |

`kontrakt` er den staerkeste af de tre: **serveren har allerede ordet tre
steder**, saa det er ikke en beslutning, kun en efterproevning.

### FAELDEN: `selvmaal` er OGSAA en wire-noegle

```
iOS      Kladde.swift      selvmaal = "selvmaal"    <- CodingKey, DANSK
server   live.py:746/822   selvmaal=True
```

**Ordet staar paa ledningen i dag, og det er dansk.** Saa de to ting skal holdes
adskilt:

```
FILNAVNET      generer-selvmaal-vektorer.py -> generate-own-goal-vectors.py
               FRIT. Et scriptnavn krydser ingen graense
WIRE-NOEGLEN   "selvmaal" i event-JSON
               #156's omraade. Kraever begge klienter aendret OG UDGIVET
```

**At ordet er afgjort betyder altsaa IKKE, at noeglen maa omdoebes.** Det er samme
skelnen som `afbud_aarsag`: ordet er `absence_reason`, men noeglen skiftede
additivt og venter paa `#156` trin 3.

> Et ord kan vaere afgjort, mens ét af dets FOREKOMSTER er frosset. Listen
> afgoer ordet; endepunktet afgoer noeglen.

### Og det er grunden til, at Backends stop var rigtigt

De omdoebte seks filnavne og standsede paa tre. **Havde de gaettet `selvmaal`,
havde de sandsynligvis ramt filnavnet korrekt** — men det er held, ikke metode,
og naeste gang kunne ordet have vaeret et, hvis engelske form var tvetydig
(`kort`, `plads`, `maal`).

```
seks gik igennem   ordene var afgjort
tre standsede      ordene var ikke -> meldt som ORD, ikke som filnavne
```

**Det er formen fra "send ord, aldrig lister", brugt af modtageren.** De sendte
tre ord op; jeg afgjorde dem mod serverens navne; filnavnene er deres.

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

**Fem mere, 05-10 kl. 23:55 — maalt til at have NUL forekomster i serveren**,
altsaa rent klientbegreb, hvor ingen server kan afgoere ordet:

| navne | dansk | engelsk | hvorfor serveren ikke kan afgoere det |
|---|---|---|---|
| **6** | er | `is` | boolean-praefikset. **Staar paa fjendelisten**, saa en mappingpost slaar fjendereglen |
| **3** | logik | `logic` | 5 danske forekomster i serveren, nul engelske |
| **2** | straffe | `penalty` | fodboldtermen, ikke `punish`. Nul forekomster i serveren |
| **1** | trup | `squad` | nul forekomster i serveren |
| **2** | personlige | `personal` | nul forekomster i serveren |

**De stod foerst som en KODEBLOK her, og Android maalte 06-10 kl. 00:15, at de
"ikke findes i filen".** De fandtes — linje 577-578 i `3e70ad6` — men deres
parse laeste dem ikke, og mit eget parse laeste ikke A5's tabel.

**To dataformater i een fil, tre parsere.** Derfor er de nu en tabel: tabellen
er det format, alle tre faktisk laeser. Prosa og kodeblokke er til mennesker.

`er` frigiver **6 navne** (`erEgetHold`, `erKamp`, `erKort`, `erStaevne`) og er
boolean-praefikset. Det staar i den engelske ordbog som et interjektion, og det
er netop derfor en ordbog ikke kan afgoere det — **den siger "engelsk" om et
dansk ord.** `straffe` er fodboldtermen `penalty`, ikke `punish`.

**`svar` → `response`, ikke `answer`:** det er altid et HTTP-svar i denne
kodebase, aldrig et svar på et spørgsmål.

**Endnu et, 06-10 nat, maalt af Backend ved brug af A11's egne fire frigivne
navne:** `seneste` → `latest`. 29 forekomster i `backend/app/*.py`
(`admin.py`/`dbu.py`/`live.py`/`db.py`/`dbu_sync.py`), samtlige betyder
"mest nylige" (`seneste_login`, `seneste kampprogram-kørsel`, `seneste
sendte content_state`, "seneste skriv vinder") — ingen anden betydning
fundet nogen steder. Frigiver bl.a. `_seneste_delt_haendelse` (live.py) →
`_latest_shared_event`.

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

## A32 — ord, der blokerede 90 FILNAVNE, og ét af dem var doemt i PROSA

**Androids maaling 06-10-2026 under `#99`** (filnavne og mappenavne). De fik 36
filnavne igennem og 90 afvist. **De fire hyppigste blokkere:**

```
Modeller   10 navne        Datainput   5
Model       5              Historik    3
```

Plus `Afhaengigheder`, som listen ikke har i NOGEN boejning — saa deres
selvkontrol falder paa den, og **deres tal 72 er et UNDERtal.**

### Og `Datainput` var allerede doemt — i broedteksten

Linje 1137 i denne fil siger:

> *"datainput er data + input, begge engelske og begge GODKENDTE"*

**`data` staar i GODKENDT ENGELSK. `input` gjorde IKKE.** Dommen blev skrevet
som prosa og aldrig foert ind i de data, vaerktoejet laeser.

```
en doem i en saetning    et menneske kan laese den
en doem i listen         et vaerktoej kan laese den
```

Det er samme skelet som alt andet i dette projekt: **noget baerende fandtes kun
ét sted, og det sted var ikke det, nogen laeser programmatisk.** Her kostede det
fem filnavne i fem dage.

### De tre nye mapninger

| dansk | engelsk | grundlag |
|---|---|---|
| historik | `history` | entydig. Ét plausibelt engelsk ord |
| afhaengigheder | `dependencies` | entydig i en kodebase. Ental `afhaengighed` -> `dependency` foelger af A28 |
| modeller | `models` | dansk flertal. Ental `model` er SAMME ord paa begge sprog og staar nu i GODKENDT ENGELSK |

**`model` er ikke en omdoebning.** Ordet er identisk paa dansk og engelsk, saa
`Model` i et navn skal blive staaende — den hoerer i godkendt-listen, ikke her.
`Modeller` er derimod en dansk boejning og skal til `models`.

### Og hvorfor det ikke bare er "tilfoej ordene"

**Listen er AUTORITETEN, og Androids vaerktoej har ret i at afvise et ord, den
ikke kender.** Det er den rigtige opfoersel: et ord, der mangler, skal
tilfoejes af et menneske, der har set at det ikke ogsaa er dansk — ikke gaettes
af et vaerktoej.

> En liste, der mangler et ord, er ikke en fejl i vaerktoejet. Den er en fejl i
> listen, og den SES kun, fordi vaerktoejet naegter at gaette.

Det er modsat af den fejlform, filen ellers advarer om: her meldte instrumentet
**for lidt** daekning, ikke for meget — og et undertal faar nogen til at maale
igen. Et overtal ville have lukket arbejdet for tidligt.

## A33 — de syv, der blokerede Androids runde 2, doemt paa NAVNENE

**Androids maaling 06-10-2026 under `#100`:** 21 filnavne igennem (17 paa A32,
fire efter en rettelse i deres eget vaerktoej). **66 stadig afvist**, hyppigste
blokkere med to forekomster hver.

**Doemt ved at laese de faktiske filnavne, ikke ordene alene** — listens egen
regel for tvetydige ord.

### Tre er ENGELSKE og hoerer i godkendt-listen

```
main    MainActivity.kt · MainTabScreen.kt
        MainActivity er Android-rammens eget navn
play    DbuPlayDuration.kt · LineupPlayDuration.kt · PlayerOverviewDisplay.kt
        og play_duration er allerede serverens ord (A27)
store   AuthStore.kt · AdvarselStore.kt · FormRegelStore.kt · HoldlisteStore.kt
        alle fire er state-store-moensteret
```

### Fire er DANSKE

| dansk | engelsk | grundlag |
|---|---|---|
| ark | `sheet` | `KampArk.kt`, `StoevneArk.kt`. **Serverens eget ord:** `teamsheet`, `/teamsheet-players` |
| sektion | `section` | `KampeSektion.kt`, `SamletSektion.kt`, `TraeningSektion.kt`. Entydig |
| statistik | `stats` | `StatistikFane.kt`, `StatistikModel.kt`. **Serverens eget ord:** `stats.py`, `/api/stats` — ikke `statistics` |

### `store` og `traek` er tvetydige som ORD, og det skal staa

**De hoerer i afsnittet *"kan ikke afgoeres paa en liste"* sammen med `side`,
`min`, `sort`, `point`, `by`, `for` og `post`:**

```
store   dansk  = flertal af "stor"      engelsk = et lager / at gemme
traek   dansk  = pull · draw · feature · trait — FIRE betydninger
```

**Her er de godkendt/mappet, fordi ALLE maalte forekomster i Androids filnavne
er entydige** — fire state-stores og én pull-to-refresh. **Det er en dom over
DISSE navne, ikke over ordene.**

> Et ord, der findes i begge sprog, afgoeres paa navnet. At det er afgjort ét
> sted goer det ikke afgjort alle steder.

**Saa: et vaerktoej maa IKKE behandle `store` som engelsk uden for de maalte
navne.**

### RETTET 06-10 kl. 17:55: `traek` har INGEN enkelt mapning — Android laeste filerne

**Jeg skrev `traek -> pull` og noterede, at `Traek.kt` kun var maalt paa sit
NAVN. Android laeste indholdet, og de to filer kraever to FORSKELLIGE ord:**

```
TraekForAtHente.kt   PullToRefreshBox, "traek ned for at hente"        -> pull
Traek.kt             detectDragGesturesAfterLongPress · TraekTilstand
                     "traek mellem kamptruppen og banen"               -> DRAG
```

**Ét dansk ord, to filer, to engelske ord.** Tvetydigheden er dermed MAALT, ikke
teoretisk — og `traek` hoerer udelukkende i afsnittet *"kan ikke afgoeres paa en
liste"*, ved siden af `side`, `min` og `sort`.

**`traek` har ingen mapning. Den afgoeres pr. FIL, af en, der har laest filen.**

### Og filen modsagde sig selv, hvilket Androids vaerktoej fangede

Efter A33 stod `traek` BAADE som en mapning OG i `KAN_IKKE_AFGOERES`. Deres
`simpelt()` tjekker den sidste FOERST, saa vaerktoejet valgte det forsigtige.

> *"Samme form som `Datainput` i runde 2: dommen fandtes ét sted, mens et andet
> sted i SAMME fil sagde noget andet — og det sted var det, vaerktoejet laeser.
> Dengang prosa mod data; her to datablokke mod hinanden."*

**Og denne gang var det forsigtige svar det RIGTIGE.** Uden `KAN_IKKE_AFGOERES`
var `Traek.kt` blevet til `Pull.kt` — en fil om drag-and-drop.

> En modsigelse i listen loeses ikke ved at vaelge den nyeste post. Den loeses
> ved at fjerne den forkerte — og her var den forkerte MIN.

### Og fire af Androids afviste var ikke listens skyld

`Datainput` blev stadig afvist, selvom `data` og `input` begge blev godkendt i
A32: `ord_i("Datainput")` giver ÉT token, og deres `del_sammensat` slog op i den
DANSKE ordbog — **ordet faldt mellem to regler.**

**Deres rettelse er formen, der er vaerd at kende:** reglen blev en
ORDBOGSregel i stedet for en formregel, og dens selvkontrol er den OMVENDTE —
alle 155 danske ord koert igennem, **0 daekket.** Kunne ét dansk ord saettes
sammen af godkendte engelske stumper, var reglen usikker.

