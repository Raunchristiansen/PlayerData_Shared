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
harGuleKort      har + gule + kort     'gule' udaekket  ->  SPRINGES OVER
spillerNavn      spiller + navn        'navn' er ENGELSK ->  omdoebes helt
periodeMarkeringMs  periode + markering + ms   udaekket ->  SPRINGES OVER
```

**Det er en egenskab ved vaerktoejet, ikke ved listen.** Derfor kan listen vokse
bagefter uden at noget skal rettes om, og springe-listen er maalbar fremdrift.

**Hvorfor det er bedre end at skrive ordlisten faerdig foerst:** halen er lang.
Af de 349 udaekkede ord rammer **146 kun ÉT navn**, og top 100 ord daekker kun
61 % af hybriderne. Et ord, der optraeder én gang, er ikke en ordforraads-
beslutning — det er navngivningen af den ene ting, og den hoerer i haanden.

### Boejningsreglen — 19 ord, 114 navne

Et ord er daekket, hvis dets **stamme** staar i listen og endelsen er en dansk
boejning. Maalt, de der faktisk forekommer:

```
kampe      = kamp + e          haendelser = haendelse + er
banen      = bane + en         farver     = farve + er
traenings  = traening + s      puljer     = pulje + er
spillere   = spiller + e       vaelger    = vaelg + er
halvlege   = halvleg + e       noegler    = noegle + er
perioder   = periode + er      traeninger = traening + er
henter     = hent + er         stoevner   = stoevne + er
hentet     = hent + et
```

Engelsk boejes derefter efter engelsk regel: `kampe -> matches`, inte
`matche`. **Tilfoej ikke boejninger til selve listen** — stammen plus reglen.

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

| delstreng | navne | betyder | eksempler |
|---|---|---|---|
| `indhold` | 13 | content | `Indhold`, `OpstillingIndhold`, `FoelgKampIndhold` |
| `holder` | 6 | holder (låsens) | `holderNavn`, `_forny_laas_hvis_holder`, `_laas_holder` |
| `placeholder` | 2 | **engelsk ord** | `PlaceholderFane`, `placeholder` |
| `beholdt` | 1 | kept | `beholdt` |
| `ophold` | 1 | stay/pause | `UDKAST_OPHOLD_MS` |
| `forhold` | 1 | ratio | `BANE_FORHOLD` (bane-forhold, altså aspect) |

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
