# Ordene bag #124: hvad der er engelsk, hvad der er dansk, og hvad der ikke kan afgoeres

**Flettet af koordinatoren 05-10-2026 fra ALLE TRE platformes egne gennemgange.**
Hver maalte paa sit eget traee, ord for ord, og doemte hvert ord mod dets
FAKTISKE brug — ikke mod stavemaaden.

```
Android    51 engelsk   26 dansk
iOS        61 engelsk   43 dansk
Backend   189 engelsk   34 dansk
```

## REGLEN

Et ord taeller **kun** som engelsk, hvis det staar her. **Ikke** fordi det staar
i `/usr/share/dict/words` eller i serverens skemaord — **begge heuristikker er
maalt utaette:**

```
serverens 186 skemaord   33 ER DANSKE   navn · dato · raekke · klub · hjemme
                                        ude · deltager · tid · afbud · aarsag
/usr/share/dict/words    laekker        er · alt · gang · mange · loft · mine
                                        tag · slip · art · hold · by · for
```

**Ordbogen og skemaordene er kandidat-generatorer, ikke autoriteter.** Backends
metode er formen: de kandidatord, der ramte en heuristik, blev LAEST og doemt
mod `word_to_names` — hvor ordet faktisk optraeder — og resten blev ikke doemt
paa formodning.

## KAN IKKE AFGOERES PAA EN LISTE — 7 ord

**Dette er IKKE en tabel over uenighed. Det er en tabel over ord, hvis betydning
afhaenger af NAVNET.** Og beviset er staerkest for de to, hvor begge betydninger
findes **inden for EEN kodebase:**

| ord | Android | iOS | Backend | beviset |
|---|---|---|---|---|
| `side` | — | — | BEGGE | `effektiv_maal_side` er team-SIDE (eng.) · `bekraeft_email_side` er webSIDE (da.) |
| `min` | DANSK | ENG | BEGGE | `_min_krav` er minimum (eng.) · `slet_min_konto` er "min" (da.) |
| `sort` | DANSK | DANSK | ENG | `sortFarve` er SORT farve (da.) · `_sort_key`/`sort_events` er sorterings-verbet (eng.), **nul danske forekomster i backend** |
| `point` | ENG | DANSK | — | dansk "point" (= points) · engelsk "point" |
| `by` | DANSK | ENG | ENG | dansk "by" (= city) · engelsk praeposition |
| `for` | DANSK | ENG | ENG | dansk "for" (= for/too) · engelsk "for" |
| `post` | DANSK | — | ENG | dansk "post" (= en raekke/en post) · engelsk HTTP POST |

**`side` og `min` er de vigtigste**, fordi de viser, at problemet ikke er to
platforme, der er uenige. **Det er ét ord, der betyder to ting i samme fil.**

**Vaerktoejet skal sende navnet til springe-listen, uanset hvilken side ordet
staar paa.** Et menneske afgoer det paa NAVNET.

> Et ord, der findes i begge sprog med forskellig betydning, kan ikke afgoeres
> paa listen. Det afgoeres paa navnet.

## GODKENDT ENGELSK — 239 ord

26 af dem er godkendt af **alle tre uafhaengigt.**

  absence        access         account        activity       add            admin          alarm        
  alert          alias          android        api            app            apple          assist       
  assists        association    at             background     backup         badge          ca           
  cache          card           category       certificate    change         checkpoint     chevron      
  chip           clamped        clear          client         compute        connection     content      
  cookie         count          create         current        custom         daily          data         
  date           db             dbu            decimal        decode         delete         deleted      
  detect         diff           discover       distance       distinct       doc            drift        
  email          entry          epoch          error          event          events         expired      
  export         face           families       family         fetch          filter         find         
  first          flag           flow           form           format         formation      full         
  get            global         goals          gps            guide          handle         hash         
  header         headers        hex            holder         home           html           http         
  id             impact         in             int            interval       ios            ip           
  is             json           key            km             label          labels         legacy       
  lifespan       limit          links          list           live           log            login        
  logout         loop           mail           manifest       match          me             minute       
  minutes        monthly        ms             name           next           no             none         
  now            ok             one            order          out            parse          password     
  patch          payload        per            person         pick           ping           player       
  pm             position       preview        prompt         purge          push           rate         
  raw            read           reason         refresh        relevant       repository     request      
  resolve        response       rest           restore        row            rules          run          
  save           scan           score          scorer         search         seconds        secret       
  secrets        secure         seed           segment        selection      send           server       
  service        session        sessions       set            sheet          shell          site         
  slot           stage          start          state          stats          status         stop         
  strip          substitution   sync           table          tags           team           teams        
  text           to             toggle         token          totals         training       type
  total          veo            init
  stat
  test           tests          link           rule           goal           second         filters
  unknown        wrong          invalid        attempt        registration   registrations  file
  basis          kickoff        markdown       meter          neutral        offline        participated   platform       selected       slots          started        theme          trend        
   
  types          unique         until          updated        upload         url            value        
  verification   verify         version        vocabularies   wizard       

