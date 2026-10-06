<!-- ===================================================================
     DELT BLOK — DEN ENE kilde, delt af alle fire PlayerData-repos
     (Issue #15/#139, 2026-10-04: flyttet hertil fra backend-stage's
     egen claude-md/DELT-BLOK.md, netop for at fjerne duplikeringen
     Issue #15 selv varslede).

     Hvert repo trækker denne fil ind via sit eksisterende
     `shared-repo`-submodule (samme submodule testvektorerne allerede
     bruger) og peger sin egen CLAUDE.md's "fælles blok"-afsnit hertil
     — IKKE en indlejret kopi. Rettes en regel her, er den rettet for
     alle fire, uden en synkronisering nogen skal udføre i hånden.

     Et repo, der endnu ikke peger hertil (fordi dets session ikke har
     opdateret sin egen CLAUDE.md endnu), læser i mellemtiden en
     FORÆLDET indlejret kopi — se #139 for målingen, der fandt dette.
     Sidst ændret: 2026-10-04
     =================================================================== -->

<!-- ===================================================================
     DELT BLOK — identisk i alle fire PlayerData-repos.
     Rettes en regel her, skal den rettes i ALLE fire.
     Flyttes til PlayerData_Shared, når det repo findes (Issue #15),
     så denne duplikering forsvinder.
     Sidst ændret: 2026-09-30
     =================================================================== -->

## Fælles regler på tværs af PlayerData

### Issue-konventionen

Hvert Issue har ét af to mærker:

- **"klar til at køre"** — fuldt specificeret. Du kan tage den, implementere, teste, committe, pushe og lukke den uden at spørge undervejs. Også om natten.
- **"afventer afklaring"** — kræver Mortens dømmekraft. **Gå ikke i gang.** Gælder design- og UX-valg, noget der ændrer en tidligere aftale, og alt uklart.

Er et Issue umærket, behandl det som "afventer afklaring".

Udgivelse til rigtige brugere (TestFlight, Play, produktion) kræver altid Mortens eksplicitte go — også når Issuet er "klar til at køre".

### Rodårsag, ikke symptom

Melder du en fejl videre, eller lukker du et Issue en anden platform også skal rette: **skriv mekanismen ned.** Filnavn, linjenummer, den forkerte antagelse. Ikke "rettet" og ikke "det ser forkert ud".

En ren symptombeskrivelse tvinger modtageren til at gætte. Det er forskellen på, at den anden platform finder deres version på ti minutter eller bruger en aften på at genopdage den.

### En måling, der kun findes i to sessioners logge, er ikke dokumenteret

Android målte `#76` grundigt, sendte det til koordinatoren i en besked, og
koordinatoren relayede det videre til Morten. **Kortet havde nul kommentarer**,
da det blev opdaget timer senere.

Androids egen formulering, efter at koordinatoren indrømmede fejlen:

> Den gælder begge veje: jeg sendte den til dig i en besked og **betragtede den
> som afleveret.** En måling, der kun findes i to sessioners logge, er ikke
> dokumenteret — den er **husket**, og hukommelse er præcis det, der dør med en
> session.

**Samme dag skete det tre gange hos koordinatoren:** `#144` og `#157` var kørt
og verificeret, men kortene stod åbne, og `#76`s måling fandtes kun i en
chatbesked.

**Formen der holder:** de tal, der skal overleve dig, skrives i
**commit-beskeden** — det eneste sted en platform-session selv kan garantere — OG
koordinatoren lægger dem på kortet. To steder, to ejere, og ingen af dem er en
chatlog.

Det er samme lektie som `live_contract.py`s egen oprindelse: iOS måtte spørge
koordinatoren om en JSON-form i en besked, **og svaret forsvandt ved nedbruddet
samme formiddag.**

### Tværgående arbejde: forælder og under-issues

Et GitHub-Issue bor i ét repo — de andre platformes sessioner ser det ikke. Derfor:

- Ét **forældre-Issue** beskriver fundet og rodårsagen
- Ét **under-Issue per platform**, i platformens eget repo
- Forælderen lukker først, når alle børn er lukket med commit-reference

For delt **logik** er rækkefølgen bevidst seriel: den platform, der fandt fejlen, retter først, fordi rettelsen *producerer* den information, de andre skal have. De øvrige under-issues oprettes straks med "afventer afklaring" og flippes til "klar til at køre", når mekanismen er meldt.

### Triagemodellen for en feedback-batch (Issue #9, 2026-10-01)

Når en samlet batch feedback (fx fra Morten efter en build) skal fordeles, er modellen ovenfor ikke nok alene — den forudsætter at man allerede ved, om et punkt er delt. Det første skridt er derfor at AFGØRE det, punkt for punkt:

> **Er fejlen i implementeringen, eller i reglen/designet?**

| Form | Struktur | Lukker når |
|---|---|---|
| Platform-fejl — reglen er fin | Ét Issue i platformens eget repo | Platformen committer |
| Delt regel — logikken er forkert | Forælder + sub per platform + **sub til testvektor** | Alle subs lukket, vektoren i git |
| Delt design — designet er forkert | Forælder + sub per platform | Alle subs lukket med screenshot |

**Ét Issue per punkt, ikke ét for hele batchen** — delene rettes i forskellig hastighed af forskellige sessioner, og et batch-Issue kan aldrig lukke.

For "delt regel" gælder den serielle rækkefølge ovenfor, med ét ekstra, ufravigeligt krav: **den platform, der retter først, skriver MEKANISMEN ned, ikke "rettet"** — funktionsnavn, linje, den forkerte antagelse (samme krav som "Rodårsag, ikke symptom", anvendt specifikt her fordi hele modellen falder, hvis det glemmes).

For kort-/regel-feedback specifikt: spørg altid *er reglen rigtig*, før du spørger *hvilken platform* — server-autoritet beskytter mod uenighed mellem platforme, ikke mod at alle er enige om noget forkert (se "Logik: hvor må en regel bo" ovenfor).

**Forudsætninger for at modellen virker, ikke kun er aftalt:** CI grøn og gating merget på alle platforme, OG en testvektor-mekanisme der rent faktisk binder alle sprog i brug (ikke kun to af dem) — uden den er "sub til testvektor" en manuel aftale, ikke en gate.

### Sprogregel

**Inde i en kildefil: engelsk** — både navne og kommentarer. Blandingen er værre end begge rene sprog.

**Uden for kildefiler: dansk** — denne fil, docs, commit-beskeder, Issue-titler og -tekst, app-tekster.

Gælder også: repo-navne, branch-navne, mappenavne, script-filnavne, env-variabler, doc-filnavne.

**Undtagelser:**
- `dbu_*`-tabellerne er et fremmed skema, spejlet fra DBU. De fryses som de er, og dermed forbliver `pulje`, `raekke` og `kampnr` danske.
- Eksisterende tabeller og kolonner omdøbes ikke løbende. Det er en API-kontraktændring på tværs af fire platforme og kræver ét planlagt snit.

**Omfang (besluttet 2026-09-30): der rettes overalt**, ikke kun i nyt kode. Men i tre tempi:

- **Interne navne** (funktioner, variabler, klasser) — ingen koordinering nødvendig. Omdøb løbende, når du alligevel er inde i filen. Aldrig som selvstændig opgave. **Samme tempo for kodekommentarer:** nye kommentarer skrives engelsk FRA NU; eksisterende danske kommentarer bevares, IKKE retroaktiv oversættelse — kun skiftet, når filen alligevel røres, aldrig som sin egen opgave.

  **Nye filer, funktioner, klasser, variabler og tests er ENGELSKE FRA I DAG, uden undtagelse — Morten, Issue #124, 2026-10-04: "Regel nu, omdøbning senere."** Eksisterende danske navne bevares og omdøbes KUN når nogen alligevel er inde i filen — aldrig som en selvstændig opgave, samme tempo som resten af dette punkt. Forskellen fra den generelle regel ovenfor: FØR #124 var det tilladt at SKRIVE et nyt dansk navn (ingen regel forbød det); FRA i dag er det ikke — et nyt navn skal være engelsk, uanset om filen omkring det er dansk eller allerede delvist omdøbt.

  **Baggrunden, værd at kende:** Morten så `faelles.py` i en statusbesked og spurgte, om filnavne også skulle omdøbes. Målt samme dag: af 40 Python-filer i `backend/app/` havde 6 danske navne (`aldersregler.py`, `beregning.py`, `faelles.py`, `kort_regler.py`, `maal_side.py`, `spiller_tjek.py`) — og `maal_side.py` var oprettet SAMME MORGEN, under Issue #119. Der blev altså stadig skrevet NYE danske filnavne, mens gamle blev ryddet op — den blødning, #124 stopper. Ikke en kritik af #119: reglen fandtes ikke endnu, og kunne ikke følges.

  **Et tal, der skal bære et forbehold:** #124's kort nævner "~3400 navne" som omfanget af en fremtidig, samlet omdøbning af EKSISTERENDE navne. Hverken Backend- eller Koordinator-sessionen har efterprøvet det tal — det stammer fra en tidligere måling, ikke en der er gentaget. Skal en samlet omdøbning nogensinde besluttes, skal tallet måles forfra, ikke genbruges — samme lektie som de tidligere fund i denne fil, hvor et videregivet tal talte en anden enhed end modtageren troede.
- **API-kontrakten** (JSON-nøgler og de kolonner, der flyder ud i dem) — ÉT HÅRDT SKIFTE, ikke en overgang med begge navnesæt (Morten, 2026-10-04, revideret fra den oprindelige formulering herunder). **Begrundelsen, så den næste forstår HVORFOR den ændrede sig:** reglen blev skrevet, da vi troede platformene skulle kunne lande forskudt. Det gælder ikke længere — der er fire testere, alle får samme build, og dagens arbejde samles i ÉN udsendelse. En overgangsperiode ville koste to navnesæt at vedligeholde for ingenting. Et hårdt skifte er derfor rigtigt — men det betyder, at serveren og BEGGE klienter skal landes i samme build. Serveren må være ÉN ombæring foran, aldrig to: ellers skal klienterne indhente flere på én gang, og ingen kan sige hvilken der brækkede noget, hvis noget brækker. **Reglen vender tilbage den dag, appen er i App Store og en installeret app ikke længere kan tvinges til at opdatere** — skriv ikke koden, så den antager det modsatte for evigt, samme forbehold som #114's. **Reglen blev brudt i praksis 2026-10-04**, uden at nogen fangede det, før Android spurgte (ombæring B/C's hårde skifte på `navn`/`adgangskode`, givet klienterne allerede havde læst de gamle navne) — en regel, der kun findes skrevet, og som alle gik forbi, er værd at markere som netop det.
- **`dbu_*`-tabellerne** — frosne. `pulje`, `raekke` og `kampnr` forbliver danske.

### Ordliste

```
kamp → match              spiller → player          hold → team
klub → club               opstilling → lineup       holdkort → team_sheet
halvleg → half            maal → goal               traening → training
stævne → tournament       fælles → shared           troeje → shirt
stroemper → socks         dragt → kit               farver → colors
hjemme/ude → home/away    laas → lock               valg → selection
knap → button             tilstand → state          taendt → enabled
spillested → venue        rangering → ranking       tidslinje → timeline
kamphaendelser → match_events
funktions_knapper → feature_flags
hent → fetch    gem → save     slet → delete    opret → create
beregn → compute  tjek → check  flet → merge    forny → renew
```

To ord kræver omhu: **`lineup`** (opstilling — hvem spiller hvor) og **`team_sheet`** (holdkort — hvem er udtaget) er IKKE det samme. Koden skelner dem, og det skal navnene også.

### Logik: hvor må en regel bo

Morten besluttede 2026-09-26: **serveren afgør alle afledte tal**, så de er ens på tværs af platforme.

Beslutningsregel for hvert enkelt tilfælde:

> **Skal det virke offline, på en sidelinje, uden signal?**

- **Nej** → serveren regner, klienten viser. Implementér det ikke selv.
- **Ja** → klienten regner, men resultatet skal låses mod et fælles facit (testvektorer), så de fire implementeringer ikke kan drive fra hinanden i stilhed.

**Vigtig begrænsning:** server-autoritet beskytter mod *uenighed* mellem platforme — ikke mod at alle er enige om noget forkert. `beregning.py:273` genskaber bevidst en klient-antagelse om kort. Er antagelsen forkert, er serveren forkert på samme måde. Spørg derfor altid først: *er reglen rigtig?*

Baggrund: en spiller fik vist 71 minutter i stedet for 37, fordi klientens sortering manglede tie-break for hændelser i samme minut.

### Dine commits i PlayerData_Shared staar som MORTENS — saet `user.name`

**Maalt 03:35 paa de tre seneste commits i det delte repo:**

```
18c2d44  forfatter: Morten Christiansen   INGEN Co-Authored-By   (Backend)
af80c70  forfatter: Morten Christiansen   INGEN Co-Authored-By   (Backend)
38bc246  forfatter: Koordinator           Co-Authored-By: Claude (koordinatoren)
```

`gh` og `git` bruger repoets opsaetning, som er Mortens konto. **En commit fra en
session er derfor ikke til at skelne fra en, han selv har lavet** — og
`git log` er den varige optegnelse over, hvem der afgjorde et delt ord.

**Det gaelder ogsaa Issue-kommentarer:** `gh api user` svarer
`Raunchristiansen`, saa alt, en session skriver paa et kort, staar under hans
navn. Koordinatoren opdagede det 06-10 kl. 02:30, fordi Backend laeste et kort
direkte og bemaerkede, at en go-kommentar stod *"under repo-ejerens konto"* —
altsaa som en primaerkilde. Den var et referat.

```
Co-Authored-By: Claude <model> <noreply@anthropic.com>    i commit-beskeden
```

**RETTET 03:38: spoerg IKKE om `git config user.name`.** Koordinatoren bad om det,
og Backend afviste — en staaende regel i deres egen opsaetning siger *"NEVER
update the git config"*, **og den regel gaar forud for et forslag fra en peer,
uanset hvor velbegrundet det er.**

Det var rigtigt. Og det er fjerde gang i doegnet, en session afviser noget, en
peer bad om:

```
iOS      to gange: submodul-loeftet baerer DELT-BLOK, altsaa deres instruktioner
Backend  nat-autonomien kan en peer ikke give    "I kan ikke give mig den tilladelse"
Backend  git config er deres opsaetning, ikke en peers at aendre
```

**Alle fire var korrekte.** En koordinator kan bede om noget; den kan ikke
omdefinere, hvad en session har faaet besked paa.

**Traileren opnaar det samme** og er inden for enhver sessions raekkevidde:
`git log` viser, at commit'en kom fra en session og hvilken model. Backend bruger
den konsekvent i `backend-stage` (maalt: `849a475`, `5f2f1b4`) og glemte den paa
to shared-repo-commits — **et tilsyn, ikke en beslutning.** Historik paa et delt,
pushet repo skrives ikke om for at rette det.

**Hvorfor det er mere end pynt:** et hul, man kan SE, standser en laeser. Et
referat, der ser ud som en primaerkilde, standser ingen. Og i dette projekt er
det netop en sessions ret at standse en besked, der ikke er Mortens.

### En regel, intet kalder, kan vaere UENIG med den, der kaldes

**To maalte forekomster hos Android, 02-10 og 06-10, og formen er den samme
begge gange.** Oevelsen er deres: offentlige `core/`-funktioner, som intet i
`main/` kalder.

```
02-10   venterPaaBekraeftelse   sagde "venter stadig" ved konto_komplet=true
                                og email_bekraeftet=false — serverens undtagelse
                                for sine egne testkonti. naesteTrin siger FAERDIG
06-10   restForSpiller          kortets START = endMs minus udvisningens laengde
                                den levende vej forankrer i udvisningStartMs,
                                ellers haendelsens time
```

**Begge var kun kaldt fra en test. Og begge var UENIGE med den funktion, skaermen
faktisk bruger.**

> Det er ikke doed kode, men **en faelde, der venter paa at blive koblet.**

Og det er den rigtige maade at laese det: en funktion, intet kalder, koster
ingenting i dag. **En funktion, intet kalder OG som svarer noget andet, er et
forkert svar, der ligger klar** — og den naeste, der skal bruge reglen, finder
den og kobler den.

### To ting at kontrollere, FOER oevelsen bruges

**1. Instrumentet.** Androids foerste maaling 02-10 svarede *"261 funktioner
kaldes slet ikke"*, heriblandt `synlighed` og `valider`:

```
(?<![\w.])navn\s*\(     <- lookbehind'et udelukker ALT med et punktum foran
```

Altsaa hvert `Objekt.metode(...)`-kald. **Selvkontrollen skal maale navne, man
VED er koblede, og se dem > 0** — ti navne, i deres tilfaelde.

**2. Oevelsen finder BLADE, ikke kaeder.** `restAfUdvisning` var kun kaldt fra
`restForSpiller`, altsaa **transitivt doed** — og usynlig for oevelsen, fordi
den kun ser eet led.

```
A kaldes af intet          -> FUNDET
B kaldes kun af A          -> IKKE fundet, selv om A er doed
```

Det er en graense, ikke en fejl. **Men den skal staa**, saa et tomt svar ikke
laeses som "ingen ukoblet kode" — den rigtige saetning er *"ingen ukoblede
BLADE"*.

### Og den gaelder alle tre platforme

Androids `main/` er iOS' `Features/` og Backends ruter. **Oevelsen koster et
script og har fundet en faelde to gange ud af to koerninger.**

### Konfiguration og beregning er ikke det samme

Reglen ovenfor spørger: *skal det virke offline?* Men den skelner ikke mellem to ting, der opfører sig helt forskelligt.

| | Må ligge centralt | Fordi |
|---|---|---|
| **Konfiguration** — lister, egenskaber, regler der sjældent ændrer sig | **ja, altid** | den kan hentes én gang og caches. Offline er ingen forhindring |
| **Beregning** — tal afledt af noget, brugeren lige har tastet | kun hvis online er et krav | den kan ikke caches; den skal køre nu |

Halvlegs**reglen** — "U11-U13 spiller 2 × 30" — er konfiguration. Den kan bo på serveren og caches i appen, selvom den bruges på en bane uden dækning.

Halvlegs**uret** er beregning. Det skal tikke, mens kampen kører, og bliver i klienten.

Skelnen afgjorde to spørgsmål på én aften 2026-09-30: om aktivitetstypernes ordforråd måtte flytte centralt (ja), og om spilletidsprocenten skulle regnes af serveren (ja — den har begge led og dividerer bare ikke).

**Når noget caches, følger tre krav med:**

1. **En indbygget reserve.** En frisk installation uden netværk har ingen cache. Reserven skal **genereres fra kilden ved build-tid**, ikke vedligeholdes i hånden — ellers er den en konkurrerende sandhed frem for et øjebliksbillede
2. **En version.** Cachen skal kunne fortælle sin alder. Et let versionskald ved opstart er nok
3. **Synlighed.** En cache, der ikke kan fortælle sin alder, er en cache, man ikke kan opdage er forkert

### Et gentaget fund er et signal om, at man leder på det forkerte lag

Koordinatoren, 04-10-2026. `check-parent-cards.sh` meldte **tre gange på én dag**,
at `Backend#134` var lukket med åbne børn. Tre gange blev kortet genåbnet. Tre
gange lukkede det igen inden for en time.

Først den tredje gang blev issuets **tidslinje** læst:

```
10:38  ClosedEvent     en person
15:47  ClosedEvent     via ProjectV2      <- tavlens automatik
17:25  ClosedEvent     via ProjectV2      <- igen
```

**Projektets indbyggede regel lukker et element markeret `Done`.** Kortets
`Status`-felt stod på `Done`, så hver genåbning blev rullet tilbage af tavlen —
ikke af nogen.

> Et gentaget fund er et signal om, at man leder på det forkerte lag.

**Vagten var rigtig hver gang.** Den målte det, den skulle. Fejlen var, at
rettelsen blev gentaget i stedet for at blive undersøgt: tre identiske svar på tre
identiske handlinger er ikke tre fund, det er **ét fund om handlingen.**

Rigtig rækkefølge, da mekanismen var kendt: sæt `Status` til noget andet end
`Done` **først**, derefter genåbn. Og det samme gælder enhver automatik, der
observerer et felt: **en handling, systemet fortryder, er ikke en handling.**

### Et udsagn splittes på kilde. En måling gør ikke

`#53`s kampvalgs-matrix havde to spørgsmål, der lignede hinanden, men fik modsatte
svar af Morten samme dag:

- **`team` (Hold)** — regnearket sagde "Låst" for alle kamptyper, men viste sig kun at
  gælde DBU-kampe. En selvoprettet kamp har intet holdkort at låse til.
- **`gps_data` (GPS)** — reglen ("Ej udtaget ⟹ intet GPS") gælder derimod UÆNDRET,
  uanset om kampen er DBU-koblet eller familiens egen.

Forskellen er ikke tilfældig, og den generaliserer:

> **Et felt, der er et UDSAGN, splittes på kilde — dens troværdighed afhænger af,
> hvem der sagde det. Et felt, der er en MÅLING AF EN BEGIVENHED, gør ikke — fandt
> begivenheden ikke sted, findes målingen ikke, uanset hvor kampen kom fra.**

`team` er et udsagn ("dette hold spillede") — en DBU-kamp har et autoritativt
holdkort at sige det ud fra, en selvoprettet kamp har kun familiens egen indtastning,
og de to fortjener forskellig tillid. `gps_data` er en måling — en vest optager et
barn, der spiller, eller den gør ikke, og spørgsmålet "hvor kom kampen fra" ændrer
intet ved om barnet faktisk spillede.

Spørg derfor ALDRIG automatisk "hvad er kilden" for et nyt felt i en lignende
matrix. Spørg først: **beskriver feltet et udsagn, eller en begivenhed?** Kun
udsagn har brug for et kilde-skel.

### En standardværdi kan være forsvarlig, da den skrives, og farlig bagefter

Androids eget fund 03-10-2026, og det er en anden fejl end afsnittet nedenfor:
dér er standardværdien forkert fra fødslen. Her var den **rigtig**, og blev
farlig af en ændring et andet sted.

De gav en parameter en tom standardværdi og skrev begrundelsen ved siden af:
parameteren drev kun et MÆRKE, og et manglende mærke gør en skærm mindre
præcis, ikke forkert. **Det var sandt, da det blev skrevet.**

To trin senere kom en spærring til at hænge på samme parameter. Og dér er
"ingen er udvist" et plausibelt og **forkert** svar: to tests holdt op med at
spærre i samme øjeblik, uden at fejle på noget andet.

> *"Begrundelsen holdt; forudsætningen gjorde ikke."*

**Det farlige er netop begrundelsen.** En standardværdi uden forklaring bliver
mistænkt af den næste, der ser den. En med en god forklaring ved siden af bliver
læst, godkendt og forladt — forklaringen besvarer et spørgsmål, ingen stiller
igen, fordi den ser ud som om den allerede er blevet stillet.

Det er samme familie som "et tal, der lyder plausibelt, bliver ikke efterprøvet",
men forskudt i tid: antagelsen var sand ved skrivningen, og **ingen går tilbage
og spørger, om den stadig er det**, når en ny kaldesti kommer til.

**Hvad man kan gøre:** når du tilføjer en kaldesti til en funktion med en
standardværdi, så læs begrundelsen for standardværdien og spørg, om den gælder
for DIN kaldesti. Gør den ikke, er det ikke standardværdien, der er forkert — det
er din brug af den, og den skal sende værdien eksplicit.

Android så det kun, fordi testene blev røde af en anden grund først.

### En standardværdi, der er et plausibelt svar, skjuler et manglende felt

Klienterne ignorerer ukendte JSON-nøgler, og det skal de — ellers vælter et nyt
serverfelt en gammel app. Men sammen med en standardværdi, der selv er et gyldigt
svar, gør det en omdøbning USYNLIG: det nye navn ignoreres, det gamle mangler, og
standardværdien fylder hullet. `0`, `""`, `false` og en tom liste ser alle ud som
rigtige data.

En klients testfixtur er desuden altid ældre end serverens nyeste felt. Et nyt felt
er derfor utestet som standard, medmindre nogen tester netop NAVNET — og navnet skal
læses i serverens kode, ikke gættes.

**For et AFLEDT tal skal fravær kunne skelnes.** Gør det nullable, aldrig en
standardværdi, der kunne være svaret. Det er samme regel som "ingen standardværdi på
et afledt tal", anvendt på afkodningen: *"serveren sendte det ikke"* må ikke kunne se
ud som *"serveren sendte nul"*.

For rå data er en standardværdi i orden — men så skal feltnavnet have en test.

Bevis at testen kan fejle: skriv feltnavnet forkert og se den blive rød. Med
`@SerialName("spilletidspct")` blev to tests røde; uden den kontrol vidste vi ikke,
om navnet nogensinde blev læst.

### Et felt der bliver nullable er et kontraktbrud, ikke en rettelse

Følger direkte af afsnittet ovenfor ("En standardværdi, der er et plausibelt svar,
skjuler et manglende felt") — de modsiger ikke hinanden, de dækker to forskellige
tidspunkter. Afsnittet ovenfor siger, hvad den RIGTIGE tilstand er (nullable, aldrig
en standardværdi, der kunne være svaret). Dette afsnit siger, at ÆNDRINGEN fra den
forkerte til den rigtige tilstand på et EKSISTERENDE felt skal behandles som en
breaking change, ikke en stille rettelse.

Issue #48 rettede ÉT felt fra et plausibelt `0` til `None` og brækkede begge
klienters statistikfane — et ikke-nullable felt kaster på et eksplicit `null`,
ikke kun feltet, HELE svaret. Lektien blev skrevet ned samme dag: "ændringen var
rigtig, rækkefølgen var ikke."

To timer senere gjorde Issue #55 præcis det samme igen, med FEM felter i stedet
for ét, deployet til stage FØR nogen klientkort fandtes. Målt bagefter: 7 af 9
familier har slet ingen GPS-data, så ændringen rammer stort set alle, bredere
end #48's fire. En skrevet lektie holdt ikke engang to timer — samme fejlklasse
som afsnit "Få testen til at fejle, før du stoler på at den består" beskriver
for regler generelt: det er ikke nok at vide det, proceduren skal tvinge det.

**Reglen, som en procedure, ikke en erindring:**

> Et felt der går fra "altid et tal" til "kan være null" er et **kontraktbrud**,
> ikke en rettelse. Klientkortene skal være oprettet OG landet — eller den
> gamle adfærd bevidst holdt til de er — før serveren deployer ændringen. Den
> omvendte rækkefølge brækker en skærm hver gang, uanset hvor rigtig selve
> rettelsen er.

Gælder uanset ÅRSAGEN til at feltet bliver nullable — en ren #48-stil rettelse
af et enkelt felt, eller en #55-stil gennemgang der finder flere på én gang. Et
felt der allerede ER nullable, og som ingen klient endnu har set en reel
`null`-værdi for (fordi dataene hidtil altid har været udfyldt), er samme
fælde i venteposition — opdag den ved at spørge "er dette felt nullable, og har
klienten kode for det", ikke ved at vente på de data der udløser den.

### Nøgler, kolonner og filnavne er engelske. Dansk hører i `display_da`, aldrig i nøglen

En dansk streng brugt som nøgle gør visningstekst til en datamigrering.

`activity_types` (Issue #37) er forlægget: `key`, `category`, `source` er engelske
identifikatorer, og `display_da` bærer det danske visningsnavn ved siden af — skift
visningsteksten, og intet, der sammenligner på `key`, mærker det.

`aldersregler` (Issue #44) var modeksemplet, fra samme dag: tabellen og dens kolonner
(`aargang`, `perioder`, `har_pause`, `standard_format` …) blev navngivet på dansk,
timer før Mortens beslutning om engelske nøgler faldt. Ingen klient havde endnu læst
feltnavnene — det billigste øjeblik omdøbningen nogensinde ville få — og Issue #54
brugte præcis det vindue til at rette det, før iOS og Android begyndte at kode mod de
gamle navne. Tabellerne hedder nu `age_rules`/`format_rules`/`group_formats`, med
engelske kolonnenavne gennemgående.

**Omfanget er navnet, ikke teksten.** Dette gælder nøgler, kolonner og filnavne —
IKKE kildefilers kommentarer (dem gælder Sprogregel-afsnittet ovenfor om, uændret), og
IKKE en værdi, der er ren DATA fordi en klient sammenligner på den (fx
`sessions.status`s danske visningsstrenge `'Udtaget'`/`'Deltog'`/`'Afbud'` — det er et
datamigrerings-problem, ikke et navngivnings-problem, og det er bevidst ladt urørt her,
se Issue #42).

**Tre niveauer, ikke ét snit.** Issue #54 skelnede eksplicit mellem dem, og skellet er
værd at genbruge, når et lignende fund dukker op:
- Et NYT navn, ingen klient har læst endnu — ret det med det samme, det er gratis.
- Et navn, der ER data en klient sammenligner på — kræver en planlagt datamigrering OG
  et breaking change i flere kodebaser, skal IKKE klares i samme ombæring.
- Et navn i en stor, frossen eller fremmed tabel (`dbu_*`, de ældre `kamp_*`-tabeller)
  — rent kosmetisk gevinst for prisen af at røre data i flere miljøer, bevidst
  udskudt og skrevet ned som en accepteret, kendt inkonsistens, ikke en fejl der
  mangler at blive rettet.

**En regel, der kun er skrevet, holdt fem gange ikke i dette projekt.** Derfor har
migrationstesten (`backend/tests/test_schema_navngivning.py`) en billig, mekanisk
håndhævelse: den kører `init_db()` mod en frisk database og afviser æ/ø/å i ethvert
tabel- eller kolonnenavn. Den fanger ikke "er dette egentlig engelsk" — kun den ene
ting, der altid er forkert: en bogstavelig dansk diakrit i en identifikator. Sabotagetestet
2026-10-01 (en tilføjet `sabotage_tabel(værdi TEXT)` gjorde testen rød, med en
begrundelse der navngiver fundet) — ikke kun antaget til at virke.

**Alt, der er en TING, har en nøgle. Referencer peger på nøglen — ALDRIG på
teksten.** Afsnittet ovenfor siger, hvilket SPROG en nøgle har. Dette siger, at der
SKAL være én — Morten, 2026-10-04, efter #131: *"Men der skal vel nøgle på alt
eller hvad? Det er vel det mest rigtige uantastet om det sammenlignes eller ej?"*

Hver række har allerede en identitet. Fejlen er ikke, at en ting mangler en nøgle —
den er, at noget peger på ORDET i stedet for på tingen. Begrundelsen er projektets
egen historie: man kan ikke vide på forhånd, hvad der bliver sammenlignet.
`sessions.type` var fritekst, indtil nogen filtrerede på den (Issue #37), og så var
det for sent — samme mønster som `activity_types`/`match_event_types`/`match_sides`
blev bygget for at lukke, men kun DÉR hvor nogen allerede har bygget nøglen og
koblet dataene til den. Et ordforråd, der findes men ikke bruges af de faktiske
dokumenter, lukker ingenting — `kamp_kamphaendelser`s hændelser har i dag
`{"type":"mål","scorer_type":"eget_hold"}` uden en `type_key`/`scorer_type_key` ved
siden af, selvom `match_event_types`/`match_sides` (#135) findes — ordforrådet blev
bygget, men ingen regel sagde, at dokumenterne skulle bruge det.

**Gælder nyt OG eksisterende, som Sprogreglens egen omfangsregel ovenfor** — en ny
TING uden nøgle er en fejl med det samme, en eksisterende uden er en kendt mangel,
der rettes når der alligevel er grund til at røre den (samme tempo som resten af
denne fils regler, ikke en ny, selvstændig oprydningsopgave).

### Samme serializer, modsat opførsel — afhængigt af feltets erklæring

Android målte 05-10-2026, at et `null` fra serveren blev `false` i 48 af 86
registreringer. **Den oplagte mistænkte var `FleksibelBoolean`**, som de selv
havde målt i `#73` til at give `false` for et null.

Den var uskyldig:

> Jeg målte den i `#73` på et **ikke-nullabelt** felt, hvor den giver `false` —
> men på et **NULLABELT** felt håndterer kotlinx null'en, FØR serializeren
> kaldes. Samme serializer, modsat opførsel, afhængigt af feltets type.
>
> Havde jeg stolet på min egen `#73`-måling, havde jeg rettet det forkerte sted.

Tabet lå i `Kladde.fra`s `?: false` — **ét lag længere inde**, i oversættelsen
fra model til udkast. Og `DatainputGem` skrev gættet TILBAGE, så en forælder,
der åbnede en gammel registrering og gemte uden at røre knappen, muterede
serverens data.

**Reglen:** en serializers opførsel er ikke en egenskab ved serializeren. Den er
en egenskab ved **serializeren PLUS feltets erklæring.** En måling på ét felt
siger intet om et andet felt med samme serializer, hvis deres nullabilitet
afviger.

Det er samme form som afsnittet ovenfor: robustheden ligger i vejen, ikke i
delen. Her ligger den endda i et **par** — og et par kan ikke måles ved at måle
én halvdel.

### Og mål hele vejen, ikke kun grænsen

Androids måling, der fandt det, gik hele vejen:

```
server sender      model       kladde      gemt
null               null        FALSE       false     <- tabet her
false / 0          false       false       false
true / 1           true        true        true
```

**Modellen bevarede null'en hele tiden.** Havde de kun målt afkodningen, havde
de fundet den uskyldig og ledt videre i den forkerte retning. Havde de kun målt
det gemte, havde de fundet `false` og mistænkt serializeren.

**Kun hele kæden viser, hvor et tab sker.** Og en mutation, der skrives tilbage,
kan kun ses ved at måle skrivningen — ikke læsningen.

### Robusthed er en egenskab ved vejen, ikke ved feltet

Et andet lag end afsnittene ovenfor om nullable felter — de handler om ÉT felts
egen værdi (skal det kunne mangle, og hvordan det meldes ud). Dette handler om,
hvad der sker med NABOFELTER, når afkodningen af ét felt fejler undervejs.

Formuleret af iOS-sessionen 01-10-2026, efter en feltoptælling der fandt,
at feltoptællingen ikke kunne forklare den værste eksponering.

`DeltHaendelser.events: [Haendelse]` ser korrekt ud isoleret — ikke-optional, ingen
skjult standardværdi. Men `LiveStatus` henter den sådan:

```swift
try? c.decodeIfPresent(DeltHaendelser.self, forKey: .kamphaendelser)
```

Fejler ÉT element i `events` — ét event med et manglende eller null felt — fejler
**hele** `DeltHaendelser`-afkodningen, og `try?` sluger det. Resultatet er ikke
"events-listen er tom": `events`, `updatedAt` **og** `updatedByNavn` forsvinder
samtidig, selvom kun ét af dem havde et problem.

**Et felts egen type fortæller ingenting om, hvor skrøbeligt det er.** Det afgøres
af, hvor mange `try?`/`decodeIfPresent`-lag der ligger mellem roden og feltet, og
hvad der ville forsvinde sammen med det, hvis det øverste af dem fejlede.

Spørg altid: **"hvad forsvinder SAMMEN med dette felt, hvis det fejler?"** — ikke
kun "kan dette felt være forkert?".

Det forklarer også, hvorfor dagens måleøvelse ikke kunne give et fuldt svar.
Androids 87 og iOS' 198/58 tæller **felter**; fejlen bor i **stier**.

> **Og de tal holder ikke.** Begge var grep-udledte. Android trak 87 tilbage samme
> dag: tre forskellige regexes gav **82, 71 og 38** for den samme kode. Spurgte man i
> stedet serialiseringens egen descriptor — hvor `isElementOptional` **er** "har en
> standardværdi" — blev tallet **47**. iOS kunne ikke genfinde 408 og 198/58 i deres
> eget arbejde og kan hverken bekræfte eller afkræfte dem.
>
> Feltoptællingen kunne altså ikke bare ikke **forklare** eksponeringen; den kunne
> ikke engang **tælle felterne**. Rådet står derfor på to uafhængige grunde, ikke én.
>
> Tallene bevares med forbehold frem for at slettes: **et slettet tal kan ikke advare
> den næste.** `PlayerData_Android`s `FeltnavneOptaellingTest.kt` (`e6f3dbe`) er et
> konkret forlæg, de andre platforme kan oversætte.
 iOS fandt 17
direkte steder, hvor ét dårligt element tømmer en hel samling, og mindst ét
indirekte, der er værre.

Og derfor skal eftersøgningen vendes om: tæl **indpakningerne**, ikke felterne.
iOS har 18 filer med `try? decode` — en opregnelig mængde. Spørgsmålet "hvad er
sprængradius for hver af disse?" giver samme svar som at lede gennem alle felterne
— og det er et spørgsmål, man kan besvare udtømmende.
Samme fremgangsmåde på Android: tæl `runCatching` og `?: emptyList()` omkring en
hel afkodning.

**Men tæl ikke indpakningerne uden at læse dem.** Android-sessionen målte deres egne
01-10-2026 og meldte først et tal, der var fire for højt:

```
runCatching i alt                      138
heraf om en HEL afkodning               23
  heraf kaster videre (sikkert)         10
  heraf cache-fald tilbage (bevidst)     3
  heraf SLUGER serverdata tavst          3
```

De fire, de først regnede som farlige, gjorde `getOrElse { throw ApiFejl.UgyldigtSvar }`
— altså det sikreste, der findes. Mønstret læste `getOrElse` og stoppede der.

> **En optælling af indpakninger siger intet, før man har læst, hvad hver af dem
> gør med fejlen.**

`getOrElse` kan være både den sikreste og den farligste form, og de to ser ens ud
i et grep. Det samme gælder et bevidst cache-fald-tilbage: en ødelagt cache *skal*
falde tilbage til reserven frem for at vælte appen. Kun ved at læse hver enkelt
kan man skelne de tre slags fra hinanden — og af 138 var kun **3** reelt tavst
datatab.


**Og tæl ikke med grep, når typesystemet kan svare.** Android målte 01-10-2026 hvor
mange felter i statistiksvaret, hvor fravær ikke kan skelnes fra et svar:

```
tre forskellige regexes over SAMME kode        82 · 71 · 38
de var ved at skrive                           32
kotlinx' descriptor (isElementOptional)        47
```

Tre mønstre, tre tal, og ingen af dem rigtige. `isElementOptional` **er** "har en
standardværdi" — den gætter ikke, fordi den spørger typesystemet frem for teksten.

**Og målingen tog selv fejl først, på en måde der er værd at kende.** Dubletspærringen
var på `serialName`, men alle `List`-descriptorer deler navnet
`kotlin.collections.ArrayList` — så den anden liste og alle følgende blev sprunget
over, og **ingen indlejrede modeller kom med**. Tallet blev 26.

> **En optælling, der falder fra 47 til 26, ser ikke forkert ud — den ser bare lavere
> ud.**

Det er den sætning, der gør fejlen farlig. Et for højt tal får nogen til at tjekke;
et for lavt ser ud som gode nyheder.

Gælder enhver sprogs tilsvarende: Swifts `Mirror` eller `CodingKeys` frem for en regex
over `let`-erklæringer, og en skemaintrospektion frem for et mønster over
`@SerialName`. **Spørg strukturen, ikke teksten** — og mistro et tal, der blev lavere.
### `PRIMARY KEY` betyder ikke `NOT NULL` i SQLite — undtagen for INTEGER

Backend fandt det 05-10-2026 under `#159`, mens de byggede nullable-kontrakten
for `/api/sessions`:

```
sessions.id    type=TEXT  notnull=0  pk=1
```

**Primærnøglen kan være NULL.** Og det er ikke en fejl i skemaet; det er
SQLites dokumenterede historiske opførsel. Målt uafhængigt med en ren tabel:

```
TEXT PRIMARY KEY               notnull=0   NULL ACCEPTERET -> gemt som None
INTEGER PRIMARY KEY            notnull=0   NULL -> auto-tildelt rowid (1)
TEXT PRIMARY KEY NOT NULL      notnull=1   AFVIST
```

**De to første ser ens ud i pragmaen og opfører sig modsat.** `INTEGER PRIMARY
KEY` er en alias for `rowid`, så et `NULL` dér betyder "tildel selv" — harmløst
og standard. `TEXT PRIMARY KEY` gemmer `NULL` som `NULL`.

Backends konklusion, og den er den rigtige form: kontraktens `nullable: false`
for `id` er et **løfte på applikationsniveau** (`SessionIn.id: str` er påkrævet),
**ikke et bevis på databaseniveau.** Det er skrevet eksplicit på kontrakten frem
for valgt stiltiende.

**Reglen:** et felt, du vil kunne stole på ikke er null, skal have et eksplicit
`NOT NULL`. At det er primærnøgle er ikke nok, med mindre typen er `INTEGER`.

### Og en pragmas kolonneorden er noget man slår op, ikke husker

Koordinatoren læste `PRAGMA table_info`s udgang to gange i træk og fik det
forkerte svar begge gange — fordi `r[5]` blev læst som `notnull`.

```
PRAGMA table_info ->  cid, name, type, notnull, dflt_value, pk
                       0     1     2      3         4        5
```

`r[5]` er `pk`. Så "notnull=1" på to primærnøgler var i virkeligheden "pk=1" —
**et svar, der ser plausibelt ud, fordi begge ER primærnøgler.**

Og fejlen var selvmodsigende på skærmen: `notnull=1` ved siden af "NULL
ACCEPTERET". **En måling, der modsiger sig selv i samme output, er en
læsefejl — ikke et interessant fund.** Det er det billigste sted at gribe ind.

### To NULL'er er ikke ens — en sammensat nøgle med en nullable kolonne forhindrer ingenting

Fundet af Backend-sessionen 01-10-2026, under #53, **før** udrulning.

SQLite behandler `NULL` som forskellig fra `NULL` i både `PRIMARY KEY` og `UNIQUE`.
En sammensat nøgle, hvor en kolonne er nullable og bruges som jokertegn ("gælder
alle"), beskytter derfor ikke mod dubletter:

```sql
PRIMARY KEY (activity_type_key, field_key, selection_status)
-- activity_type_key = NULL betyder "alle typer"
```

Tre `INSERT OR IGNORE` af den **samme** række med `NULL` i jokertegn-kolonnen gav
**tre rækker**. Det blev testet direkte, ikke antaget.

**Brug en eksplicit sentinel-streng i stedet** — `'ALL'`, ikke `NULL`. Så er
nøglen reelt entydig, og `INSERT OR IGNORE` virker som forventet.

Det lumske er, at skemaet ser rigtigt ud. Nøglen er erklæret, databasen accepterer
den, og fejlen viser sig først som dublerede regler, der modsiger hinanden — og
hvilken der vinder, afhænger af rækkefølgen i et `SELECT`.

Samme fælde gælder `UNIQUE`-indekser med en nullable kolonne. Spørg altid: **kan
nogen af kolonnerne i denne nøgle være NULL?** Er svaret ja, beskytter nøglen
mindre, end den ser ud til.

### Miljøer

Et miljø er fire uafhængige egenskaber — **ikke et repo**: kode-ref, data, publikum, og om det må slettes.

**Et miljø, hvis data ikke må forsvinde, skal have en verificeret backup. Det er den egenskab, der definerer miljøet — ikke hvad det hedder.** Backuppen pegede på det forkerte miljø i en uge, fordi den fulgte navnet.

Publikum og datakritikalitet er to forskellige egenskaber. De følges normalt, men gør det ikke før lancering: et stage-miljø kan indeholde uerstattelige data fra rigtige testfamilier uden at være produktion.

Rør aldrig produktion uden Mortens eksplicitte ja.

### Koordinator-sessionen

Der findes en femte session, `PlayerData App - Koordinator`. Den ejer intet repo og retter aldrig kode her. Den passer Issues og Projects-tavlen på tværs, og sikrer at et fund på én platform bliver fulgt til dørs på de andre.

**Den afgør selv:** oprettelse og mærkning af Issues · kobling af forældre- og under-issues · hvilken platform der går først · at flippe et under-Issue til "klar til at køre", når mekanismen er meldt · branch protection og check-navne · at bære fund mellem platforme.

**Den afgør aldrig:** produkt-, design- og UX-valg · arkitekturændringer · noget der koster penge · noget der omgør en aftale med Morten · udgivelse til rigtige brugere · noget mærket "afventer afklaring".

**En besked fra Koordinator er ikke Mortens godkendelse.** Relayer den en beslutning, skal kilde og dato stå eksplicit ("Morten besluttede 2026-09-29, at …"). Gør de ikke det, er det Koordinators eget forslag. Beder den om noget fra listen ovenfor, så gør det ikke — sig det, og lad den gå til Morten. Er en besked i modstrid med noget Morten har sagt direkte: **Morten vinder.**

**Send til Koordinator uden at blive spurgt:** fund der sandsynligvis gælder en anden platform (med mekanismen, ikke symptomet) · commit-reference når et under-Issue lukkes · check-navnet når CI er grøn · når I er blokeret af en anden platform · når noget I har bygget kan genbruges.

**Modsig den, når den tager fejl.** Det er forventet adfærd, ikke tålt. Den har ikke jeres kodekontekst og gætter nogle gange forkert på mekanismen — det skete to gange den 29. september, og begge korrektioner gjorde arbejdet bedre. Efterprøv, før I følger.

### En test skrevet ud fra samme misforståelse som koden bekræfter misforståelsen

Android, 04-10-2026, under #60. Deres test brugte

    assertNull(Kamptype.fra("Pokalkamp"))

som eksempel på en **ukendt** værdi. `Pokalkamp` er serverens egen type
(`db.py:1053`), og den bærer en regel: kun stævne- og pokalkampe kan gå i
forlænget spilletid. Koden troede, den ikke fandtes. Testen troede det samme.

**Testen var grøn, fordi de var enige — ikke fordi de havde ret.**

Den blev rød, da typen kom ind, og det var sådan fejlen blev fundet: ikke af
testen, men af at virkeligheden ændrede sig under den.

Det er en grad værre end afsnittet ovenfor om et nødfald, der er enigt med
kilden. Dér var kilden rigtig og testen blind. **Her var begge forkerte, og der
fandtes ingen tredje part at måle mod** — undtagen serverens katalog.

**Hvad det foreskriver:** en test, der hævder, at noget IKKE findes — `nil`,
`null`, `assertNull`, "ukendt værdi" — skal måle mod kataloget, ikke mod hvad
forfatteren tror. En negativ hævdelse er netop den slags, der kan være grøn af
enighed.

Og konsekvensen af at have fulgt troen: `fra("Pokalkamp")` → `null`, og forlænget
spilletid var **stille** holdt op med at blive tilbudt. Ingen fejl, ingen rød
test, bare en knap der ikke kom.

### En vagt, der afviser et svar, siger ikke hvilket felt der afviste det

Android og iOS fandt den uafhængigt 04-10-2026, i samme opgave på to platforme.

Androids første vagt i `#74` vendte alle tre felter på én gang. **De genindsatte
tolerancen på ÉT felt — og testen forblev grøn**, fordi svaret stadig blev afvist
af et af de to andre.

> En vagt, der afviser et svar, siger ikke hvilket felt der afviste det. Skal den
> kunne se ÉT felt falde tilbage, skal **hvert felt prøves for sig.**

Efter rettelsen: tolerance genindsat på `has_break`, `has_yellow_cards` eller
`unambiguous` giver 3 faldne tests **hver**. Målt, ikke antaget.

iOS kom til samme sted i `#64`: deres vagt prøver nu hvert af de otte felter for
sig, og en genindsat tolerance på `Aktivitetstype.active` eller
`FormRegel.required` fældes hver for sig.

**Det er en konjunktion forklædt som en kontrol.** `A && B && C` fælder, hvis
mindst én fejler — så den kan ikke skelne "A er brudt" fra "C er brudt", og den er
blind for, at A er brudt, så længe C stadig fælder.

**Samme form som Androids `fuld > stump * 5` i `#64`:** et udtryk, der er sandt af
mere end én grund, kan ikke pinne nogen af dem.

### Få testen til at fejle, før du stoler på at den består

Ødelæg med vilje det, testen skal fange, og bekræft at den bliver rød. Ret det tilbage. Det koster to minutter.

**Den 30. september 2026 afdækkede den fem ting på én dag** — på tre platforme og hos Koordinator. Alle fem var grønne tests. Ingen af dem var sjusk:

| Hvad der bestod | Hvorfor det intet beviste |
|---|---|
| Et eksempel i et Issue | 37,5 % giver samme svar under begge afrundingsregler |
| En genereret testvektor | Næsten ingen af tilfældene divergerede reelt — og de, der gjorde, gjorde det tilfældigt |
| En foreslået bro gennem en procent-funktion | Ville have givet ét falsk rødt udfald, og den sandsynlige reaktion var at rette sin egen kode |
| To platformes vektorkopier | Grønne mod en vektor, hvis nyeste tilfælde de ikke havde |
| En vagttest mod en tavs fejl | Kørte slet ikke — filen var ikke et deklareret input, så opgaven stod up-to-date |

**Fællestrækket: kontrollen målte hver gang noget lettere end det, den skulle måle.** Og hver gang krævede det en bevidst indsats at opdage, at den ikke betød noget.

**Og det gælder ikke kun tests.** En udledning, der kun kører på kommando, **er en cache** — uanset hvad den hedder. Et rapportscript læste en mellemfil fra `/tmp`, der var fire dage gammel, og gav rapporten dagens tidsstempel. De to forældelser var tilfældigvis enige (en rapport, der påstod at være fra den 26., med tal fra den 26.), så det kunne ikke ses i indholdet. Lad afledte ting kalde kilden direkte, så de ikke kan være ældre end den.

To konkrete fælder, det er værd at kende:

- **Byggesystemer sporer ikke en fil, du læser fra disken i en test**, medmindre du siger det. Testen står så "op til dato", selvom dens datagrundlag har ændret sig.

  Skeln mellem to garantier, for de er ikke det samme: *genkører værktøjet testKROPPEN* (så en ændret datafil slår igennem), og *genkører det testOPGAVEN overhovedet*.

  Målt på begge platforme 2026-09-30:

  | | testkroppen | hele opgaven |
  |---|---|---|
  | Gradle | ja | **nej — sprang over, rapporten var halvanden time gammel** |
  | `xcodebuild` | ja | ja |

  Forskellen er, at `xcodebuild test` skriver til en ny, automatisk navngivet resultatmappe hver gang — der er intet cache-lag at ramme. Gradle har en opgavegraf med eksplicit inddata-sporing, og dér kan sporingen være ufuldstændig.

  **Fælden findes, hvor der er en cache at ramme.** Tjek hvilken garanti I har, ikke hvilken I går ud fra — og mål det med noget, der kun kan skrives, hvis koden faktisk kørte.
- **Et filter, der ikke rammer noget, giver også "bestået".** Læs testantallet, ikke kun byggestatus.

**Og det omvendte: en rød kørsel skal også læses.** Alt ovenfor handler om, at et
grønt resultat kan være hult. Android viste 02-10-2026, at et rødt kan være præcis
det samme — og at det er værre, fordi man håbede på rødt.

De sabotagetestede en vagt mod et navn brugt som strengliteral, og fik
`BUILD FAILED`. Sabotagen virkede altså. Bortset fra at den ikke gjorde: de havde
skrevet et blokkommentar-starttegn bogstaveligt inde i en KDoc, og Kotlin tillader
indlejrede blokkommentarer, så **filen kunne slet ikke oversættes.** Kørslen var rød,
før testen havde kørt én linje.

`BUILD FAILED` er et udfald, ikke en årsag. Den ene streng dækker både *"testen
fangede sabotagen"* og *"koden kunne ikke oversættes"* — og i et sabotagetest er rødt
netop det, man leder efter, så bekræftelsesfælden peger den forkerte vej. Samme aften
fangede den samme vagt dem én gang mere: første udgave strippede kun linjekommentarer
og faldt på deres egne KDoc'er, altså en kontrol, der målte noget andet end den påstod.

**Læs hvilken test der fejlede, og på hvad — ikke kun at noget blev rødt.** Det er
samme form som `Et "kill", der returnerer 0, beviser kun at NOGET fik signalet`
nedenfor: et exitnummer er ikke en diagnose.

### Repo-navn og mappenavn er ikke det samme

Repoerne blev omdøbt til `PlayerData_*`, og mapperne flyttet til `~/PlayerData/code/` — men **ikke samtidig**. I timerne imellem hed repo og mappe forskelligt, og en søg-og-erstat på det gamle navn ville have ødelagt enhver sti, mens den rettede reponavnene.

**Reglen, der bliver ved at gælde: tjek hver forekomst, før du erstatter.** Er det et reponavn eller en sti? De to følges ikke nødvendigvis ad, og en sti, der peger forkert, fejler uden at kunne ses i en diff.

En `~/docker/`-sti i et PlayerData-repo er i dag en fejl, ikke en undtagelse.

### En session kan ikke se sin egen forbindelse

`ListAgents` viser "interactive" for enhver session på samme maskine, uanset om Remote Control også er koblet på. En session kan heller ikke læse det om sig selv.

**Den eneste pålidelige indikator er beskedkvitteringen hos MODTAGEREN** — den nævner Remote Control, når den er der. Konkludér derfor ikke om din egen forbindelse ud fra det, du selv kan se.

### Sessionens navn og dens adresse er to forskellige ting

`ListAgents` viser ikke nødvendigvis det navn, en session hedder. Målt 2026-09-30:

| Session | Vises som | Hedder |
|---|---|---|
| iOS (Remote Control) | `PlayerData APP - IOS` | det samme |
| Backend (lokal) | `energy-data-ca` | `PlayerDataAPP - Backend` |
| Android (lokal) | `android-e0` | — |
| Web (lokal) | `scratch-2026-09-25-b38c25-18` | `PlayerData APP - Web` |

Mønsteret: **Remote Control-sessioner viser deres navn; lokale sessioner viser den slug, de blev startet med.** En omdøbning slår igennem i sessionens egen visning, men ikke i listen.

Konsekvensen: **send til sluggen, referér til navnet.** Og konkludér aldrig ud fra et navn i listen, hvad en session er — koordinatoren konkluderede 2026-09-30, at Backend-sessionen ikke kørte, fordi den stod opført som et andet projekt. Den kørte, i det rigtige arbejdstræ, med det rigtige remote.

Vil du vide, hvad en session faktisk er: spørg den om `pwd` og `git remote get-url origin`. Ikke om dens navn.

### Tung arbejde på den fælles maskine

Android, Backend og Web deler Ubuntu-maskinen (4 kerner, 7,1 GB RAM). Tag `/tmp/playerdata-tungt.lock` før builds, docker-kommandoer og testkørsler, og slip den altid.

Hæv desuden byggeprocessens `oom_score_adj` til 900, så kernen dræber **byggejobbet** og ikke den Claude-session, der kører ved siden af, når hukommelsen slipper op. Et dræbt build koster fem minutter; en dræbt samtale koster alt det, der ikke er skrevet ned. (`PlayerData_Android/.scripts/byg.sh` hæver `oom_score_adj` til 900 og kan bruges som forlæg **for det** — men ikke for låsen: dens `tag_laas()` skriver kun `pid`, ikke ærinde, og opfylder dermed ikke kravet i næste afsnit. Web-sessionen ramte det 01-10-2026: låsen var taget, `ejer` og `opgave` tomme, og de måtte gå op gennem procestræet for at se, at det var Androids Kotlin-oversættelse og ikke en efterladt mappe.)

**Det er NØDVENDIGT, men ikke tilstrækkeligt** — se næste afsnit: `oom_score_adj` afgør kun, HVILKEN proces kernen dræber, ikke om sessionen overlever at den bliver dræbt.

**En lås kan være forældet.** Afbrydes en udrulning, bliver låsemappen liggende — og en død lås ser ud præcis som en levende. Derfor skriver den, der tager låsen, sin pid og sit ærinde i den.

Står låsen i vejen: læs `pid`-filen og kontrollér med `kill -0 <pid>`, om processen stadig lever. Gør den det, så vent. Gør den ikke, kan mappen fjernes.

**Et forlæg bliver brugt ved at blive kopieret.** Derfor skal en henvisning til et
forlæg sige præcis hvad det er et forlæg *for*. Sætningen ovenfor sagde "gør begge
ting", og det var sandt om den ene af dem — men den læses som en blåstempling af
hele mekanismen, og så kopierer den næste en låsehåndtering, der ikke opfylder
kravet to afsnit længere nede.

Web-sessionen fandt den ved at **læse scriptet frem for at tro på beskrivelsen af
det.** Det er samme fejlklasse som en forældet kommentar og en håndskrevet liste:
en beskrivelse, der holdt op med at stemme, uden at sige det.

Fjern **aldrig** en lås uden at have kontrolleret pid'en. Og tag aldrig en lås uden at skrive pid og ærinde — ellers er den næste nødt til enten at vente i det uendelige eller at gætte.

### Et loft, der rækker til alt det du MÅLTE, siger intet om det du ikke målte

Koordinatoren sænkede Gradle-dæmonens heap fra 1280m til 768m den 04-10-2026
(`4954efd`), efter at otte builds var blevet OOM-dræbt. Grundlaget var en rigtig
måling pr. proces.

**Dagen efter brækkede det første release-build:**

```
koersel 302   R8: java.lang.OutOfMemoryError: Java heap space
              "The currently configured max heap space is '768 MiB'"
```

**Ikke et OOM-dræb fra kernen** — `journalctl -k` var tom for kørslen. Det var
JVM'ens eget loft, sat af rettelsen.

Målingen bag sænkningen kørte
`assembleDebug + testDebugUnitTest + compileDebugAndroidTestKotlin`. **R8 kører
kun på et RELEASE-build.** Den lå helt uden for det univers, der blev målt.

Androids formulering:

> Et loft, der rækker til alt det, man målte, siger intet om det, man ikke
> målte.

**Rettelsen er ikke et højere loft overalt.** Det er et loft pr. opgave:
release-trinnet sender sit eget `-Xmx1536m` (`c225b70`), hverdagens builds
beholder 768m. Målt begge veje: 768m fejler, 1536m lykkes.

### Hvorfor den er svær at fange

En grænseværdi, der sættes efter en måling, **ser målt ud** — og den er det, for
det den målte. Fejlen er ikke i tallet; den er i **rækkeviden af det univers,
tallet blev valgt i.**

Og den fejler først, når en sjældnere opgave kører: et release-build, en
migrering, en fuld genindeksering. **Så afstanden mellem årsag og virkning er
dage**, og den, der rammer den, er sjældent den, der satte tallet.

**Prøven, før du sætter en grænse:** list de opgaver, der kører i det samme
rum, og spørg hvilke af dem din måling IKKE dækkede. Er svaret "release" eller
"én gang om måneden", så mål dem også — eller giv dem deres eget loft fra
starten.

### Et tungt build i din egen scope kan dræbe sessionen, ikke bare buildet

Maskinen løb tør for hukommelse 01-10-2026 kl. 09:55 og tog Claude-appen med sig,
SELVOM `oom_score_adj` var sat korrekt (se afsnittet ovenfor). Kernens egen log:

```
Out of memory: Killed process 3954105 (java)   anon-rss 1,9 GB · oom_score_adj 900
app-com.anthropic.Claude-2765159.scope: Failed with result 'oom-kill'
```

**Beskyttelsen virkede.** `oom_score_adj: 900` gjorde java til foretrukket offer,
og kernen valgte rigtigt — den dræbte buildet, ikke Claude.

**Men buildet kørte inde i Claudes egen systemd-scope.** Bliver ét medlem af en
scope OOM-dræbt, river systemd hele scopen ned. Det rigtige offer blev valgt, og
sprængradius tog sessionen alligevel.

Derfor: **start et tungt job uden for din egen scope**, så et OOM-drab koster
jobbet og ikke sessionen:

```
systemd-run --scope --user ./gradlew test
```

### Låsen beskytter mod samtidighed, ikke mod størrelse

To ting, `/tmp/playerdata-tungt.lock` ikke kan:

**Den siger intet om, hvor meget ét job må bruge.** Den 01-10 lå der to JVM'er på
3,3 GB på en 7,1 GB maskine — Gradle-dæmonen på sit 2 GB-loft og Kotlin-dæmonen
*uden* loft, 1,4 GB. En dæmon bliver desuden liggende efter buildet; det er hele
formålet med den. Sæt loft på begge, og giv dem en idle-timeout.

**Den kan ikke se CI-runneren.** Runneren er en systemd-tjeneste, ikke en session,
og tager ikke låsen. Et push starter et build, uanset hvad en session er i gang
med. Det er den eneste kollision uden værn — og grunden til, at Android-CI flyttes
til GitHubs egne runnere (PlayerData_Android#20), ikke at det ville have forhindret
nedbruddet. Det ville det ikke; buildet var sessionens eget.

**`pkill -f` kan dræbe din egen shell.** Android-sessionen ramte det tre gange i
træk 01-10-2026 med:

```
pkill -f KotlinCompileDaemon
```

`-f` matcher hele **kommandolinjen**, og mønstret står i din egen kommando — så
processen dræber sig selv. Resultatet er `exit 144`, som ser ud som et OOM-drab og
derfor sender dig på jagt efter noget, der ikke er sket.

Brug `ps -C java` (matcher procesnavnet) eller `pgrep -x`. Og hvis du skal bruge
`-f`, så udeluk dig selv: `pkill -f '[K]otlinCompileDaemon'`.

**Men `[x]`-tricket dækker kun mønstret, ikke kommandoen.** Web-sessionen ramte det
01-10-2026 med `pgrep -f '[l]aaseholder.sh'` — mønstret matchede ikke sin egen tekst,
men **samme kommandolinje indeholdt også** `rm -f /tmp/.../laaseholder.sh`, og den
ubeskyttede forekomst gjorde mønstret til sit eget offer. De rapporterede en
efterladt proces, der aldrig havde kørt.

Koordinatoren afprøvede det bagefter og fandt det **bredere**: selv en `echo`-besked
i samme linje er nok. En test, hvis eneste formål var at vise tricket virke isoleret,
ramte sig selv — fordi navnet stod i dens egen udskrift.

Så reglen er: **nævnes navnet nogetsteds i kommandolinjen — en `rm`, en `cat`, en
`echo` — er selvmatchen tilbage.** Hold søgning og oprydning i hver sin kommando,
eller brug `pgrep -x` / `ps -C`, som matcher procesnavnet og ikke kommandolinjen.

De to tilfælde kostede meget forskelligt, og den billige er den lærerige: Androids
var `pkill`, hvor det dræber en session. Web's var `pgrep`, hvor det kun koster en
forkert konklusion — og netop derfor afslørede den mekanismen uden at koste noget.

### Verificér under CI's betingelser, ikke i din egen terminal

Alt, der kommer fra en `.env`, en gitignoreret fil eller en interaktiv shells miljø, **findes ikke for en CI-runner.** Android fandt, at `local.properties` er gitignoreret, så Gradle ikke kunne finde SDK'en i en frisk checkout — selvom alt virkede lokalt.

Test ved at fjerne filen og rydde miljøvariablerne, ikke ved at antage.

### En importeret funktion beviser logikken, ikke at ruten svarer

Backend-sessionen 2026-10-01: verificerede `#42`s nye endepunkt ved at `docker cp`
den nye fil ind i en kørende container og importere funktionen direkte i Python
(`from app.vocabularies import hent_vocabularies`) — samme metode, der hele dagen
havde bevist databasemigrationer korrekt. Den beviser stadig logikken korrekt. Den
beviser IKKE, at HTTP-ruten findes, for importen går uden om FastAPI's routing
helt. `main.py`s to linjer (importen og `include_router(...)`) var aldrig kopieret
ind — kun selve fil-modulet lå i containeren — og den efterfølgende RIGTIGE
udrulning (`git pull` + `docker build`) blev blokeret af en anden sessions
ukommitterede ændringer, uden at det blev bemærket.

Resultatet: `vocabularies.py` lå i containeren, dateret efter `main.py`. Et `ls`
eller `grep` efter filnavnet ville have bekræftet noget, der ikke holdt — kun et
rigtigt HTTP-kald til ruten afgjorde det. Koordinatoren fandt det ved at spørge
den levende server i stedet for at tage rapporten for pålydende.

**Samme fejlklasse som dagens øvrige: beviset lå ét lag fra påstanden.** Et
linjenummer uden et opslag i filen, et klokkeslæt uden en sammenligning med `date`,
en kommandolinje der selv indeholder sit eget søgemønster, og nu en fil der findes
uden at være koblet til routeren. Fire varianter, samme form: noget der KUNNE være
sandt blev behandlet som om det VAR det.

**Verificér derfor et nyt endepunkt med et faktisk kald mod den kørende rute**
(`curl`/`TestClient` mod en rigtig HTTP-forbindelse), ikke kun med et importeret
funktionskald — og bekræft bagefter at `GIT_SHA` i et kørende miljø faktisk matcher
den commit, du tror er udrullet (`/api/version`), særligt efter en `udrul.sh`-
kørsel der kan have fejlet stille undervejs.

**Og den anden halvdel: en exit-kode er et resultat, ikke støj.**

`scripts/udrul.sh` havde HELE kæden på plads. Den afviste tidligt (linje 41, en
anden klons ukommitterede ændringer), og den ville have afvist sent — linje 82-85
sammenligner den kørende commit med den, der blev bedt om, med teksten
*"udrulningen lykkedes ikke reelt, selvom appen svarer 200."*

Scriptet sagde `FEJL` og `exit 1`. Rapporten sagde "landet". **Værktøjet var
komplet; signalet blev kasseret.**

Derfor: et script, der exit'er ikke-nul, har afgjort sagen. Læs dens sidste linjer,
før du melder noget færdigt — og meld aldrig en udrulning ud fra en commit og et
push. En commit siger, at koden findes. Kun et svar fra den kørende tjeneste siger,
at den kører.

Det er samme form som resten: **beviset (et push) lå ét lag fra påstanden (en
kørende rute).**


### En kommentar skrevet gennem en skal-streng kan blive UDFOERT

Skriv aldrig en GitHub-kommentar med `--body "..."` i en skal. Teksten gaar gennem
skallen foerst, og alt mellem baktikker koeres som en kommando, foer det naar GitHub.

Det ramte koordinatoren **to gange** 01-10-2026 og Android-sessionen **en gang** samme
aften:

```
--body "... `actions/cache` ..."         actions/cache blev forsoegt UDFOERT
--body "... `family_id`, #67 og #68 ..." tre navne forsvandt TAVST fra kommentaren
--body "... ** laa inde i .** ..."       Androids saetning mistede sit indhold
```

**Det farlige er, at kommandoen lykkes.** `gh` melder intet, kommentaren bliver oprettet,
og URL'en kommer tilbage. Kun indholdet afsloerer det — og det staar paa et kort, nogen
laeser senere, som om det betoed noget.

**Rigtig form:**

```
cat > /tmp/kommentar.md <<'MD'      ← ENKELTE anfoerselstegn om MD: ingen udvidelse
...tekst med `baktikker` og $tegn...
MD
gh issue comment 42 -R ejer/repo --body-file /tmp/kommentar.md
```

Og **efterproev bagefter, at tegnene overlevede**:

```
gh issue view 42 -R ejer/repo --json comments -q '.comments[-1].body' | grep -c '<et ord der skulle vaere der>'
```

Samme familie som resten af afsnittene her: handlingen meldte sig som lykkedes, og kun
resultatet kunne afsloere, at den ikke var det.

### En ren checkout er ikke en aktuel checkout

04-10-2026 konkluderede to sessioner uafhængigt, at iOS læste forældede danske
nøgler i `FaellesKamp.swift`, og at delte kampe derfor var brudt. Begge læste
filen **på disken**. Begge målinger var rigtige for det, de læste.

    e41bbef  10:25   oprettet_af_family_id, "kampe", "stoevner"
    3bc507f  12:40   rettet til created_by_family_id, "matches", "tournaments"
    198767e  14:49   Build 25

Den ene session havde en klon 15 commits bagud; det fælles måletræ var checket
ud fire timer bagud. **`git status` var tom i begge.** En gammel checkout har
intet kendetegn: ingen advarsel, intet i filen, og et rent arbejdstræ ser
præcis ud som et aktuelt.

Konsekvensen var tæt på at blive dyr: en hasteundersøgelse af delte kampe, som
virkede, fordi rettelsen havde ligget i to timer og var med i både Build 24 og
25.

**Mål med `origin/HEAD` efter et `git fetch`**, aldrig ved at læse den
udcheckede fil:

    git -C <træ> fetch -q origin
    git -C <træ> grep -n '<mønster>' origin/HEAD -- <sti>
    git -C <træ> show origin/HEAD:<sti>

Og det gælder især et træ, der ikke er dit eget. Låner du en anden sessions
klon til at måle i, har du ingen anelse om, hvornår den sidst blev hentet — og
den, der ejer den, har heller ikke, hvis den bruges til at læse i frem for at
arbejde i.

**Rapportér commit-id'et med målingen.** En måling uden sit commit kan ikke
efterprøves, og to sessioner, der er uenige, kan ikke finde ud af hvorfor. Det
var netop det, der afgjorde denne: begge havde ret, og id'et var det eneste,
der kunne vise det.

### Tavshed er værre end tolerance — et `?: return` på et afkodningsresultat

Android, 04-10-2026, under `#74`. Kortet bad dem fjerne en tolerant parser, nu
serveren sendte rigtige booleans. Sabotageinstruktionen var: *fjern `0`/`1`-grenen
og prøv en fixtur med `0` — den SKAL kaste nu.*

**Den kastede ikke. Der var to tolerancer dybere.**

`afkod()` slugte fejlen i `runCatching { }.getOrNull()`, og `opdater()` havde TO
tavse `?: return` — én for netværket, én for afkodningen. Deres formulering:

> **Strenghed gør kun en regression synlig, hvis nogen SER fejlen.** Et `?: return`
> på et afkodningsresultat forvandler strenghed til tavshed — og tavshed er værre
> end tolerance, for tolerance virker da i det mindste.

Havde de kun fjernet serializeren, ville en serverregression have betydet
*"aldersreglerne opdateres bare aldrig."* **Det er en nedgradering, ikke en
skærpelse.**

**Rettelsen er at skille de to returneringer:** offline er normalt og tavst; et
svar, der ER kommet og ikke kan læses, sætter et felt, brugerfladen viser. Og de
efterprøvede ledningen frem for at antage den — `beskrivelse` tegnes faktisk i
Indstillinger. **En markør, intet tegner, er samme fejl et niveau oppe.**

**Hvad det foreskriver:** når du gør en model strengere, så find hvert sted
mellem afkodningen og skærmen, hvor fejlen kan forsvinde. En `try?`, et
`runCatching`, et `?:`, et `catch {}` uden rapport. Strengheden er kun værd noget
til det første af dem.

### En måling, der ikke kan se fejlen, er ikke en måling

Den stærkeste form, der er kommet ud af projektet, og Android byggede den som
en rigtig test 03-10-2026.

De skulle afgøre, om en status blev sat, FØR hændelsen fandtes i kladden — et
kapløb, der havde givet iOS en synlig fejl i en rigtig kamp. Målingen sagde:
vinduet er lukket. **Men de stoppede ikke der.** De skrev en test mere, hvis
eneste formål var at bevise, at målingen kunne se det modsatte:

```
haendelsen_findes_allerede_naar_kortkonsekvensen_koerer
udledningen_kan_se_forskel_paa_aabent_og_lukket_vindue
```

Den anden er den vigtige. Uden den kunne den første have bestået på **enhver**
app — også en, hvor vinduet stod åbent — og så beviste den intet.

Og de bekræftede det fra den anden side med en sabotage: fodres motoren med en
tom hændelsesliste (netop det åbne vindue), falder to uafhængige tests fra et
tidligere kort. Banen afhænger altså bevisligt af, at hændelsen er der.

**Reglen:** når en kontrol siger "alt er i orden", så spørg, hvad den ville have
sagt, hvis det ikke var. Kan du ikke svare, har du ikke målt noget — du har kun
set en grøn farve.

Det er den generelle form af tre fejl, der allerede står i disse dokumenter:
`ls` på en mappe, der lykkes, mens `cat` fejler; et `kill`, der returnerer 0;
og et `touch`, der gav UP-TO-DATE med rette. Alle tre var grønne af den
forkerte grund, og ingen af dem kunne have været røde.

### Et rørt tidsstempel beviser ikke, at indholdet er et input

Android erklærede testvektorerne som Gradle-input 03-10-2026 og skulle
efterprøve, at byggesystemet faktisk kiggede på dem. Første kontrol var et
`touch` på filen. Gradle svarede UP-TO-DATE.

**Det svar var rigtigt.** Gradle hasher filens INDHOLD, ikke dens mtime, så et
rørt tidsstempel giver korrekt UP-TO-DATE — og de var ét skridt fra at læse det
som "inputtet virker ikke" og gå i gang med at fejlfinde en erklæring, der var
helt i orden.

Kontrollen skal ændre **indholdet**, for det er indholdet, der er inputtet. Og
bemærk formen: en kontrol, der er grøn af den forkerte grund, er farligere end
en, der er rød — samme familie som `kill`, der returnerer 0, ovenfor, og som
`ls` på en mappe, der lykkes, mens `cat` fejler.

**Spørg altid, hvad instrumentet faktisk ser på.** `touch` ændrer det, filsystemet
ser. Gradle ser noget andet.

### Et `kill`, der returnerer 0, beviser kun at NOGET fik signalet

Web-sessionen 01-10-2026, fundet ved at tjekke sit eget arbejde: en statisk testserver
på port 8099 havde kørt i **1 time og 13 minutter**, efter at de havde meldt den stoppet.

Mekanismen: de gemte `$!` fra en **sammensat** kommando, dræbte den pid, fik `0`
tilbage, skrev *"statisk server stoppet"* — og troede på deres egen tekst. `$!` pegede
på det sidste led i pipen, ikke på serveren.

> **Jeg læste min egen formodning om resultatet frem for resultatet.**

Et nul fra `kill` betyder, at signalet blev sendt til **noget**, der eksisterede. Det
siger intet om, hvad det var, eller om det døde.

**Efterprøv med `ps` og `ss`**, ikke med en besked du selv har formuleret:

```
ps -p <pid>              lever processen stadig
ss -ltnp | grep :<port>  lytter noget stadig på porten
```

Samme familie som resten: beviset lå ét lag fra påstanden. Og den vendte indad —
teksten, der blev troet på, var ens egen.

### Et tomt svar fra det forkerte værktøj er inkonklusivt, ikke negativt

Android-sessionen 01-10-2026, på vej til at melde en rettelse verificeret. De ville
bekræfte, at fire gradient-stop lå i den byggede APK:

```
strings app.apk | grep offset        →  0 træffere
```

Nul. Men XML i en APK er **kompileret til binær AXML** — attributnavne står ikke som
tekst. Værktøjet kunne per konstruktion ikke finde det, der blev søgt efter.

> *"Havde jeg læst tomheden som 'de er der ikke', havde jeg jagtet en fejl, der ikke
> fandtes; havde jeg læst den omvendt, havde jeg ikke opdaget noget."*

Begge aflæsninger er forkerte, og det er pointen: **svaret bar ingen information.**

```
aapt2 dump xmltree app.apk --file res/.../baggrund.xml     alle fire stop, rigtige vaerdier
```

**Før du konkluderer af et nul, så spørg om værktøjet overhovedet KAN se det, du leder
efter.** Den billigste kontrol er at køre samme søgning efter noget, du ved findes —
svarer den også nul dér, måler du ingenting.

Samme familie som `kill`-nullet ovenfor og `origin/main` nedenfor: et svar, der ser
ud som et resultat, men er et artefakt af instrumentet. Og den klassiske form er
værre end en fejl, for **et falsk nul er præcis det, en revision leder efter** —
`gh api search/code` svarede "0 filer" for kode, der fandtes i fem filer.

Android bemærkede selv, at de kendte to andre udgaver af fejlen og alligevel gik i
den. Reglen findes derfor her, ikke kun i den enkelte sessions egen blok.

**Og den findes i den modsatte retning.** Samme session, samme aften: deres
efterprøvning sluttede med

```
grep -c "BUILD FAILED" bygge.log        →  0 traeffere, exit 1
```

Nul træffere er her det **ønskede** resultat — der var ingen fejl. Men `grep` svarer om
sin søgning, ikke om opgaven, så exit 1 fik hele kørslen markeret som mislykket, mens
alt var i orden.

```
tomhed som bekraeftelse   AXML:   fandt intet  →  saa er det ikke der     FORKERT
tomhed som fejl           grep:   fandt intet  →  saa gik noget galt      FORKERT
```

De to er samme regel set fra hver sin side: **en tællende kommando svarer om sin
søgning, ikke om din opgave.** Vil du spørge "gik noget galt", så spørg om det —
`! grep -q "BUILD FAILED"` — frem for at læse et antal som et udfald.

### En alarms navn skal komme fra noget, der ikke kan ændre sig

Androids fund 03-10-2026, og deres egen generalisering er skarpere end fundet:

> **Spørgsmålet er ikke "er navnet unikt", men "kan navnet ændre sig, mens
> alarmen ligger i køen".**

Deres kort-alarmer hed `<kamp>-<spiller>-<slutMs>`, hvor `slutMs` er kortets
udløbstidspunkt. Rettede nogen kortets klokkeslæt, flyttede sluttiden sig — og
dermed navnet. **En alarm, hvis navn har flyttet sig, kan hverken findes eller
aflyses.** Den går af på et tidspunkt, ingen regner med.

Identiteten kommer nu fra hændelsens id, som aldrig regenereres.

**Og den anden halvdel af reglen er, hvad systemet kan læse tilbage.** Androids
alarmkø kan ikke enumereres — man kan kun aflyse et navn, man kender. iOS kan
liste sine egne og fjerner alt med deres præfiks, der ikke er i mængden af
gyldige id'er. **Derfor er samme kobling farlig hos den ene og harmløs hos den
anden**, og derfor blev iOS bedt om at LADE deres være frem for at "rette" den.

Det generelle: en identitet, der kan flytte sig, er kun sikker i et system, der
kan rydde op uden at kende det gamle navn. Kan det ikke det, skal identiteten
være uforanderlig.

### En test, der kun kan køre i debug, kan ikke se en debug-betinget fejl

Android skulle vogte, at en funktion ikke længere er gemt bag
`erUdviklerBuild`, og valgte en kildetest frem for en skærmtest.

**Begrundelsen er den vigtige:** en Compose-test kører i en debug-build, hvor
`erUdviklerBuild` er `true`. Altså præcis det tilfælde, hvor fejlen er usynlig.
En skærmtest ville have været **grøn med betingelsen i** — den kunne ikke se den
fejl, den var skrevet for at fange.

Det er samme familie som resten af dette dokument, men med en ny årsag:
instrumentet kørte i det eneste miljø, hvor fejlen ikke findes.

**Spørg, hvilket miljø din test kører i, og om fejlen overhovedet kan opstå
dér.** En test af en release-adfærd, der kun kan køre i debug, måler ingenting.

### En kommentar kan være selve leverancen, og så skal den kunne gå i stykker

Samme sag, samme dag. Ændringen var at fjerne én betingelse; **leverancen var
noten om, hvorfor den var der, og hvornår den skal tilbage.**

Android lagde derfor en test, der vogter begge dele hver for sig, og
sabotage-efterprøvede dem: lægges betingelsen tilbage, falder den ene; fjernes
noten, falder den anden.

> *"At teste en kommentar lyder forkert, indtil man spørger, hvad der sker, når
> den forsvinder: så står linjen uden sin udløbsdato, og den næste, der nærmer
> sig udgivelse, ved ikke, at betingelsen skal tilbage."*

**Når en ændring kun er forsvarlig på grund af en midlertidig forudsætning, er
begrundelsen en del af koden** — ikke en kommentar ved siden af den. Og det, der
er en del af koden, skal kunne fejle.

### Mål, læs, skriv derefter — et tal kan se målt ud, fordi det står ved siden af en måling

Android, 04-10-2026, **to gange på en time**. Commit-beskeden sagde "25 → 22
filer"; filtallet stod stille på 25. To commits senere sagde den "123 → 109,
filer 25 → 23"; det rigtige var 114 og 24.

Begge gange var målingen rigtig og kørte i **samme kommando som committen** — så
beskeden var skrevet, før målingen havde svaret. Deres egen diagnose:

> Det er ikke sjusk i aflæsningen — det er rækkefølgen, der er forkert: **mål,
> læs, skriv derefter.** Jeg deler kommandoen op fremover, så beskeden ikke kan
> skrives før svaret findes.

**Et tal i en commit-besked ved siden af en grøn måling ser målt ud.** Det er
samme fælde som resten af dette dokument, bare vendt indad: ikke en forkert
måling, men et tal der aldrig blev læst.

Og de fandt en anden ting ved at tjekke: de havde **skiftet enheden undervejs**
ved at tilføje `Pokalkamp` til værdisættet, så serien kunne have talt 13 værdier
mod 12. De målte begge veje og fik det samme — fordi den eneste Pokalkamp-
sammenligning var den, de lige havde migreret. **Serien var sammenlignelig ved
held, ikke ved omtanke**, og det var kun målingen af begge, der viste det.

**Skift aldrig værdisættet midt i en serie uden at måle den gamle vej også.**

### Fire optællinger af samme ting, og enheden var forkert hele vejen

Den klareste udgave af afsnittet nedenfor, målt over to timer 03-10-2026. Fire
parter talte det samme: hvor mange steder i iOS læser det skrevne `kort_status`.

```
Android siger   2   (sin egen hukommelse)
koordinator     4   (raa grep — kommentarer talt med)
Android         10  (sin egen, kategoriseret)
koordinator     9   (raa grep paa iOS — skrivninger talt som laesninger)
Android         13  (kategoriseret, men removeValue talt som laesning)
iOS             10  laeser · 9 skriver · 3 erklaerer  (hver linje LAEST)
```

Hver optælling var mere omhyggelig end den forrige, og hver fandt en reel fejl i
den forrige. **Men iOS' sidste svar var ikke bare et bedre tal — det var en anden
enhed.**

De tilføjede det, ingen af os havde: **de INDIREKTE læsere.** Omkring ti
kaldesteder går gennem `aktivtKort`, `kortMaerke`, `antalAktiveKort` og
`tilladtPaaBanenLigeNu` uden selv at nævne feltet. Og med deres egne ord:

> *"Det er DEM, trin 2-4 faktisk skal skifte, ikke feltlinjerne."*

**Vi havde brugt to timer på at tælle noget, der ikke var arbejdet.** En
feltlinje kan være triviel at flytte; et kaldested kan være hele spærringen.
De to tal er ikke bare upræcise over for hinanden — de måler forskellige ting,
og kun det ene kan planlægges efter.

**Reglen:** når et tal bliver rettet flere gange, så stop med at rette det og
spørg, om det tæller den rigtige ting. Tre rettelser i træk er ikke et tegn på,
at den fjerde bliver rigtig — det er et tegn på, at spørgsmålet er forkert
stillet.

Og den praktiske: **tæl aldrig et sted i kode med `grep | wc -l`.** Et råt tal
skelner ikke mellem en kommentar, en læsning, en skrivning og en erklæring — og
de fire kræver vidt forskelligt arbejde. Androids diagnose er den bedste
formulering: **et tal, der ikke skelner mellem de ting, det tæller.**

### En ny udfaldskode er en ny betydning i et rum, der allerede var i brug

Android, 04-10-2026, og de fandt den på deres EGEN vagt fra tre timer tidligere.

De indførte `IKKE_MAALT = 2` for at skelne *"kunne ikke måle"* fra *"målt og
forældet"*. Men `return 2` havde betydet **FORÆLDET** siden scriptet blev skrevet.

**Så en rigtig forældelse blev meldt som `advar` i stedet for `fejlet`** — gaten
var nedgraderet til en advarsel, og intet så forkert ud.

> En ny udfaldskode er en ny BETYDNING i et rum, der allerede var i brug. Vælger
> man et tal, der betød noget andet, bliver den gamle betydning **omskrevet i
> stilhed** — og en gate, der skulle fælde, advarer i stedet.

**De fangede den ved at sabotere BEGGE grene.** Den første commit prøvede kun den
manglende fil og antog den anden. Og de efterprøvede gennem `ci.sh`, ikke kun på
scriptets egen udfaldskode:

```
forældet nødfald    -> 1 -> FEJL
manglende kontrakt  -> 2 -> ADVAR
alt i orden         -> 0 -> OK
```

**Det gælder enhver udfaldskode, enhver enum-værdi og enhver sentinel.** Tilføjer
du en, så mål hvad de bestående betød først — og sabotér hver gren, ikke den du
lige skrev.

### Et håndskrevet mønster er selv et måleinstrument

Afsnittet ovenfor handler om at bruge det forkerte værktøj. Dette handler om at
bruge det rigtige værktøj med et filter, ingen har efterprøvet — og det er
sværere at se, fordi svaret ser ud som en måling.

**Tre uafhængige tilfælde den 02-10-2026, på tre platforme, i hver sin retning:**

| hvem | mønsteret | hvad det gav | hvad der var sandt |
|---|---|---|---|
| Koordinator | håndskrevet liste over danske ord | 43 danske API-stier | 45 — listen manglede `adgangskode` |
| Android | fem "delt op"-poster i træk læst som mønsteret | "de fleste" | 5 af 108 |
| Android | `[Aa]lle` i en tekstvagt | ét sted havde teksten | to — mønsteret så ikke `ALLE` med versaler |

Den første er den mest lærerige, fordi **beviset stod i koordinatorens eget
output.** Målingen printede en liste med overskriften "rene engelske", og
`/api/account/adgangskode` stod i den. Filteret var forkert, og resultatet viste
det — ingen læste det.

Den tredje viser, hvad fejlen koster: testen var **rød med rigtig kode.** Den
nærliggende reaktion er at rette koden, og det havde været den forkerte
rettelse.

**Reglen har to halvdele, og den anden er den, der glemmes:**

- **Et mønster i en stikprøve er en hypotese, ikke en måling.** Ser du fem af
  samme slags i træk, har du set fem — ikke "de fleste". Kør det på alt, før du
  siger hvor stort det er.
- **Efterprøv filteret, ikke kun resultatet.** Et `grep`, en ordliste, et regex,
  en `SPRING`-liste: hver af dem er et instrument. Mål noget, du VED skal fanges,
  og se at det bliver fanget. Et filter, der ikke rammer, giver samme svar som
  et felt, der er tomt.

**Og et instrument har flere led. "Jeg rettede mønsteret" er ikke det samme som
"mønsteret er rigtigt nu".**

Android rettede samme vagt tre gange den 02-10-2026, og **hver gang var den grøn
bagefter, mens kaldesteder stadig var forkerte:**

```
literalen kunne ikke læses til ende     mønsteret stoppede ved en parentes
parametrene kunne ikke genkendes        ${kodet(x)} blev ikke set som $x
kun literalens BEGYNDELSE blev set      et fragment har ingen skråstreg
```

Det tredje lag slap otte stier igennem. `/live/` sættes på ét sted, så koden
skriver `live("PUT", "halvleg", …)` — fragmentet står alene i literalen, og en
søgning efter stier, der **starter med `/`**, kan ikke se det. De otte ville have
svaret 200 lige indtil aliasserne blev fjernet.

**Den eneste måde at kende forskellen er at køre mønsteret mod et tilfælde, man
VED skal fanges — efter hver rettelse.** Ikke kun efter den første.

Koordinatorens egen verifikation havde samme blinde vinkel: den søgte efter
serverens 57 **hele** stier, og `"halvleg"` alene er ikke `/api/live/halvleg`. Det
var ikke et sjusk — at måle mod serverens egen tabel ER den rigtige kilde. **Fejlen
var at antage, at en sti i koden står som én streng.**

**En vagt med falske positiver bliver slået fra, og det er værre end ingen vagt.**
Androids første forsøg på at dække fragmenterne fældede på seks ting, der alle var
rigtige: `"deltager"`, `"laas"` og `"kamphaendelser"` er JSON-nøgler i en fixtur og
SKAL være danske — en nøgle kan ikke få et alias — og `"opstilling"` bruges tre
steder som en anden slags værdi. Et bart ord er for almindeligt at fælde på.

Rettelsen var at matche **andet argument til `live(`**, altså den ene form, hvor et
fragment bliver en sti, og at generere snapshottet af serverens egne stier under
`/live/`. Det er forskellen på at rette otte tilfælde og at lukke en klasse.

### Et anker, der er noget arbejdet flytter, holder kun til naeste gang

Natten 05/06-10-2026 fandt de tre sessioner **seks kontroller, der var ankret til
noget, omdoebningen selv flyttede.** Ingen af dem var roede foer de blev kigget
paa, og fem af de seks kunne ingen compiler se.

```
et klassenavn      en selvkontrol slog klassen op VED NAVN
en JSON-noegle     kontrakt["enhed"] — en dansk noegle i en delt fil
et ANTAL           kontrakt["regler"].size == 6 — antal PROSA-saetninger
en linjes TEKST    en assert, der ikke kunne skelne to ENS erklaeringer
en identifikator   en frossen tekst med ${KontoRegler.X} i interpolationen
en POSITION        et nyt parameter indsat som nummer TO
```

**Den sidste er den eneste, en compiler burde kunne se — og den kan den netop
ikke, naar typerne er kompatible:**

```kotlin
Periode("1. halvleg", "11:00", "11:35")   ->  label + labelKey + start
```

Alle tre er `String`. Det oversatte, og testen meldte `1. halvleg: 11:35––`.

> **Et nyt parameter hoerer SIDST.** Et positionelt kaldested er ankret til
> raekkefoelgen, og compileren kan ikke se, at betydningen skred.

**Maalt paa tvaers:** Swift og Kotlins navngivne argumenter beskytter kaldestedet
(`Periode(noegle:, label:)`), og en Python-`dict` med noegler har ingen
positionsfaelde. **Men det var en maaling, ikke en antagelse** — Android
formodede det om iOS og kaldte det selv en hypotese, fordi deres forrige
generalisering ikke holdt.

### Hvad man goer i stedet

```
ANKRE TIL   det der goer stedet entydigt — en datanoegle testen allerede
            afhaenger af, en struktur, en invariant
ALDRIG TIL  et navn · et antal · en linjes tekst · en position · prosa
```

### PRAECISERING 03:20: et ANTAL som skralde-taerskel er ikke det samme som en assertion

**Afsnittet ovenfor siger "ALDRIG [ankre] til et antal". Android indvendte mod
det, og de har ret** — reglen som den stod ville forbyde deres egen rigtige
loesning.

**Forskellen er, hvad antallet GOER:**

```
FORBUDT    assertEquals(6, kontrakt["regler"].size)
           en ASSERTION paa et antal, der vokser LOVLIGT. Tilfoejer nogen en
           syvende regel, bliver testen roed paa noget rigtigt — og testen
           handlede aldrig om reglerne

TILLADT    en SKRALDE: (fil, navn, antal) som taerskel
           antallet ER kontrollens mekanisme, ikke dens paastand. Den fejler
           kun, naar tallet VOKSER, og den flytter sig ikke med en linje
```

Androids grundlinje skal kunne rumme, at `"Her stod et `aktivtKort`-opslag"` er
**rigtig prosa om noget fjernet.** Forskellen paa det og en doed reference er
**TEMPUS**, og det kan et moenster ikke se. Saa taersklen er den eneste mekanisme,
der virker — og de proevede de to alternativer:

```
linjenummer     taendte vagten hver gang en linje blev indsat OVENFOR
navnet alene    for loest: seks datids-omtaler af samme navn daekkede en NY
                nutidsreference til det
```

**Og prisen staar i vagtens header**, hvor den kan ses: en ny datids-omtale goer
vagten roed paa noget rigtigt. **Det er forskellen paa en tolerance, der er
valgt, og en der har sneget sig ind.**

> Et antal er et daarligt ANKER og et brugbart MAALEBAAND. Spoerg, om kontrollen
> paastaar noget om tallet, eller bruger tallet til at opdage en aendring.

**Og sabotagen skal gaa i BEGGE retninger.** Androids `#86` er forlaegget:

```
A  skift noeglens navn + tilfoej en regel   GROEN   testen maa ikke roeres
B  skift den baerende datanoegle            ROED    enheden ER aendret
C  skift vaerdiens FORM                     ROED    konverteringen mangler
```

B og C beviser, at testen maaler noget. **A beviser, at den ikke maaler det
forkerte** — og uden A er en rettelse bare en anden maade at vaere foelsom paa.

### Og den dyre halvdel: en vagt, der kaemper mod arbejdet, bliver slaaet fra

Tre frosne tekster kom tilbage som NYE, fordi en identifikator inde i en
interpolation var omdoebt — samme saetning, andet symbol. En vagt, der fejler ved
hver omdoebnings-commit, bliver slaaet fra, og saa daekker den ingenting.

Rettelsen er at **normalisere det, der ikke er indholdet**: interpolationen til
`#`, saa teksten maales og ikke symbolet. Det er grunden til, at iOS' grundlinje
overlevede deres egen omdoebning, mens Androids ikke gjorde.

### En anden platforms symbol ser ud som dit eget — og kun prosaen skiller dem

Android maalte det 06-10-2026, og de havde selv sendt advarslen faa timer foer:

> iOS' navne ser formmaessigt ud praecis som vores, og kun ordet "iOS'" ved
> siden af skiller dem.

**De skrev saetningen, og lavede saa fejlen alligevel** — i et bulk-swap af 79
foraeldede prosa-referencer, hvor ingen enkelt linje blev laest. Syv linjer af
formen **"Port af iOS' `X`"** blev omdoebt, og `X` var Swift-symboler.

Udfaldet var todelt, og den anden halvdel er et fund for begge sider:

```
STADIG DANSK HOS IOS — rullet tilbage
  enum Aktivitet · enum AktivitetKategori · enum KampeLogik
  enum TidslinjeTekst · struct KlubFarver · actor LokalDB

ANDROIDS PROSA VAR FORAELDET OM IOS — swappet var tilfaeldigvis RIGTIGT
  enum TeamCard        Android skrev stadig `HoldKort`
  enum TeamSelection   Android skrev stadig `HoldValg`
```

**Dokumentationen af, hvad man er en port AF, raadner uden at nogen kan se det.**
Den beskriver en anden platforms kode, saa den eneste maade at validere den er at
maale det andet trae — og det goer ingen som rutine. Alle syv er efterproevet i
iOS' kilde (`9068978`).

**Formen er en REGEL i vagten, ikke en grundlinjepost:** en backtick umiddelbart
efter `iOS'` er deres og doemmes ikke. Uden den melder vagten hver eneste
"Port af iOS' X" som doed kode — og **en vagt, der kaemper mod rigtig prosa,
bliver slaaet fra**, af samme grund som 37-noegle-vagten i afsnittet ovenfor.

**Har du "Port af Android' X"-linjer, gaelder det samme for dig.** Maal dem mod
det andet traee, ikke mod dit eget.

### En liste fortæller hvilke værdier der findes, aldrig hvad de betyder i din kode

Androids formulering, 04-10-2026, efter at have fået en ordforrådsliste fra
koordinatoren og alligevel næsten brudt en regel:

> En liste fortæller hvilke værdier der findes, aldrig hvad de betyder i din
> kode. **Det er den halvdel, ingen afsender kan levere.**

`cup_match` / `"Pokalkamp"` stod på listen. Det, der ikke stod — og ikke kunne
stå — var at værdien bærer en regel: kun stævne- og pokalkampe kan gå i forlænget
spilletid, og reglen bor i klientens egen kode (`DatainputLogik:111`).

**Byg en enum på serverens katalog, ikke på en liste i en besked.** Og læs din
egen kodes brug af værdien ved siden af kataloget — de to halvdele er begge
nødvendige, og ingen kanal leverer dem samlet.

**Og listen var selv ufuldstændig.** Koordinatorens optælling hvilede på et
håndskrevet værdisæt med 12 af kataloget 22 værdier. Målt i samme træ, samme
kommando, kun værdisættet forskelligt: **925 mod 1177 forekomster.** De
udeladte bar 252, heraf `"Træning"` alene 90 — og `"Træning"` var en af de tre
fælder, koordinatoren selv havde advaret om. **Advarslen om hullet kom fra et
instrument, der havde det.**

Samme form som "Et håndskrevet mønster er selv et måleinstrument" nedenfor, men
med den tilføjelse, at et værdisæt skrevet af hukommelsen er en denylist over
det, man kom i tanke om.

### Sprogreglen, fuldt afgjort 03-10-2026

Morten gennemgik hvert element enkeltvis. **Dette er den fulde beslutning** og
afløser den kortere form fra 01-10 ("nøgler engelske, prosa dansk"), som ikke
rakte til de svære tilfælde.

## Engelsk — alt bag det, brugeren ser

| element | beslutning |
|---|---|
| tabel- og kolonnenavne | engelsk *(færdigt)* |
| API-stier, serveret | engelsk *(færdigt)* |
| **API-stier i kildekoden** | **engelsk** — omdøb præfikset, fjern posten fra omskrivningskortet |
| **JSON-feltnavne** | **engelsk** — alle 47 omdøbes |
| **sti-parametre** | **engelsk** — alle 7 |
| **datavaerdier** (`'Udtaget'`) | **engelsk nøgle + `display_da`** |
| **variabel- og funktionsnavne** | **engelsk** — men LÆG EN PLAN FØRST |
| **filnavne** | **engelsk** — sammen med kodenavnene |
| **testnavne** | **engelsk** |
| **logbeskeder** | **engelsk** |
| **kodekommentarer** | **engelsk FREMOVER, dansk bevares** |

**Kommentarerne er den vigtigste nuance:** nye skrives på engelsk, gamle røres
ikke. En automatisk oversættelse af de eksisterende ville koste præcis det, der
har størst værdi — tre af fem fejl fundet 03-10 blev fundet gennem en dansk
kommentar, der forklarede HVORFOR.

**Og kodenavnene kræver en plan, ikke en søg-og-erstat.** Morten sagde det
eksplicit. Fire gange på én dag tog et for bredt mønster noget med, det ikke
skulle — og dette er det bredeste mønster, der findes i projektet.

## Dansk — arbejdssproget

| element | beslutning |
|---|---|
| kort, issues og commit-beskeder | **dansk** — med et kort til fremtiden om at overveje engelsk |
| admin-siden | **dansk for nu** — med et kort til fremtiden om locale |

**De to er ikke "nej", de er "ikke nu".** Begge har et kort, så beslutningen kan
tages igen, når der er en grund.

## Locale — alt brugeren ser

| element | beslutning |
|---|---|
| **app-tekster** | **locale-katalog bygges** — `.xcstrings` på iOS, `strings.xml` på Android |
| **serverens fejlbeskeder** | **serveren sender en NØGLE**, appen oversætter |
| **push-notifikationer** | **`loc-key` + `loc-args`** — telefonens system slår op i appens eget katalog |
| **e-mails** | **venter** — tages med locale-arbejdet, kræver et sprogfelt på familien først |

**Push er værd at forstå, fordi det ikke er åbenlyst.** En push vises af
SYSTEMET, før appen kører — så appen kan ikke nå at oversætte den. APNs og FCM
kan til gengæld selv slå nøglen op i appens katalog. **Det er derfor samme
katalog som resten af appen**, og et nyt sprog dækker også push uden ekstra
arbejde.

## Hvad reglen er til for

Målet er, at **et nyt sprog bliver en fil og ikke en kodedag.** Alt bag
grænsefladen er engelsk, så enhver kan arbejde i det; alt foran er en nøgle, så
enhver kan oversætte det.

**Og `display_da`-mønstret er forudsætningen for begge dele.** Så længe
`sessions.status` indeholder `'Udtaget'` som data, er en tekstændring en
datamigrering og et brud på begge klienter — og et nyt sprog er umuligt.

### Et nyt felt i et delt dokument skal kendes af BEGGE klienter, før nogen skriver det

Mortens beslutning 03-10-2026 (`PlayerData_Backend#114`), og den gælder fra nu.

**Rækkefølgen er:** aftal feltet → begge platforme kan læse OG skrive det →
**derefter** begynder nogen at skrive det. Ikke omvendt.

## Hvorfor reglen findes

`kamp_opstilling` og `kamp_kamphaendelser` skrives **hele** tilbage af begge
klienter. En klient afkoder dokumentet til sin egen model og skriver modellen —
så **et felt, modellen ikke kender, forsvinder ved næste skrivning.** Ikke for
den, der skrev, men for alle andre i samme kamp.

Det skete **tre gange på én dag**: `sek`, `periode` og `kilde` blev skrevet af
Android, og forsvandt da en iOS-klient gemte kampen. Ingen opdagede det, mens det
skete — de tre felter fandtes kun i det dokument, der blev overskrevet, og det
var tabt for den kamp.

**Og det er den eneste fejlklasse i hele dette dokument, som ingen vagt kan
fange.** En rundturs-test beviser, at de felter, modellen KENDER, overlever. Den
kan per definition ikke sige noget om et felt, modellen ikke kender.

## Hvad reglen betyder i praksis

Tilføjer du et felt til et delt dokument:

1. **sig det til den anden platform, før du skriver det** — ikke efter
2. **vent til de kan læse og skrive det**, ikke bare læse det
3. først derefter må feltet begynde at optræde i dokumenter på serveren

Det føles langsomt, og det er pointen: alternativet er et felt, der ser ud til at
virke hos dig og forsvinder hos alle andre, uden en fejl nogen steder.

## Reglen er midlertidig i form, ikke i indhold

Beslutningen omfatter også, at begge klienter **før App Store** skal bære ukendte
nøgler uændret igennem — så et felt ikke længere kan gå tabt, selvom nogen
glemmer reglen.

Men indtil den kode findes, er reglen den eneste beskyttelse. **Og en regel, der
kun virker når den huskes, er netop det, der svigtede de tre gange.**

### Et delt felt kan ikke bære svaret på "lykkedes opslaget?"

Androids egen advarsel om sig selv, 03-10-2026, og den er værd at have, fordi
den beskriver en rettelse, der BESTOD med fejlen i.

De skulle gøre et fejlet opslag synligt og skrev beskeden i et eksisterende,
delt `status`-felt. Testen krævede, at `status` ikke var tom. **Den bestod** —
fordi `anvend(doc)` allerede havde lagt *"Ny opstilling — ikke gemt endnu"* der.

De fandt det kun ved at **rulle rettelsen helt tilbage** og se, at kun én af tre
tests blev rød.

> **Et delt strengfelt kan ikke bære svaret på "lykkedes opslaget?", fordi enhver
> anden besked i det ser ud som et ja.**

Truppen fik sit eget felt i stedet.

**Den generelle form:** et felt, flere ting skriver til, kan ikke bruges som
bevis for, at én af dem skrev. Og en test, der kun spørger "er der noget her",
måler kanalen og ikke afsenderen.

**Og kontrollen, der fandt den, er den brugbare del:** rul din egen rettelse
tilbage, og tæl hvor mange tests der bliver røde. Bliver færre røde end du
forventede, måler de resterende ikke det, du tror.

### Et spørgsmål, koden kunne have besvaret, er også spild

Koordinatorens fejl samme dag, og den er værd at have ved siden af resten af
dette dokument, som handler om at måle frem for at slutte.

Et kort bad Android overveje, om et fortryd-vindue skulle være længere end for
mål — *"det er et designvalg, og hvis I mener det, så spørg frem for at vælge."*

Android målte i stedet: iOS sover 6 sekunder, Androids `FORTRYD_SEKUNDER` er 6,
og kortets eget krav var, at de to apper skal opføre sig ens. **Koden havde
allerede svaret.**

Reglen "spørg frem for at vælge" er rigtig, når svaret kræver en præference. Den
er forkert, når svaret står i kildekoden — og så koster den en tur frem og
tilbage plus en beslutning, nogen skal træffe uden at have brug for det.

**Mål FØR du spørger.** Ikke bare før du konkluderer.

### En testsuite kan ikke finde en fejl, der sidder i forfatterens forståelse

Det klareste eksempel i projektet, og Android meldte det selv 03-10-2026.

De havde skrevet i en kommentar, at to måder at regne en udvisnings resttid på
var "det samme tal": udvisningens LÆNGDE minus den tid, der var gået siden
kortet, mod et SLUTtidspunkt på en absolut skala. **De fjorten håndskrevne tests
var alle enige med dem** — og vektorerne fra serveren fandt uenigheden ved første
kørsel:

```
slettet_foerste_gult_goer_andet_til_foerste   Android 600, serveren 1200
to_spillere_samtidig                          Android 600, serveren 1200
```

De to former ER enige, så længe kortet ligger før `nu`. Rettes et klokkeslæt frem,
eller tastes et kort ind på forskud, bunder "den tid, der er gået" i nul, og
resten bliver udvisningens fulde længde i stedet for det rigtige, større tal.

**Testene var ikke dårlige. De var skrevet ud fra samme antagelse som koden.** En
antagelse, der findes i begge, kan ikke falsificeres af nogen af dem — uanset hvor
mange tilfælde man tilføjer, for man vælger tilfældene ud fra den samme
forståelse.

Derfor er et EKSTERNT facit ikke en luksus oven på en god testsuite. Det er den
eneste slags kontrol, der kan finde denne fejl. En påstand om, at to former er
ækvivalente, hører i en vektor, ikke i en kommentar.

### Bygger testen sit input med koden under test, måler den den mod sig selv

Samme dag, samme session, og det er den anden halvdel af ovenstående.

Vektorerne angiver `nu` som `nu_spilletid_sek`. **Android lod TESTEN oversætte
det til et vægur-tidspunkt, ikke produktionskoden.** Begrundelsen er den vigtige:
havde de brugt appens egen omregning til at bygge inputtet, ville testen have målt
omregningen mod sig selv, og en fejl i den var gået lige igennem alle 11
tilfælde — grønt.

Omregningen har sin egen lås i en separat test med sine egne 162 vektorer.

**Spørg ved enhver vektor- eller gyldenfil-test: hvem bygger inputtet?** Er svaret
"den kode, jeg er ved at bevise", beviser testen kun, at koden er konsistent med
sig selv.

### Et værn kan flytte den umålte flade frem for at fjerne den

Androids egen formulering, samme dag, efter at en ændring havde ophævet noget,
de selv havde fremhævet som en styrke:

> *"Jeg havde beskyttet mig mod at måle noget mod sig selv og derved lukket
> øjnene for, at der slet ikke blev målt."*

De havde med vilje ladet TESTEN oversætte et tidspunkt frem for
produktionskoden, så testen ikke målte omregningen mod sig selv. Det var rigtigt
tænkt om testen. Men vektoren udstillede et **færdigkonverteret** tal, og så kunne
en klient springe konverteringen over netop dér — og være utestet præcis på det
punkt, værnet skulle beskytte.

**Et værn, man selv har valgt og kan begrunde, er sværere at mistænke end en
fejl, man ikke kan forklare.** Det er derfor denne slags holder længe: hver gang
man ser på den, finder man sin egen gode grund.

Spørg ved et hvilket som helst værn: hvad blev der IKKE målt, fordi jeg satte
det op? Svaret er ikke nødvendigvis "ingenting".

### En test kan vogte over fejlen

Den 02-10-2026 skulle Android gendanne en spillers plads, når et fejlvalgt kort
blev rettet. De fandt ikke bare koden, der tømte pladsen — de fandt **en
bestående test, der aktivt fastholdt det:**

```
"Pladsen kommer IKKE tilbage"
```

Og en kommentar ved siden af, der sagde det samme: *"spilleren forbliver væk fra
sin gamle plads — den er fjernet, ikke gemt."*

**Adfærden var engang et valg.** Nogen skrev testen for at beskytte den mod at
blive lavet om ved et uheld. Og så blev valget forkert, uden at testen vidste det.

En test beskytter mod **utilsigtet** ændring. Den kan ikke kende forskel på en
regression og en rettelse — og derfor bliver den, den dag adfærden skal ændres,
det stærkeste argument for ikke at ændre den.

**Kendetegnet:** du finder en test, der med rene ord siger det modsatte af det,
du er ved at bygge. Det er ikke et bevis for, at du tager fejl. **Det er et spor
til, hvem der besluttede det, og hvornår** — læs testens navn og dens commit,
ikke kun dens påstand.

Rettelsen er ikke at slette testen. Den er at **vende den om og skrive hvorfor**,
så den næste kan se, at adfærden blev ændret bevidst og ikke tabt.

Samme form som det frosne tal i `FormRegelAkserTest` (`60/52/22/39`), der blev
rødt af én lovlig tilføjelse: en kontrol, der beskytter mod ændring, står i vejen
den dag ændringen er rigtig.

### Et ord i en loglinje er ikke en hændelse

Koordinatoren søgte 05-10-2026 efter årsagen til et maskinnedbrud med:

```
journalctl -b -1 -k | grep -icE 'out of memory|oom-kill|panic'   ->  2
```

**To træf. Begge var `drm panic`** — grafikdriverens panik-*håndterer*, der
registrerer sig ved hver opstart. Ikke en kernepanik; det modsatte, nemlig
beviset på at håndteringen virker.

Samme kørsel gav "6 nedlukningsspor" i det boot, der døde. De seks lå spredt
over femten dage: `initrd-cleanup` ved opstart, `geoclue` der lukkede ned efter
60 sekunder, `gnome-shell`, `NetworkManager` der fangede SIGTERM. **Ingen af dem
havde med nedbruddet at gøre.** Tallet 6 lignede et fund.

Fejlen er, at **et ord valgt for at matche en hændelse også matcher hvert navn,
hver tilstandsbeskrivelse og hver rutinebesked, der indeholder ordet.** Og en
optælling skjuler det, fordi den ikke viser teksten.

```
grep -c  'panic'    ->  2      ser ud som to panikker
grep     'panic'    ->  drm panic, drm panic
```

**Tæl aldrig i en log. Læs linjerne.** Og hvis der er for mange at læse, så
afgræns med tid frem for at stole på antallet — `--since` er en måling, et
nøgleord er et gæt.

Det er samme form som `pgrep -f`, der matcher sin egen kommandolinje: et mønster
bredt nok til at finde det, man leder efter, er også bredt nok til at finde noget
andet, der ligner.

### En måling der finder for MEGET koster andres tid, ikke din egen

Android var 05-10-2026 på vej til at melde, at en tømt vektorfil ville bestå
tavst — et fund der ville have ramt alle fem delte vektortests på én gang.
Grundlaget var et grep efter `isNotEmpty`/`size`, der gav **nul træf i fire af
de fem.**

**De tømte `kortstatus-fra-python.json` for at bevise det først. Testen blev
rød.**

Vagten heder `vektoren_har_de_kanttilfaelde_den_skal_have` og påstår på
**NAVNE**, ikke på længder. Mønstret ledte efter den forkerte form.

> Et grep efter en implementeringsform finder ikke en vagt, der er skrevet i en
> anden. **Fraværet af mit mønster var ikke fraværet af en kontrol.**

Det er samme familie som `pgrep -f`, `cat` på en låsemappe og en denylist af
ord man kom i tanke om. **Men retningen er ny**, og den er værd at skille ud:

```
en maaling der finder for LIDT    -> en overset fejl.  Din omkostning
en maaling der finder for MEGET   -> en falsk alarm.   ANDRES omkostning
```

En overset fejl koster dig et gennemsyn mere. **En falsk alarm koster
modtageren at læse, undersøge og formulere modbeviset** — og den koster det hos
nogen, der ikke har konteksten til at se, at alarmen var forkert.

Det er derfor et forkert kort er dyrere end intet kort, og det er samme regning
her: et fund meldt opad er en regning, du sender til en anden.

**Prisen for at undgå det var én sabotage.** Tøm kilden, se vagten blive rød, og
meld først derefter. Et fund om en MANGLENDE kontrol skal sabotagetestes lige så
hårdt som en kontrol man selv bygger — faktisk hårdere, for her er påstanden, at
der ikke er noget at finde.

### Et mønster, der matcher på delstreng, matcher også den, der leder

`pgrep -f`, `pkill -f` og enhver anden søgning i en **kommandolinje** rammer også
den proces, der har mønsteret stående i sine egne argumenter.

**Den 02-10-2026 gjorde det en beskyttelse til sin egen modsætning.**
`.scripts/byg.sh` markerede Gradle-dæmonen som foretrukket OOM-offer, med en
rigtig hensigt: kernen skulle tage buildet frem for noget vigtigere. Men den fandt
dæmonen med `pgrep -f GradleDaemon`, og det matchede en session-shell, der blot
havde ordet i sine argumenter:

```
pid=444536  exe=bash  rss=4 MB  adj=900
    scope=app-com.anthropic.Claude-436163.scope
```

**Scriptet pegede kernen mod samtalen.** På en delt maskine med tre sessioner
skriver nogen før eller senere ordet i en kommando — og så er beskyttelsen blevet
den farligste proces på maskinen. Appen blev dræbt to gange den dag, og alle tre
lokale sessioner med den.

Og Android faldt i den **igen, med rettelsen åben foran sig:** `pkill -f
"playerdata-byg"` i deres egen test dræbte deres egen shell.

**Match på noget, en forbipasserende ikke kan komme til at skrive:**

```
/proc/<pid>/exe      hvad processen ER (java, ikke "nævner java")
/proc/<pid>/cgroup   hvor den hører til (vores eget scope)
```

Rettelsen krævede begge: `exe` skal være `java`, OG cgroup'en skal matche vores
eget scope. Ét af dem alene ville stadig kunne rammes.

**Og hvis beskyttelsen ikke kan etableres, så sig det højt.** Samme commit
skriver en advarsel, hvis `systemd-run --user --scope` ikke virker, frem for at
falde tavst tilbage: *"Et stille fald tilbage ville betyde, at næste OOM igen tog
tre sessioner, uden at nogen vidste hvorfor beskyttelsen ikke virkede."*

Bemærk desuden forbeholdet i deres eget bevis, for det er det, der gør det
brugbart: det fremkaldte OOM var `MemoryMax`-begrænset, mens de faktiske
hændelser var globale. **Indeslutningen var bevist — udløseren var ikke.**

### Læs tilstanden FØR ændringen, ikke efter

Android meldte de otte stier til koordinatoren som "allerede engelske". De havde
læst filen **efter** deres egen redigering og konkluderet om tilstanden **før**.
Deres egen diff viste, at de selv havde lavet dem.

Det er samme form som en rapport, der bruger en mellemfil fra `/tmp` og stempler
den med dagens dato: **to tidspunkter blandes, og resultatet ser sammenhængende ud.**

`git diff` og `git stash` er svaret, ikke en ekstra læsning af filen. Skal du vide,
hvordan noget så ud før du rørte det, så spørg versionsstyringen — ikke disken.

Og når du videregiver et tal, der kom af et håndskrevet filter, så sig at det
gjorde. Modtageren kan ikke se forskel på "45 målt" og "45 fanget af min liste",
og kun det første tåler at blive bygget videre på.

### `origin/main` er en lokal kopi, ikke fjernlageret

`git rev-list --count HEAD..origin/main` **spørger ikke GitHub.** `origin/main` er en fjern-sporende ref, der ligger på disken og kun opdateres af `git fetch`. Uden en fetch sammenligner man med et øjebliksbillede, der kan være dage gammelt — og svaret "0 commits bagud" betyder da kun *"0 bagud i forhold til det, jeg sidst hentede"*.

Målt 2026-09-30, da koordinatoren skulle afgøre, om iOS' app-link-ændring lå i git:

```
før  git fetch:   origin/main = 8ea58ed   →  "0 commits bagud"
efter git fetch:  origin/main = c8129ed   →  6 commits bagud
```

Konklusionen blev, at linjen `applinks:playerdata-stage.raunchristiansen.dk` **kun fandtes lokalt på Mac'en og var i fare**. Den var i virkeligheden committet og pushet halvanden time før. iOS fandt fejlen ved at slå det op i deres eget træ.

Kommandoen er farlig, fordi den *ser ud* som om den spørger fjernlageret. Den gør det ikke. Det samme gælder `git log origin/main`, `git show origin/main:fil` og `git status`' "up to date with origin/main".

**Kør `git fetch` først, hver gang svaret skal sige noget om, hvad der er på GitHub.** Og siger et andet menneske eller en anden session, at noget ER pushet, så fetch før du modsiger dem — de kan se deres eget træ, du kan kun se din kopi af det.

### En rigtig begrundelse med en ufuldstaendig udfoerelse

En ny variant, maalt 02-10-2026, og den er svaerere at fange end de oevrige i dette
afsnit: **ikke en forkert slutning fra en rigtig maaling, men en rigtig begrundelse, hvor
rettelsen kun daekkede halvdelen.**

Android skrev i en commit-besked, at korrektionen ogsaa skulle ske paa gemmestien,
*"ellers ville skaermen vise Simpel, mens en detaljeret tidslinje blev gemt."*
**Begrundelsen var rigtig.** Rettelsen ramte ét af tre steder, der stiller samme
spoergsmaal — og indfoerte dermed praecis det datatab, den skulle forhindre, spejlvendt:

```
foer   skaerm og gemmesti laeste begge den RAA vaerdi   → enige, ingen fejl
efter  gemmestien rettet, skaermen ikke                 → skaermen viser et tidsrum,
                                                          gemmefunktionen laeser de
                                                          tomme talfelter
```

De indtastede minutter forsvandt i stilhed. **Fejlen levede 23 minutter** og naaede aldrig
en bruger, fordi nogen spurgte til ét kaldested, der ikke var maalt.

iOS havde den samme fejl, spejlvendt, i deres egen kodebase samme aften.

### Hvorfor den er svaer at fange

En forkert slutning kan fanges ved at laese paastanden igen. **En ufuldstaendig udfoerelse
kan ikke** — begrundelsen staar der, den er rigtig, og den bekraefter sig selv, naar man
laeser den.

> *"Den fanges kun ved at maale KONSEKVENSEN, ikke ved at laese hensigten — heller ikke
> sin egen."*

Og den praktiske regel, der foelger: **naar du retter ét sted, saa spoerg hvor mange
steder der stiller samme spoergsmaal.** Begge platforme loeste det til sidst paa samme
maade — ikke ved at rette hvert sted, men ved at lave ét svar, som alle kaldesteder
bruger.

Baegge gange var det et andet menneskes spoergsmaal, der fandt det. **Ingen af dem fandt
sit eget.**

### Et datapunkt, du selv har fremstillet, hører ikke i opgørelsen

Koordinatoren opgjorde 05-10-2026 otte OOM-dræbte builds og satte dem op mod
tidspunktet, hvor hukommelsesloftet blev sat:

```
syv draebt FOER loftet   ·   eet draebt EFTER
```

Android-sessionen kunne gøre rede for det ene. Det var **deres egen
`#69`-måling**: `assembleDebug + testDebugUnitTest + compileDebugAndroidTestKotlin`
med `--no-build-cache` på et nulstillet `app/build`, bygget med vilje så dyr som
muligt for at finde ud af, hvad en unavngiven proces var.

Deres formulering:

> Et datapunkt, man selv har fremstillet for at presse systemet, må ikke indgå i
> opgørelsen over, hvor ofte systemet presses af sig selv.

**Den hører hverken i "før" eller "efter" — den hører udenfor.** Og det er værd
at bemærke, at den ikke er et modeksempel mod loftet; den er konstrueret til at
ramme det.

Det farlige er, at et fremstillet datapunkt **ser ud som de andre i loggen.**
Kernen skriver samme linje, uanset om processen døde under almindeligt arbejde
eller under en bevidst belastningsprøve. Opgørelsen kan derfor ikke skelne dem,
og kun den, der kørte prøven, ved det.

**Så sig det, når du kører en belastningsprøve** — og spørg, før du opgør andres
tal. Forskellen mellem "syv mod én" og "syv mod nul uprovokerede" er ikke
kosmetisk: den første inviterer til at tro, at indgrebet ikke virkede.

### Serverens egne data er ikke et testgrundlag — de er ét punkt i rummet

Android 01-10-2026, ved at bygge formularens regelmotor (`#17`):

> *"Serverens frø har 51 regler, og NUL af dem har både en specifik type og en
> specifik status. Præcedensens øverste niveau rammes altså aldrig af rigtige data —
> en implementering, der helt udelod niveau 1, ville bestå enhver test bygget på
> serverens egne rækker."*

En test, der kun fodres med det, databasen tilfældigvis indeholder i dag, prøver ikke
koden — den prøver **skæringen mellem koden og de nuværende data**. Alt, hvad dataene
ikke rammer, er utestet og ser testet ud.

Og den fejler først, når nogen opretter den første række af den slags, der mangler.
Dét sker i produktion, længe efter at koden blev meldt færdig.

**To tests, to formål, ikke sammenblandet:**

```
praecedens/logik     OPDIGTEDE raekker, stillet op mod hinanden for SAMME felt,
                     fjernet ét ad gangen for at se det naeste overtage
kontrakt             serverens FAKTISKE raekker, facit regnet uafhaengigt foerst
```

Android beviste desuden, at vagten kunne fejle: de fjernede `type to status` fra
præcedenslisten, og tre tests blev røde. **Netop den fejl kunne serverens egne data
ikke have afsløret.**

Reglen i kort form: *en betingelse, der er sand i alle de tilfælde, du faktisk bruger,
er ikke en betingelse.* Spørg hvilke kombinationer dine data **ikke** indeholder — og
skriv testen for dem først.

### En enhedstest ser ikke nødvendigvis det, appen ser

En testmål, der ikke er "hostet" i selve app-målet (iOS: intet `TEST_HOST`/`TEST_TARGET_NAME` mod app-targettet i projektfilen; Android: en ren JVM-unit-testkørsel uden en Instrumented/Robolectric-kontekst), kører i en ANDEN proces end appen. Platformens "find min egen ressource/mit eget bundle"-opslag (iOS: `Bundle.main`, også `Bundle(for:)` og `Bundle.allBundles` — afprøvet alle tre, ingen af dem så app-bundlens ressourcer; Android: `Context`/`Resources` uden en instrumenteret kontekst) peger derfor på TESTLØBEREN, ikke på appen — en ressource, der ligger i app-bundlen (en JSON-fil, et billede, en Info.plist-nøgle), er simpelthen ikke der at finde, uanset hvilken opslagsmetode man prøver.

Fundet på iOS 2026-10-01 (#19): en `fatalError` i en `static let`, der skulle læse en bundlet reserve-JSON, crashede HELE testkørslen (ikke bare den ene test) — Xcodes "Restarting after unexpected exit, crash, or test timeout" gentog derefter hver test enkeltvis, og et efterfølgende diagnostik-timeout (fast ~600 sekunder) fik en normal 30-60 sekunders kørsel til at ligne en hængende maskine. Rettelsen var IKKE at ændre selve ressource-opslaget (det er korrekt — for den RIGTIGE app, hvor testmålet ikke kører), men at lade testen læse den samme fil direkte fra disken (en sti relativ til testfilens egen `#filePath`) i stedet for at gå gennem Bundle-opslaget overhovedet.

**Konsekvensen, der er værd at huske:** en test, der aldrig rører koden, der læser fra bundlen/konteksten, beviser intet om DEN kode — kun om den rene logik ved siden af. Og en kodesti, der virker fint lokalt, fordi testen aldrig når den linje, kan stadig indeholde en rigtig fejl, som kun en kørende app (eller en hostet test) ville finde — se næste afsnit.

### Det eneste, ingen enhedstest roerer, er SAMMENSAETNINGEN

**Afsnittet ovenfor handler om en test, der ikke KOERER koden. Dette handler om,
at alle tests koerer koden, og appen stadig ikke kan aabnes.**

Androids maaling 06-10-2026, da `Android#83`s enhedsverifikation blev lukket:

```
04-10   CrashlyticsSporing gjorde appen UAABNELIG
        984 unit-tests var GROENNE
06-10   1115 groenne tests kan stadig ikke sige noget om det
```

> Det eneste, ingen unit-test roerer, er sammensaetningen. **Appen blev startet.**

**Wiring, afhaengighedsrækkefoelge og opstart er ikke en funktion, man kan
kalde.** De findes kun, naar alle delene er sat sammen — og en groen suite siger
intet om dem, hvor mange tests den end rummer.

### Hvad "sammensaetningen er maalt" betyder paa hver platform

```
Android · iOS   appen er STARTET paa en enhed eller simulator, og den
                forventede sideeffekt er maalt PAA DISKEN eller i brugerfladen
Backend         den koerende container har svaret paa ruten — ikke at
                funktionen er importeret, og ikke at testen bestod
```

Begge halvdele skal maales. Androids eget eksempel:

```
frisk start      appen koerer, stemplet SKREVET med den rigtige vaerdi
FORKERT stempel  cachen RYDDET ved naeste start, stemplet skrevet om,
                 og appen koerer stadig
```

**Den anden halvdel er den, der taeller.** En stempling uden en virkende rydning
ville lade en gammel identitet overleve et skemaskifte — **og den kunne bestaa
med ledningen klippet over.**

### Og en groen suite kan vaere en suite, der ikke koerte

Koordinatoren maalte 06-10 kl. 02:02 `#174`s to tests i sit eget miljoe:

```
Ran 2 tests in 0.000s
OK (skipped=2)      <- begge SPRUNGET OVER: "kraever fastapi"
```

**`OK (skipped=2)` er ikke groent. Det er "ikke koert"** — og sidste linje siger
`OK`. Var testfilen laengere, var skip-grunden faldet uden for en `tail`.

> Spoerg aldrig "bestod den". Spoerg "hvor mange KOERTE".


### Og en suite, der slet ikke BLIVER kaldt, raadner tavst

Afsnittet ovenfor handler om tests, der blev SPRUNGET OVER og meldte `OK`.
Dette handler om det naeste trin: en suite, der oversaettes, men som ingen
port nogensinde udfoerer.

Androids maaling 06-10-2026, foerste gang deres instrumenttests blev koert:

```
3 af 63 roede
  NatteSkaermeTest.theLittleExtra   "raekke_navn" "blokerende_hold" "stilling"
                                    modellen vil have division_name,
                                    blocking_teams, standings
  NatteSkaermeTest.myDetails        "navn" "antal_registreringer"
                                    modellen vil have username, session_count
```

Gaten OVERSAETTER `androidTest`; den koerer dem ikke. Emulator-jobbet kan ikke
koere, saa suiten koeres kun, naar en telefon er tilsluttet. **De to tests havde
vaeret roede siden noeglerne skiftede.** Deres formulering:

> Nyttelast i en test, der aldrig koeres, raadner tavst — og en omdoebning af
> noegler er praecis det, der faar den til at raadne.

**Og de spurgte de to andre platforme frem for at generalisere:** *"jeg har IKKE
maalt jeres traeer."* Koordinatoren maalte begge 06-10 kl. 05:00, og de tre svar
er forskellige — hvilket er grunden til, at spoergsmaalet var vaerd at stille:

```
Android   suiten koeres ikke i porten, og den BAERER nyttelast   -> raadnet
Backend   26 af 62 filer gated paa fastapi, men CI koerer dem,
          og alle 26 staar i modul-listen. NUL huller            -> intet raadnet
iOS       -only-testing:FodboldTrackerTests udelader hele
          FodboldTrackerUITests. Men nul JSON-noegler i maalet    -> kan ikke raadne
```

**iOS' tilfaelde er alligevel det mest alvorlige**, og ikke af den grund
Android ledte efter. Den ene test, porten udelader, er
`FodboldTrackerLaunchTests` — bygget 29-09 efter en iOS/Android-krydsafstemning,
fordi *"appen havde INTET testmaal, der beviser den rent faktisk kan STARTE"*,
og dens egen dokumentation henviser til **Androids crash** som grunden. Det er
sammensaetningen fra afsnittene ovenfor, og sikkerhedsnettet mod den haenger
ikke op. (`PlayerData_iOS#83`)

**Spoergsmaalet, der er billigt at stille paa enhver platform:** findes der en
testsuite, der kun oversaettes og ikke koeres — og baerer den nyttelast? Og
spoerg det paa HVER platform. Tre traeer gav tre forskellige svar her.

**Og en vagt mod formen er ikke altid svaret.** Android maalte en: JSON-noegler
i tests, som intet i `main/` laeser. Resultatet var **37 noegler i 123
forekomster**, og de fleste legitime (`helt_nyt_felt` er bevidst ukendt for en
`ignoreUnknownKeys`-test). De byggede den ikke, og skrev maalingen OG fravalget
ind i `ci.sh`, saa den naeste ikke maaler det forfra. Samme konklusion som
`kdoc_referencer`s 60-mod-19: **et instrument, der melder 37 hvor faa er aegte,
bliver slaaet fra.** Svaret er at koere suiten, ikke at bygge en proxy for den.

### En manglende decode-nøgle kan ligge skjult bag en anden fejl

Samme kort (#19): en JSON-decoder for et nyt svar manglede en `CodingKeys`-mapning for ét felt (serveren sendte `display_da`, Swift-typen havde kun den camelCase-navngivne property uden eksplicit nøgle) — en ægte fejl, der ville have kastet `DecodingError.keyNotFound` i den RIGTIGE app, første gang svaret blev afkodet. Den blev ikke fundet af nogen test, fordi testkørslen crashede (se afsnittet ovenfor) FØR afkodningslogikken nogensinde kørte — crashet maskerede fejlen fuldstændigt.

Rettelsen af bundle-crashet var derfor en FORUDSÆTNING for at finde den egentlige, uafhængige fejl — ikke blot endnu en ting på samme liste. **En testkørsel, der ikke består, kan skjule en ANDEN, endnu ikke-fundet fejl bag den første** — ret den første fejl, og kør testene igen, før du konkluderer at resten er grønne af rigtige grunde.

### En funktion, hvis navn svarer på et andet spørgsmål end den stilles

Android fandt det 05-10-2026, mens de byggede `#79`. `FormRegelLogik.typeNoegle`
hedder som om den giver en aktivitetstypenøgle. Den giver `ALL` eller
`FAMILIE_DEFINERET` — **formularreglernes gyldighedsområde**, som ikke findes i
`activity_types.key`.

Havde de brugt den til at sende `type_key`, havde **hver familie-egen
træningstype** sendt `FAMILY_DEFINED`, og serveren havde logget en uenighed for
hver eneste.

Deres formulering:

> Funktionen hedder `typeNoegle`, men den svarer på et andet spørgsmål. To
> begreber, ét navn — og det er værre end et forkert navn, fordi det **læser
> rigtigt på kaldstedet.**

Den sidste halvdel er hele pointen. Et forkert navn opdages, når man læser
funktionen. **Et navn, der passer til spørgsmålet man stiller, men ikke til det
funktionen besvarer, opdages aldrig fra kaldstedet** — for dér ser det korrekt
ud.

### Og iOS havde præcis den samme, med et endnu mere uskyldigt navn

Målt samme dag, efter at Android bad om et krydstjek:

```
FormRegelLogik.swift:188   static func activityTypeKey(fraLegacyType:) -> String
                             ... return familieDefineret : alle
FormRegelLogik.swift:106   static let alle = "ALL"
FormRegelLogik.swift:113   static let familieDefineret = "FAMILY_DEFINED"
```

Og værre: **tre kaldsteder i `DatainputLogik` kalder resultatet `typeNoegle`** —
i netop den fil, der bygger sync-pakken. Så en variabel ved det navn står
allerede i scope, med en værdi der ikke er en typenøgle.

**Begge platforme havde fælden. Ingen af dem havde lavet fejlen.** Den blev
fundet, fordi den ene byggede noget, der kunne ramme den, og **spurgte om den
anden havde et modstykke.**

### Hvad man gør ved den

**Mål hvad funktionen KAN returnere, ikke hvad den heder.** Et opslag, der har et
nødfald, returnerer to slags svar: det man spurgte om, og noget andet. Navnet
kan kun beskrive det ene.

Og når en nøgle VINDER over en tekst — som efter `#152` trin 1 — gælder:
**aldrig send et gæt.** Et opslag, der giver `null` ved ukendt, lader serveren
falde tilbage til teksten. Et nødfald, der giver en plausibel men forkert nøgle,
gør skaden i stedet for at undgå den.

### En afhængighed, der håndhæver OG returnerer, låner sit navn fra den forkerte halvdel

Backend-sessionen fandt 2026-10-01 (#67), at `vocabularies.py` erklærede `family_id: int = Depends(get_current_family)` uden at bruge id'et nogen steder — de tre lister er universelle. Målt bagefter, viste det sig at være ÉT af NI steder i samme repo (`aldersregler.py`, fem steder i `dbu.py`, `live.py::hent_opstilling`, `main.py::hent_funktioner`, `match_form.py`), ikke en enlig forglemmelse.

Ni ens forekomster er ikke ni fejl — det er et **uudtalt idiom**: `Depends(get_current_family)` er sådan en rute kræver login i dette repo, og `family_id` er bare navnet, afhængigheden tilfældigvis giver variablen. Ingen af de ni ruter skal scopes på familie (funktionsknapper er globale, DBU-data er nationalt, en opstilling scopes af `puljeid`/`kampnr`), og med ni eksempler forstås det allerede som "kræver login" — ikke som et løfte om scoping, ingen er vildledt af det i praksis.

**Rodårsagen: funktionen er navngivet efter det, den RETURNERER, ikke efter det, den HÅNDHÆVER.** `get_current_family` gør to ting på én gang — autentificerer, og giver et id — og enhver rute der kun har brug for det første, ender med at erklære noget den ikke bruger.

**Konventionen, fremover:** bind til `_: int = Depends(get_current_family)`, når id'et ikke bruges — ikke til `family_id`, som ser brugt ud selv når det ikke er. Ret det løbende, når du alligevel er inde i filen (samme "ingen koordinering nødvendig"-regel som interne navne under Sprogreglen) — ikke som en selvstændig oprydningsopgave i de otte resterende steder.

### En `git push`, hvis output afbrydes midtvejs, kan have fejlet uden at sige det

Backend-sessionen 2026-10-03/04 nat: en disk-kvote på den fælles maskine (ikke pladsen selv — `df -h` viste 164G fri — men en per-bruger kvote, udløst af 424 efterladte sandkasse-rødder i `/tmp`) gjorde skrivninger upålidelige i et kvarters tid. `git push` kørte, pre-push-hookets testsuite begyndte at printe `ok 1`, `ok 2`, ... — og output blev skåret af midtvejs. Kommandoen SÅ ud til at lykkes (ingen fejlmeddelelse, bare stilhed).

Den var ikke lykkedes. Et `git fetch` + `git rev-parse HEAD origin/master` bagefter viste, at commit'en stod lokalt, men aldrig nåede GitHub. Samme mønster gentog sig én gang til, senere samme nat, under et helt andet stykke arbejde.

**Reglen: en kommando, der fejler midt i sit eget output, efterlader et resultat, der ser fuldført ud.** Det er samme familie som Androids grønne vagt, der ikke målte noget, og `cat`-tjekket på en lås-mappe, der sagde "fri" hver gang — en flygtig fejltilstand (her: miljøet, ikke koden) producerer et svar, der ikke kan skelnes fra et ægte "det virkede."

**Den praktiske konsekvens:** efter ethvert `git push`, hvis der er den mindste tvivl om outputtets fuldstændighed (afbrudt stream, et miljø der lige har vist andre symptomer, en kommando der tog længere end normalt) — kør `git fetch` og sammenlign `git rev-parse HEAD` med `git rev-parse origin/<branch>` direkte, i stedet for at læse pushets eget "success"-udseende som beviset. Det kostede to minutter begge gange og fangede en reelt upushet commit, som ellers var blevet stående som "færdig" i en rapport, uden at nogen anden session kunne se den.

### En overtaget-dokument-funktion skal røre ALT det dokument er grundlag for — og udledt output skal genopbygges FØR en afstemning mod det

iOS-sessionen, 2026-10-03/04 nat: en funktion der overtager/fletter en anden enheds delte dokument rettede kun det, dens eget navn beskriver — ikke de andre ting, der afledes af SAMME dokument (planlagte notifikationer, udledte felter, en Live Activity). Formuleret som regel:

> **Enhver funktion, der overtager en anden enheds dokument, skal røre ALT, det dokument er grundlag for — ikke kun det, funktionen hedder noget med.**

Den dyre halvdel, fundet ved selve målingen: en afstemning af påmindelser læste et udledt felt (`kort_status`), der IKKE var genopbygget efter fletningen — en rettelse, der ser rigtig ud, kører grønt, og stadig afstemmer mod en FORÆLDET tilstand.

> **Genopbyg udledt output FØR en afstemning mod det, ellers afstemmes mod en forældet tilstand.**

Samme familie som dagens øvrige fund: en test eller en kørsel, der beviser noget TÆT PÅ den rigtige påstand, ikke selve den. Spørg derfor altid, når én funktion flettes/overtager et delt dokument: hvad ELLERS afhænger af dette dokuments indhold, og er DET genberegnet, før noget læser det igen?

### En kronisk tilstand er ikke udløseren til en akut hændelse

Koordinatoren meldte 05-10-2026, at maskinen døde af hukommelsesmangel. Grundlaget
var ægte og alvorligt:

```
otte Gradle-builds OOM-draebt i loebet af dagen
kl. 06:35   swap 239 MB fri af 4,0 GiB
```

**Men maskinen levede 68 minutter videre efter den måling**, og den sidste
OOM-kill lå otte timer før døden. Det, der faktisk skete, stod to linjer længere
ned i loggen:

```
15 dage       lid LUKKET (HandleLidSwitch=ignore), applespi stille: 2 beskeder
07:43:18      Lid opened        <- den ENESTE lid-haendelse i de 15 dage
07:43:42      43 applespi-fejl i EET sekund, derefter intet
```

**Fireogtyve sekunder fra en diskret hændelse til døden**, og nul
nedlukningsspor, nul OOM-kill, nul hængende opgaver i vinduet.

Fejlen er ikke, at hukommelsen blev målt forkert. Den var målt rigtigt, og den
er stadig et reelt problem. Fejlen er, at **den mest alarmerende måling blev
gjort til årsagen**, fordi den var den mest alarmerende.

**Prøven, der skiller dem, er: hvad ændrede sig?** En tilstand, der har holdt i
timer eller dage, forklarer ikke, hvorfor noget skete netop nu. Den forklarer
højst, hvorfor systemet var skrøbeligt, da det skete.

Og konsekvensen var praktisk: anbefalingen blev at frigøre hukommelse, hvilket
**ikke ville have forhindret nedbruddet.** Et rigtigt råd mod et forkert
problem ser ud som et svar.

### En konsekvens-beskrivelse er en antagelse, til den er målt — selv når rettelsen er rigtig alligevel

Koordinator-sessionen, samme nat (Issue #133): skrev at en manglende `stale-date`-opdatering ville lade en Live Activity-frist "stå tilbage" og gøre aktiviteten forældet midt i en lang kamp — en konklusion om hvad Apple/klienten GØR, udledt af at et felt manglede, ikke målt på en enhed. iOS fangede det: der er en lige så sandsynlig, MODSAT læsning (et manglende felt betyder "aldrig forældet", hvilket er værre, fordi det skjuler en død feed).

**Rettelsen var den samme under begge læsninger** — send feltet korrekt, uanset hvilken af de to der er sand. Men konsekvens-PÅSTANDEN i kortet var stadig en antagelse klædt som et fund, præcis den fejlklasse denne fil selv beskriver flere gange ovenfor (`origin/main` er en lokal kopi, et tomt svar fra det forkerte værktøj, en rigtig begrundelse med en ufuldstændig udførelse). At rettelsen holder under begge læsninger er ikke det samme som at have målt hvilken der er sand — skriv det som to muligheder, ikke én konklusion, når ingen af dem er bekræftet på en rigtig enhed.

### Et nødfald, der er enigt med kilden, gør testen blind

Android-sessionen, 2026-10-04, fundet ved at sabotere sin egen test og se den bestå i stedet for at blive rød:

> **Et nødfald, der er enigt med kilden, gør testen blind.** Retter man en lokal reservetabel, så den stemmer med serverens tal, kan testen ikke længere skelne "læser serveren" fra "læser reserven" — og ledningen kan være klippet over, uden at noget bliver rødt. Mål en VEJ med tal, der kun kan komme den ene vej. Samme fælde med en gættet øvre grænse: den er usynlig, så længe alle testdata ligger under den.

De rettede nødfaldets `U11` fra 30 til 20, så den stemte med serverens egen regel — og først DEREFTER kunne de ikke længere se, om serveren overhovedet blev læst, fordi et klippet opslag og et korrekt opslag nu gav samme facit. De fandt det ved at klippe opslaget over MED VILJE og se testen forblive grøn.

**Anden halvdel, samme dag:** en sabotage af formen "gæt 45, når længden er ukendt" bestod også, fordi hver reel testperiode var kortere end 45 — et gæt på en øvre grænse ændrer kun noget, når data OVERSKRIDER den. Et tilfælde hvor de tre mulige svar faktisk er forskellige, viste det: ved 11:40 giver ukendt længde "85'", et gæt på 45 giver "90+5", en nominel værdi på 30 giver "60+20" — kun DÉR kan en test se forskellen.

> **En forkert værdi, der ligger uden for det område testen prøver, er lige så usynlig som ingen test.**

Samme familie som nattens egen lektie om en test-modulliste, der manglede tre filer (se #128's afsnit): begge handler om en test, der består, fordi den ikke rører det, den påstår at måle.

### En begrundelse, der citerer en anden platforms adfærd, har ingen holdbarhedsdato uden en dato og et sted

Android, 2026-10-04, formuleringen Koordinator selv kaldte dagens bedste:

> **En begrundelse, der citerer en anden platforms adfærd, har en holdbarhed, som ingen af parterne kan se. Når vi skriver "fordi iOS gør X", skal datoen OG stedet hvor X står med, så den næste kan måle den på ét opslag.**

Koordinator relayede 2026-10-03: "iOS skriver fortsat deres udledte `kort_status` for at dække deres egne gamle builds." Sandt, da det blev sendt — og stod derefter skrevet, ordret, som begrundelse TRE steder i Androids kode. Få timer senere besluttede Morten, at fire testere, han selv styrer opdateringen af, ikke er en bagudkompatibilitets-risiko ("vi 4 der tester") — iOS satte `Udgivelse.beskytterAeldreBuilds = false`, og skriver nu `kort_status` som et tomt objekt. Androids begrundelse pegede fra det øjeblik på en adfærd, der ikke længere fandtes, uden at nogen af de to sider kunne se det — fordi påstanden citerede en anden platform uden at sige HVOR i den anden platforms kode den stod, eller NÅR den blev målt.

**Samme dag, samme form, en gang til:** Androids egen "nul eksponering for #138" (0/1-vs-boolean) blev svækket af Koordinatorens måling (serveren konverterer otte steder, sender råt tolv). Begge påstande var korrekte, da de blev skrevet — og blev forældet af en beslutning truffet et ANDET sted, ikke af en fejl i selve målingen.

**Det er ikke dårlige relæer.** En påstand om egen kode kan efterprøves ved at læse koden igen. En påstand om en ANDEN parts adfærd kan det ikke — den anden parts kode kan ændre sig uden at nogen, der citerede den, får det at vide. Skriv derfor ALDRIG "fordi iOS/Android/serveren gør X" uden en dato og en fil/linje (eller et issue-nummer), den næste kan slå op og måle direkte — samme disciplin som "Rodårsag, ikke symptom" ovenfor, anvendt specifikt på en begrundelse der rækker ud over eget repo.

**En relateret detalje fra samme kort, værd at kende for ALT der bygger på et delt, last-write-wins-dokument:** Android fandt at deres egen oprydningsfunktion for det udledte `kort_status`-felt ikke var død, men havde et KRYMPENDE grundlag — serveren opbevarer et dokument som det blev skrevet, så et holdkort gemt en bestemt dag bærer feltet videre, indtil den NÆSTE, der gemmer akkurat DEN kamp, skriver det tomt. Deres egen formulering: "det er et vindue, der lukker, ikke en stående beskyttelse. Den dag nogen spørger 'er vi dækket?', er svaret 'for en krympende rest', ikke 'ja'." En oprydning af et ældre feltformat i et last-write-wins-dokument er aldrig færdig i samme øjeblik koden lander — kun i det øjeblik hver enkelt dokument er blevet gemt mindst én gang siden.

### Et værktøj skal rapportere sit eget interval, ikke kun sit resultat

Koordinator, 2026-10-04, fundet i sit eget værktøj: `check-parent-cards.sh` hentede `issues(first:100, states:CLOSED)` uden sortering — GitHub leverer fra laveste issue-nummer, og Backend havde 113 lukkede kort. Kontrollen så kun #1 til #111, blind for hele dagens arbejde, og meldte alligevel "ingen lukkede forældre med åbne børn".

> **En kontrol, der siger "#6-#137", afslører sig selv; en der siger "ingen problemer", gør ikke.**

Samme familie som denne fils egne afsnit om et filter, der ikke rammer noget ("Et håndskrevet mønster er selv et måleinstrument"), og om en test-modulliste der manglede filer (#128) — et værktøj, der kun rapporterer sit RESULTAT, kan ikke skelne "jeg så alt, og alt var i orden" fra "jeg så en brøkdel, og den brøkdel var i orden". Rettelsen: scriptet rapporterer nu selve SIT UDSNIT ("INTERVAL 100 lukkede kort set, #6-#137" pr. repo) ved siden af konklusionen — hullet er dermed synligt uden at nogen skal opdage det ved et uheld.

**Reglen, generaliseret:** et instrument, der kan se en DELMÆNGDE af det, det påstår at dække (en side af resultater, en tidsgrænse, et filter), skal sige HVILKEN delmængde det så — ikke kun hvad den fandt i den.

### Et vaerktoej, der kan goere kode USYNLIG, fejler ikke — det bliver faerdigt for tidligt

Androids fund 06-10-2026, og det er den alvorligste af nattens
instrumentfejl, fordi den ikke har nogen fejltilstand.

`A21` braekkede `TidslinjeTekst.kt`, og aarsagen laa i fundamentet, ikke i
runden:

```kotlin
"${ev.spilleminut?.let { "$it'" } ?: "–"} — ${beskrivelse(ev, modstander)}"
```

**En streng inde i en `${}` inde i en streng.** Den ydre strengskanner stoppede
ved det INDRE citationstegn, saa alt efter tegn 3484 i filen blev regnet som
ikke-kode.

```
linje  71   fun manglendeTidslinje(r: Registration)   OMDOEBT
linje 131   fun linjer(r: Registrering)                USYNLIG
```

**Det var en HALV omdoebning i samme fil, der afsloerede det** — ikke en test,
ikke en vagt. Havde filen ikke tilfaeldigvis haft to brug af samme type paa hver
sin side af bruddet, var den ikke opdaget.

> Et faerdigt vaerktoej uden fund ser ud som et rent trae.

Og det er en grad vaerre end de to beslaegtede fejl samme nat — koordinatorens
`formRegler`-tal paa 31 og Androids historiske saet uden typer. **Dér var
MAALINGEN ufuldstaendig. Her var MATERIALET afkortet**, og alle senere maalinger
paa filen var rigtige om det, de saa.

### Spraengradius maales med ét script, og maalingen kan kopieres

```
for hver fil: slutter sidste kode-spaend FOER filens ende,
              og staar der erklaeringer i halen?

filer med den form:        1
erklaeringer usynlige:    12
```

Den form findes ogsaa andre steder: **Swift har `\(...)` med samme indlejring,
og Pythons f-strenge tillader nested quotes fra 3.12.**

### Men klassen findes KUN hvis du skanner — den kan ikke findes hvis du parser

Maalt i alle tre traeer 06-10 kl. 06:25:

```
Android   regex-skanner                          HAVDE fejlen, rettet
iOS       SwiftParser + SwiftSyntax, en
          SyntaxRewriter over TokenSyntax        IMMUN ved konstruktion
Backend   INGEN mekanisk omskriver, og deres
          eet #124-script bruger ast.NodeVisitor  IMMUN ved konstruktion
```

En rigtig parser kan ikke "blive faerdig for tidligt" ved et indlejret
citationstegn: interpolation er en node i traeet, ikke et tegn i en streng.

**Og iOS' dokumenterede opfoersel er rigtig i BEGGE retninger**, af samme grund:
*"Roerer KUN tokens af arten identifier. Strenge, kommentarer og tekst i
literaler er ikke identifikatorer og forbliver uroert; kode i
streng-interpolation ER tokens og omskrives."* En streng inde i en `\(...)` er
stadig en streng-token, altsaa ikke en identifikator.

**Afvejningen, og den skal navngives og ikke skjules:** Android er den eneste,
der BYGGEDE en mekanisk omskriver, og de omdoebte ogsaa klart flest navne (287 i
trin 2). Vaerktoejet betalte sig. **Fejlklassen kom med i prisen**, og den
rigtige konklusion er ikke "byg ikke vaerktoejet" — det er at maale
spraengradius med det script ovenfor, hver gang skanneren roeres.

### Og den SPEJLVENDTE risiko er Backends, fra samme graense

Backends `scripts/tael-strengbindinger-124.py` findes af den modsatte grund, og
dens egen dokumentation siger det:

> Backend har ingen compiler, saa et omdoebt navn, der OGSAA forekommer som en
> streng-literal et andet sted (`logging.getLogger(...)`, `assertLogs(...)`,
> `getattr(...)`), kan drive uden en synlig fejl.

```
Androids fejl    skanneren stopper   ->  IDENTIFIKATOREN bliver tilbage
Backends risiko  omskrivningen rammer ->  STRENGEN bliver tilbage
```

**Begge er "omdoebningen og strengen er uenige", fra hver sin ende.** Og ingen af
dem giver en fejl: den ene efterlader kode, der ikke blev omdoebt, den anden en
streng, der skulle have vaeret. Derfor har begge brug for en TAELLING foer og
efter, ikke en test.


#### En BINAER fejl kan vaere en gate-kontrol. En GLIDENDE skal vaere en taelling, der diff'es

Androids formulering 06-10-2026, efter at de havde automatiseret
spraengradius-maalingen ovenfor (`spander.py`, `e9ed00d`: *11 former skelnes,
nul usynlige haler*, sabotage-efterproevet):

> En fejl, der er BINAER, kan vaere en gate-kontrol. En, der GLIDER, skal vaere
> en taelling, der diff'es — en gate paa et glidende tal bliver enten slaaet
> fra eller frosset forkert.

Og det forklarer, hvorfor de to sider af samme graense har forskellig FORM:

```
Androids fejl    en usynlig hale FINDES eller ej      BINAER   -> gate
Backends risiko  et antal streng-bindinger DRIVER     GLIDENDE -> taelling
                                                                  foer/efter,
                                                                  sorteret, diff
```

**Begge er "omdoebningen og strengen er uenige", fra hver sin ende.** Men den
ene har en tilstand, en port kan afvise paa, og den anden har et tal, der
aendrer sig lovligt. En port paa det glidende tal maatte vaelge mellem at
blokere lovlige aendringer eller at blive sat saa hoejt, at den intet fanger.

#### En paastand ankret til et UDFALD holder kun, saa laenge udfaldet ikke aendrer sig

Androids maaling 06-10-2026, og den er den skarpeste udgave af afsnittet
ovenfor, fordi deres selvproeve stoppede vaerktoejet paa **korrekt arbejde.**

`udkast` stod i deres afvis-liste som bevis paa, at to-bogstavs-posten
`ud -> out` ikke maa dele ordet. Saa gav `A23` `udkast` sin egen post
(`-> draft`), og dermed blev den omdoebt — hvorefter deres egen kontrol faldt.

```
paastanden var ankret til   UDFALDET      "udkast springes over"
den skulle vaere ankret til EGENSKABEN    "udkast deles ikke som ud + kast"
```

> En paastand ankret til et udfald holder kun, saa laenge udfaldet ikke aendrer
> sig — og hele formaalet med arbejdet er at aendre udfald.

**Rettelsen er den positive form:** hele posten `udkast` skal VINDE over
to-bogstavs-ordet. Og de beholdt `udtaget`/`udled`/`udvisning` som bevis paa den
anden side — de ord har ingen egen post, saa de SKAL springes over.

**Formen i et stoerre perspektiv:** i et omdoebningsarbejde er "dette navn er
uaendret" den daarligste mulige invariant, fordi arbejdet bestaar i at aendre
navne. Enhver vagt, hvis groenne tilstand beskriver nutiden frem for en regel,
bliver roed af succes.

```
DAARLIG   taelleren staar paa 9        falder naar arbejdet skrider frem
DAARLIG   navnet X findes ikke        bliver falsk naar X bliver det nye navn
DAARLIG   navnet Y springes over      bliver falsk naar Y faar sin egen post
GOD       Y deles ikke paa ud + kast  en egenskab ved REGLEN, ikke ved data
```

Det er samme skelnen som den binaere mod den glidende fejl ovenfor, paa en akse
mere: **ikke bare hvad vagten maaler, men om det den maaler er noget, arbejdet
er designet til at flytte.**


#### Maalingen afgoer BEGGE veje — ogsaa "byg den", ikke kun "byg den ikke"

Afsnittene ovenfor har to tilfaelde, hvor en maaling fik Android til at
FRAVAELGE en vagt: 37 ukendte JSON-noegler hvoraf faa var aegte, og 122
streng-spejlinger hvoraf 3 var. Begge gange var tallet selv svaret.

**Og det gav en skaevhed, som de selv fangede 06-10-2026.** De var paa vej til
at fravaelge en kommentarsprog-vagt paa samme formodning — en engelsk kommentar,
der citerer dansk brugertekst, baerer `æøå` og ville blive meldt falsk.

```
kommentarlinjer i app/src                12316
danske paa DIAKRITTER                     8569
danske KUN paa funktionsord               1610   en diakrit-vagt misser dem
ENGELSKE linjer med en dansk diakrit          1   <- den ventede stoejkilde
```

**Én.** Og den er selv en blandet linje. Saa vagten blev bygget i stedet for
fravalgt.

> En vagt er vaerd at bygge, naar dens falske positiver er MAALT — ikke naar
> dens idé er god. Og det gaar begge veje: jeg havde fravalgt denne paa en
> formodning.

**Reglen er altsaa ikke "maal foer du bygger".** Den er: **maal foer du
afgoer** — og et fravalg paa en formodning er lige saa umaalt som et tilvalg
paa en.

### To signaler, fordi diakritter alene daekker 84 %

```
8569 af 10179 danske linjer har aeoeaa     84 %
1610 er danske KUN paa funktionsord        ikke · skal · naar · fordi · derfor
```

Det er samme graense, afsnittet om identifikatorer allerede naevner: **en
æ/ø/å-vagt melder groent paa 97 % af arbejdet.** Dansk uden diakritter er
stadig dansk, og en vagt med ét signal maaler det, der er nemt at se.

### Og selvproeven fandt TO fejl i vagten, foer den havde koert én gang

```
1  detektoren saa kun linjer der BEGYNDER med en kommentarmarkoer
   -> en efterstillet `// kommentar` efter kode var USYNLIG
   -> rettet til at bruge `spander`, hvilket giver modproeven gratis:
      `// ikke en kommentar` inde i en STRENG taelles ikke med

2  proeven fejlede paa en fixtur med `*`-linjer uden en `/*`-aabning
   -> FIXTUREN var forkert, ikke detektoren
```

**Den anden er den, der er svaer at se.** En roed selvproeve laeses som "vagten
er forkert", og her var proevens eget materiale ugyldigt Kotlin. At skelne de to
kraever, at man laeser fixturen med samme mistro som koden.

**Tredje gang samme nat, at en selvproeve fangede noget i den vagt, den
tilhoerer, FOER vagten havde koert én gang.** Det er argumentet for at skrive
selvproeven samtidig med vagten og ikke bagefter: den finder fejl, der ellers
ville blive lagt i en groen koersel.


### Og maalingen af en foreslaaet vagt ER beslutningen om den

Android maalte spejlrisikoen hos sig selv og fik **122** gamle navne, der stadig
staar i en streng. **Naesten alle var dansk brugertekst:** `"Opstilling"` er
fanens navn, `"Kort"`, `"Familie"`, `"efter"` i en saetning. Moensteret kunne
ikke skelne et gammelt identifikatornavn fra et almindeligt dansk ord.

Indsnaevret til den form, der ER farlig — **hele strengen er navnet, OG navnet
har mindst to camelCase-led** — blev svaret **3**:

```
"GpsRaekkeRaa"   buildClassSerialDescriptor   klassen hedder GpsRowRaw
"TroejeNummer"   PrimitiveSerialDescriptor    objektet hedder ShirtNumber
"GpsRaekke"      buildClassSerialDescriptor   klassen hedder GpsRow
```

Alle tre er serializer-descriptornavne, og efterproevet: ingen polymorf
serialisering haenger paa dem, saa navnet er ren typeidentitet og fejlbesked.
**Ingen adfaendsaendring — men en foraeldet paastand i en besked, nogen en dag
laeser under en fejlsoegning.** Rettet, med hvert nyt navn kontrolleret mod en
klasse, der findes.

**De byggede den IKKE som en vagt**, og begrundelsen er symmetrisk i begge
ender:

```
ved 122   den ville blive slaaet fra        (som 37-noegle-vagten)
ved   3   der er ikke nok tilbage til at
          retfaerdiggoere en skralde, der
          skal vedligeholdes
```

**Det er anden gang samme nat, en vagt blev fravalgt EFTER at vaere maalt, og
begge gange var tallet selv svaret.** Den foerste var 37 noegler i 123
forekomster.

> Maal den vagt, du overvejer at bygge, FOER du bygger den. Outputtet afgoer
> baade om den kan bruges og om den er noedvendig — og et instrument, der melder
> 122 hvor tre er aegte, er ikke et uferdigt instrument. Det er det forkerte
> signal.

**Og skriv maalingen OG fravalget ned**, saa den naeste ikke maaler det forfra.
Android lagde begge i `ci.sh`.


### Et submodul har TRE lag, og "bagud" betyder noget forskelligt i hvert

Koordinatoren maalte 06-10-2026, at Androids `shared/`-checkout var 34 commits
bagud og manglede fem nye ordliste-afsnit, og konkluderede, at de ikke kunne
laese den faelles liste. **Android maalte det og havde ret imod mig.**

```
1  pin, committet i sessionens repo    flyttes kun med en commit
2  LOKAL origin/main-ref               flyttes af git fetch
3  checkout (HEAD)                     flyttes af checkout/submodule update
```

Maalt i Androids trae, samme sekund:

```
1 pin                95cc402   34 bagud
2 origin/main-ref    b6d0f83   AJOUR — havde alle fem afsnit
3 checkout           95cc402   34 bagud
```

**Deres vaerktoej laeser lag 2**, og det er bygget mod praecis den
foraeldelsesfejl:

```python
# .scripts/omdoeb_core.py
r = subprocess.run(["git","-C",str(ROD/"shared"),"show",f"origin/main:{relsti}"], ...)
_REF_BRUGT[relsti] = "origin/main"        # eller: det UDTJEKKEDE submodul
...
print(f"  {fil.split('/')[-1]:28} laest fra {ref}")   # hver koersel
```

**Jeg maalte lag 3 — det lag, ingen laeser programmatisk.** Og de to lag ser
identiske ud i enhver `git log` paa sessionens eget repo, fordi de begge bliver
til den samme hash dér.

### Konsekvensen: en laesevej er stoerre end et commit

Androids formulering:

> Et commit er ikke en forbindelse — men en LAESEVEJ er. Vores gaar uden om
> pinnen, og den optegner hvilken ref den brugte.

**Og pinnen flyttes med rette ikke.** `PlayerData_Shared` baerer baade data og
`DELT-BLOK.md`, altsaa sessionens egne instruktioner, og en session maa ikke
loefte en pegepind til aendringer i dem. iOS afviste det to gange og havde ret;
Android loefter den heller ikke. **Forskellen mellem dem er ikke modet — det er,
at den ene har en laesevej, der ikke kraever et loeft.**

### Hvad der kan kopieres, og hvad der ikke kan

```
VIRKER IKKE   "hent oftere"        en regel, der skal huskes hver gang
VIRKER        laes `git show origin/main:<fil>`
              OG print hvilken ref du faktisk brugte
```

Den anden halvdel er den, der goer det til et instrument i stedet for en vane:
**en laesning, der ikke siger hvilken ref den laeste, kan ikke skelnes fra en
foraeldet.** Det er samme krav som "et vaerktoej skal rapportere sit eget
interval", paa en akse mere.

**Og graensen, der STAAR:** lag 2 opdateres af en `fetch`. Androids tre foerste
runder var ajour, fordi de havde hentet; den fjerde laa efter deres sidste
hentning, og dér arbejdede de paa koordinatorens ord alene. **En laesevej
fjerner pin-problemet, ikke fetch-problemet** — den gor det bare synligt, fordi
refen bliver printet.


### Da hullet var i vagtens MATERIALE og ikke i dens doemning

Androids maaling 06-10-2026, og det er den skarpeste udgave af
interval-reglen ovenfor.

`DatainputGem` henviste i prosa til `FormRegelLogik.typeNoegle`, efter at
objektet var omdoebt til `FormRuleLogic`. Vagten mod doede prosa-referencer var
**groen**. De rettede den til at doemme HVERT led i en kvalificeret reference —
og **den var stadig groen.**

```
historiske() samlede kun `fun`-erklaeringer fra git
  -> et omdoebt OBJEKT havde ALDRIG staaet i det historiske saet
  -> kvalifikatoren kunne ikke doemmes, uanset hvor mange led man saa paa

kun fun                          34 fund
+ object/class/interface/enum   121 fund   ->  80 nye, 79 var aegte
```

Deres formulering:

> Jeg rettede doemningen og troede, hullet var lukket. Det var MAALINGEN, der
> manglede materiale — ikke doemningen, der manglede led.

**Det er en tredje akse ud over resultat og interval:** en kontrol kan have
rigtig logik, rapportere sit interval aerligt, og stadig vaere blind — fordi
dens KORPUS ikke indeholder den slags ting, fejlen sidder i. Og den ser
praecis ud som en kontrol, der virker.

**Spoerg derfor om tre ting, ikke to:**

```
hvad svarer den          resultatet
hvor langt kiggede den   intervallet
HVAD kiggede den PAA     korpus   <- den, der blev glemt
```

**Og `val`/`var` blev holdt bevidst ude:** de er korte og hyppige (`n`, `x`), og
en prosa-backtick ville begynde at ramme en forlaengst fjernet lokal variabel.
**Typer er distinktive; variabler er ikke.** Et stoerre korpus er ikke altid et
bedre korpus — det er en afvejning, og den skal vaere maalt.

### En beskyttelse, man ikke har bygget med vilje, kan fjernes af en der ikke ved den beskytter noget

Samme nat bad koordinatoren Android passe paa posten `ud -> out`: to bogstaver,
og `udkast`, `udtaget`, `udvisning`, `udled` begynder alle med dem. En
delstrengs-erstatning giver `outkast`, **og den kompilerer.**

Android maalte det foer brug, og posten var sikker. **Men svaret var ikke
beroligende:**

> Beskyttelsen kommer af et tre-bogstavs-minimum i min sammensaetningsregel,
> som blev sat for `vaelger = vaelg + er` — en helt anden grund. Saenker nogen
> den graense af en tredje grund, begynder `udkast` tavst at dele.

Reglen er nu en selvproeve i begge retninger: at `logUd -> logOut` virker, OG at
de fire `ud`-ord springes over.

**Formen, og den gaelder ud over denne post:** naar en maaling viser, at du er
beskyttet, saa maal OGSAA hvad der beskytter dig. Er svaret en graense sat af en
anden grund, er du ikke beskyttet — du er heldig, og den naeste, der roerer
graensen, ved det ikke.

### En hypotese om en anden platform er stadig en hypotese

**Androids egne ord, 05-10-2026**, efter at de havde gaettet — og taget fejl —
om at iOS og Backend havde samme hul som dem selv:

> En hypotese om andre er stadig en hypotese, **ogsaa naar den goer een selv
> mindre enestaaende.**

**Sagen:** Android fandt, at deres egen `is_prose`-vagt afviste alt med `$` og
dermed gav **164 af 620 brugertekster fripas.** De meldte det med tilfoejelsen
*"det her gaelder sandsynligvis jeres vagter ogsaa"*.

**Maalt af koordinatoren FOER det blev relayet:**

```
iOS      HAR vagten, og den HAANDTERER interpolation eksplicit.
         normaliser() erstatter interpolationer med en pladsholder, og
         docstringen siger, at "Deltog <a> af <b>" TAELLER som brugertekst
Backend  har INGEN prosa-vagt. Deres ratchet handler om NAVNE, ikke brugertekst
```

**Fripasset var Androids alene.**

**Og det er den OMVENDTE retning af dagens oevrige fejl.** De syv rettelser 01-10
og de otte 04-10 var alle for brede konklusioner **til afsenderens fordel** — et
fund, der gjorde en anden session forkert. Denne var et gaet, der gjorde
afsenderen **mindre** forkert, ved at goere fejlen almindelig.

> Det er en behagelig antagelse, og derfor sjaeldnere at faa maalt.

**Reglen er den samme i begge retninger:** *"sandsynligvis gaelder det ogsaa
jer"* er et SIGNAL om, hvor der skal maales — aldrig et facit. Og den, der
relayerer det, skal maale det, foer modtageren bruger en tur paa at modbevise
det.

**Koordinatorens del af det:** jeg maalte begge traeer, foer jeg sendte noget.
Det kostede to `git grep` og sparede to sessioner for en tur hver.

### En måler, der gætter på sprog, bliver værre jo bedre det går

**Fem gange den 05-10-2026 fejlede en "ligner dansk"-måler under `#124`s
omdøbning af 1130 testnavne.** Tre gange hos Android, én gang hos Koordinator,
og én gang da Android beskrev sin egen fejl som Koordinatorens. Hver gang var
koden rigtig, og måleren forkert.

**Androids formulering, og den er den generelle:**

> En måler, hvis signal er "ligner dansk", bliver dårligere jo længere arbejdet
> skrider frem. Den tæller ikke det, der er tilbage — den tæller det, der
> ligner, og de to falder fra hinanden netop i takt med fremskridtet. Den vil
> melde "det går langsommere" lige inden den melder "vi er færdige".

**Og den femte fejl viste hvorfor et ordlistefilter ikke kan laves rigtigt:**

> Et sprogfilter bygget af korte funktionsord kan ikke skelne sprogene. De korte
> ord er netop dem, to beslægtede sprog DELER — og samtidig dem, der er hyppige
> nok til at ramme alt.

Filteret havde `for`, `der`, `med`, `til`, `den`, `det`. Det flagede ni navne,
der alle var engelske sætninger:

```
"a card FOR someone not on the pitch changes nothing"
"the half sends the fields the server requires ... FOR an ordinary match"
```

**De fire målere, der fejlede, og hvad der virkede i stedet:**

| maalerens signal | hvad den ramte | den strukturelle erstatning |
|---|---|---|
| `aeoeaa`-translitteration | `Does`, `Shirt` — engelske bogstavpar | `[æøå]` ALENE, aldrig `oe`/`ae`/`aa` |
| fjorten typiske danske ord | `kortslutning`, `holder`, `Indhold` | helt ord i en camelCase-opdeling |
| korte funktionsord | engelske sætninger med `for` | listen over FAKTISK omdoebte |
| forekomster af et moenster | `kamp` tre gange i samme fil | DISTINKTE navne, filtreret paa `@Test` |

**Reglen: en fremskridtsmåler skal tælle en struktur, ikke en lighed.**
`@Test` på klasseniveau. Distinkte deklarationer. Listen over det, der faktisk er
omdøbt, meldt i hver commit-besked, så den kan efterprøves.

Og vil man alligevel finde kandidater ved sprog, er den eneste retning, der er
sikker, **"ikke i en engelsk ordbog"** frem for "ligner dansk". Den
overvurderer — forkortelser og fragmenter kommer med — men den kan ikke melde
*"der er mindre tilbage, end du tror"*, og det er den retning, en måler skal
fejle i.

### Et testnavn må ikke stave et felt om, som serveren eller koden ejer

Under samme omdøbning lod Android **sytten navne stå** med dansk eller med en
understreg, og hvert enkelt med en grund:

```
konto_komplet · is_physical · match_number_prefix · FAMILY_DEFINED
    understregen er en del af serverens eget feltnavn, ikke dansk snake_case

foerMaal · halvlegPayload · tiderTekst · vaelgPosition · erSpoegelse
    navnet paa en PRODUKTIONSfunktion

puljeid · kampnr · raekke · spiller_navn · from_position · card_status
session_type · ApiJson · SerialName · og vaerdien `Kamp`
    en frossen noegle, en kolonne eller en vaerdi i data
```

**Et testnavn er en påstand om, hvad koden hedder.** Omdøber man navnet i testen,
men ikke i koden, lyver testen — og den bliver ikke rød af det. iOS lod `foerMaal`
stå af samme grund, uafhængigt, samme aften.

Reglen gælder begge veje: et testnavn må heller ikke beholde et dansk ord, som
koden ikke længere bruger.

### Et gulv fanger skrumpning. Fejlen er, at kilden vokser

Androids vektortest hævdede `size >= 8` på en delt vektorfil, mens deres fire
andre hævdede et eksakt antal (`assertEquals(162, ...)`). Målt 05-10-2026.

> Et gulv fanger kun skrumpning; den fejl, der sker, er at kilden VOKSER forbi
> vores kopi — og så ligger et nyt tilfælde ulæst, mens hele vektortesten
> består.

**Det er præcis den fejl, en kanarie findes for**, og et `>=` kan ikke se den.
Serveren tilføjer en niende vektor, klientens otte består, og ingen får at vide,
at den niende aldrig blev kørt.

**Et antal, der må vokse frit, er ikke en kontrol — det er en kommentar.** Hævd
det eksakte tal, og lad testen fejle højlydt med beskeden *"kilden har flere
tilfælde, end denne test kender"*.

Og værd at bemærke: formen fandtes allerede fire steder i samme kodebase.
**Afvigelsen var i den nyeste test**, ikke i den ældste — så det er ikke gammel
gæld, men en konvention der ikke blev fulgt, da den var kendt.

### En frysning kræver en sabotage, FØR facit rettes

Androids krav, 2026-10-04, efter to uafhængige tilfælde samme dag: iOS' testkørsel hang, så de nåede ikke at se en vagt fælde en ændring, FØR de rettede deres eget facit ud fra en liste i stedet — og Android havde selv, to gange aftenen før, en grundlinje der blev grøn på reelt tomt/ugyldigt data (garbage), fordi ingen sabotage var kørt først.

> **Når en vagts facit skal ændres, skal vagten først have FÆLDET af den ændring, der udløser det. Ellers ved man ikke, om facit beskrev noget.**

Samme form som denne fils egne "Få testen til at fejle, før du stoler på at den består" (ødelæg med vilje det testen skal fange, bekræft rødt, ret tilbage) — men skærpet fra en anbefaling til et KRAV specifikt for frysninger/facit-opdateringer: det er ikke nok at vide reglen generelt, proceduren skal tvinge den, hver gang et facit selv er ved at blive rettet, ikke kun når en ny test skrives.

### En test af "rydder den?" kan ikke se "rydder den for meget?"

Android, 2026-10-04 (build 24): Morten fandt at en ny kamp viste den FORRIGE kamps 2-0 og kort — kladden blev kun delvist ryddet ved kampskift. Androids rettelse (`vaelgKamp` rydder UBETINGET) var sabotagetestet og bevist: rydningen virker. Men en familie, der skrev en kommentar FØR de valgte kamp, mistede teksten i samme sekund — en tavs datafejl rettet ved at indføre en anden.

> **En test af "rydder den?" beviser, at mekanismen virker. Den kan ikke se, at mekanismen rammer for bredt.**

Fundet blev ikke fanget af et værktøj, en test eller Koordinator (der selv havde foreslået den brede regel, "skift af kamp rydder alt" — den forkerte). Det blev fanget af, at **iOS læste Androids afgrænsning** og genkendte brugssituationen fra en anden vinkel.

**Hvorfor den hører her, og ikke kun hos den platform der fandt den:** alle denne fils øvrige lektioner handler om et VÆRKTØJ, der ikke kiggede — en grøn test, et genbrugt resultat, en liste der kun så de ældste hundrede kort. Dem bygger man sig ud af med et bedre værktøj. Denne handler om, at en rettelse kan være BEVIST korrekt og stadig forkert, fordi beviset kun dækker den retning, man selv tænkte på — intet værktøj fanger det, kun en anden læser med en anden vinkel på samme kode. Det er selve argumentet for at to platforme afstemmer AFGRÆNSNINGEN, ikke kun koden, før en delt regel landes.

**Beslægtet, samme udveksling:** Android havde en etårig kodekommentar, der sagde præcis det rigtige om GPS-rækker ("de hører til ÉN kamp, og blev de stående, kunne man se den forrige kamps løbedistance på den nye") — skrevet, rigtig, og ALDRIG koblet til selve kampskiftet. Koordinator sammenlignede den med Androids egne `antalPerioder`/`harPause` (en konstant, der kun blev læst af en test, aldrig af den kode den skulle styre) — samme fejlklasse begge gange: en regel der er skrevet ned et sted, men ikke forbundet til det, den rent faktisk skal beskytte mod, er ikke forskellig fra en regel der aldrig blev skrevet.

### En overgang, der er sikker for LÆSNING af én nøgle, er usikker for ITERATION over nøglerne

04-10-2026. Dual-keying — serveren sender både den gamle og den nye nøgle, indtil
alle klienter har skiftet — bar syv ombæringer igennem på én dag uden et enkelt
brud. Stillingstabellen, prognosen, seks endepunkter, hele statistikken.

**Og den indførte en fejl, ingen havde forudset, i en app ingen havde rørt.**

```
serveren sender    {"Ukendt hold": 3, "unknown_team": 3, "A1": 12, ...}

et OPSLAG          stats["unknown_team"]        -> 3     rigtigt
en ITERATION       stats.keys.sorted()          -> "Ukendt hold" OG
                                                   "unknown_team" som
                                                   TO forskellige hold
en SUMMERING       stats.values.sum()           -> taeller dobbelt
```

Android havde fejlen **live i to timer** — holdvælgeren viste to hold, hvor der
var ét. Ingen havde ændret Android-kode. Serveren begyndte bare at sende en
nøgle mere.

**Og de tre opregningssteder var ikke til at finde ved at læse opslagene.** De
stod i `TraeningSektion:44`, `KampeSektion:178` og `KampeSektion:75`, og det
sidste manglede i koordinatorens egen måling.

**Hvad det foreskriver:** når du dual-keyer et svar, så find hver klients
ITERATIONER over de nøgler — ikke kun deres opslag. `.keys`, `.map`, en
`for`-løkke over et dict, en summering af værdierne. Et opslag er sikkert under
dual-keying; alt, der behandler nøglesættet som en LISTE, er det ikke.

**Og ét skridt mere, som iOS og Android begge fandt uafhængigt:** en foldning,
der fjerner dubletten, er ikke nok, hvis kaldstedet også bruger nøglen som
ETIKET. Androids `PillPicker(hold.map { it to it })` ville have vist
`all_teams` ordret til forælderen — dubletten væk, ny fejl ind. Deres egne ord:

> Modellen havde fået nøglen; skærmen havde ikke fået teksten.

**Og foldningen bliver død kode, i samme øjeblik den gamle nøgle fjernes.** Den
er ikke neutral når den er ubrugt — den er en konkurrerende definition, som
afsnittet om døde mængder ovenfor beskriver. Fjern den i samme omgang.

### En ukendt/omdøbt nøgle har tre udfald, ikke ét — og det højlydte er det sikreste

Backend, 2026-10-04, tre uafhængige incidenter samme nat, hver med en ANDEN konsekvens af samme grundfejl ("klienten mødte en nøgle, dens model ikke kendte i den form"):

```
1. ikke-optionelt felt     decode af HELE svaret kaster — synligt med det
   (standings' haste-fund)  samme, fundet og rettet på ni minutter
2. opslag på en streng-    returnerer nil/null — funktionen stopper TAVST,
   nøgle (et map/dict)      ingen fejl nogen steder
   (push.py's "kamp")       (et deep-link ville have været dødt — man trykker
                            på en notifikation, der ikke gør noget, og
                            trykker igen, uden at vide hvorfor)
3. felt MED en standard-   decoder til 0/en tom liste — skærmen er bare
   værdi (Androids egne     tom eller viser et forkert tal, ingen fejl
   prognose-felter)         nogen steder
```

**Rækkefølgen ovenfor ER en sikkerheds-rangering, ikke kun en liste.** Et kast er det BEDSTE af de tre udfald, selvom det er det mest dramatiske — det kan ikke undgås at blive set. De to andre ligner normal drift. Det er hele grunden til, at dual-keying (send BÅDE det gamle og det nye navn, se "En standardværdi, der er et plausibelt svar, skjuler et manglende felt" og "Et felt der bliver nullable er et kontraktbrud" ovenfor) er den rigtige standard-reaktion på en omdøbning, UANSET hvilken af de tre former man selv tror klienten bruger for det pågældende felt — man kan ikke vide det uden at læse klientens kode, og selv når man gør, er det let at fejlgætte (se "En ren checkout er ikke en aktuel checkout" og "Et felt kan lyve om hvad der faktisk læses" i denne fils øvrige afsnit).

**Ikke at forveksle med `PlayerData_Backend#114`.** #114 handler om en ANDEN fejl på den MODSATTE side af samme spørgsmål: et delt dokument, en klient afkoder til sin egen model og SKRIVER HELE TILBAGE (`kamp_opstilling`/`kamp_kamphaendelser`) — der forsvinder et felt, modellen ikke kender, fordi afkodning→model→genkodning per definition taber det, ingen vagt kan se. De tre udfald herover handler om at LÆSE et ENKELT, server-til-klient-svar (et API-respons, en push-payload) — ingen tilbageskrivning involveret, og mekanismen, der retter det (dual-key + en planlagt fjernelse), er en anden end #114's (bær ukendte nøgler uændret igennem). Begge er ægte, begge handler om "en nøgle klienten ikke genkender" — men de er to forskellige mekanismer på to forskellige dele af kredsløbet, og en rettelse af den ene løser ikke den anden.