## MAALT DANSK — 66 ord

Ord der staar i ordbogen eller serverens skemaord, men **ER danske.**

  af             afbud          alder          alt            art            cachet         da           
  dato           deltager       er             fed            fri            frisk          gang         
  gem            gemmer         gule           halve          handling       hjemme         hold         
  i              ind            kampdato       kampnr         kant           klub           knap         
  lager          linje          loft           mange          mangler        marker         med          
  mig            mine           mod            navn           nu             ny             og           
  op             pause          placer         positioner     praefiks       prognose       raekke       
  rang           registrer      sendt          skift          slip           slut           standard     
  stat           stilling       tag            tal            tid            tom            trin         
  troeje         typer          ude          

### De farligste: de laeser rigtigt og betyder noget andet

**En hybrid er synlig for en laeser. Disse er det ikke.**

```
sort        dansk BLACK      sortFarve er en SORT farve, ikke en sorteret
standard    dansk DEFAULT    STANDARD_FARVE er standardfarven
loft        dansk CAP        et loft er en graense — fundet af BAADE Android og Backend
handling    dansk ACTION     ikke haandtering
halve       dansk "halve op" halve_op er en AFRUNDINGSREGEL, ikke "to halve"
hold        dansk TEAM       _hold_navn_for_niveau er team-navn, ikke "at holde"
lager       dansk STORAGE    _opstilling_doc_fra_lager er "fra lager", ikke oel
da          dansk SPROGKODE  display_da er "visning, dansk" — ikke et engelsk ord
marker      dansk BYDEFORM   markerHalvleg = "markér halvlegen". Stod 0 gange i
                             prosaen, saa enhver frekvensmaaling missede den
```

## OVERDETEKTION — hvorfor et ord rammer heuristikken, ikke en TREDJE kategori

Forkortelser, varemaerker og fragmenter rammer heuristikkerne, men skal ikke
omdoebes: `repo`, `dev`, `dsl`, `hid`, `api`, `json`, og enkeltbogstav-fragmenter som
`b` i `b64url` og `v` i `v1`.

### RETTET 06-10 kl. 01:30: `dbu` og `veo` er FLYTTET til GODKENDT ENGELSK

**`dbu` stod i BEGGE afsnit, og `veo` kun her. Det kostede navne.**

De to kategorier har forskellig virkning paa et navn, og det er ikke aabenlyst:

```
godkendt engelsk   BEHOLD ordet, omdoeb RESTEN     dbu_kampnr -> dbu_match_number
overdetektion      "skal ikke omdoebes"            kan laeses som: SPRING
                                                   HELE navnet over
```

**For en forkortelse INDE i et sammensat navn er "godkendt engelsk" den
rigtige opfoersel.** Beviset er Backends egen omdoebning:
`hent_veo_links -> fetch_veo_links` — `veo` beholdt, `hent` omdoebt. Det er
praecis, hvad den godkendte liste giver, og det modsatte af at springe navnet
over.

Android maalte 06-10, at **`dbu` blokerer 17 af deres navne** — flest af alle
undtagen `opstilling`. Om det skyldes denne modsigelse, ved jeg ikke: jeg har
maalt FILEN, ikke deres parser, og jeg har een gang i nat paastaaet noget om
tre sessioners vaerktoejer, jeg ikke havde maalt. **Spoergsmaalet er sendt til
dem.**

**Afsnittet her er derfor en BEGRUNDELSE, ikke en kategori.** Et ord staar
antingen i GODKENDT ENGELSK (behold det) eller i MAALT DANSK (omdoeb det) eller
i mapningens tvetydige afsnit. Dette afsnit siger kun, HVORFOR nogle ord
rammer heuristikkerne forkert.

## HVAD DER IKKE HOERER HER

**Kollisioner er per platform.** Backends maaling:

```
er    -> is      RESERVERET i Python
i/ind -> in      RESERVERET i Python — TO danske ord rammer SAMME reserverede ord
med   -> with    RESERVERET i Python
pause -> break   reserveret i ALLE TRE sprog
tid   -> time    BLOED kollision — stdlib-modulnavn, ikke reserveret
```

Og Swifts `View.body`, SwiftUIs `List`/`Text`, Kotlins soft keywords er hver
sine. Androids maaling: *"et ord, der kolliderer paa een platform, kolliderer
ikke paa alle. En unoedig springe-liste ser ud som forsigtighed."*

```
SPROG      hoerer HER      et ord er dansk eller engelsk, uanset repo
SYNTAKS    hoerer DER      hvad der kan staa som et navn i Swift/Kotlin/Python
```

## ET HYBRID-EKSEMPEL, FUNDET I NAVNET SELV

`rang_for` — dansk `rang` + engelsk `for`. **Navnet var en hybrid, foer nogen
omdoebte noget.** Det er den klasse, hybrid-reglen findes for at fange.
