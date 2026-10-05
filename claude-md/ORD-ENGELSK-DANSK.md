# Ordene bag #124: hvad der er engelsk, hvad der er dansk, og hvad der ikke kan afgoeres

**Flettet af koordinatoren 05-10-2026 fra Androids og iOS' egne gennemgange.**
Begge maalte paa deres eget traee, ord for ord. **Backend har ikke maalt endnu** —
deres liste flettes ind, naar de har.

Hjemmet er her, saa de 76+ ord ikke maales tre gange. **Men listen er ikke en
autoritet, der kan overtages uden at laese:** de fire ord under "kan ikke
afgoeres" er beviset paa, at et ord kan vaere engelsk i eet traee og dansk i et
andet.

## REGLEN, som goer listen brugbar

Et ord taeller **kun** som engelsk, hvis det staar her — ikke fordi det staar i
`/usr/share/dict/words` eller i serverens skemaord. **Begge heuristikker laekker
dansk**, maalt: 33 af serverens 186 skemaord ER danske (`navn`, `dato`,
`raekke`, `klub`, `hjemme`, `ude`, `deltager`, `tid`...), og ordbogen indeholder
`er`, `alt`, `gang`, `mange`, `loft`, `mine`, `tag`, `slip`.

**Ordbogen og skemaordene er kandidat-generatorer, ikke autoriteter.**

## KAN IKKE AFGOERES PAA EN LISTE — 4 ord

**Disse findes i BEGGE sprog med forskellig betydning**, og Android og iOS naaede
modsatte svar paa dem. **Ingen af dem tog fejl.**

| ord | dansk | engelsk | Android | iOS |
|---|---|---|---|---|
| `point` | point (= points) | point | ENGELSK | DANSK |
| `by` | by (= city) | by (praeposition) | DANSK | ENGELSK |
| `for` | for (= for/too) | for | DANSK | ENGELSK |
| `min` | min (= my) | min (minimum/minute) | DANSK | ENGELSK |

**Vaerktoejet skal sende navnet til springe-listen, uanset hvilken side ordet
staar paa.** Et menneske afgoer det paa NAVNET: `pointAntal` er point;
`sorteretBy` er engelsk; `minSpiller` er "min spiller"; `minVarighed` er
minimum.

> Et ord, der findes i begge sprog med forskellig betydning, kan ikke afgoeres
> paa listen. Det afgoeres paa navnet.

## GODKENDT ENGELSK — 72 ord

Gennemgaaet ord for ord af Android (51) og iOS (61), **36 af dem af begge
uafhaengigt.**

  absence         alarm           alert           api             app             assist        
  badge           cache           chevron         chip            data            dbu           
  distance        email           face            filter          flow            form          
  formation       gps             hex             id              interval        json          
  key             km              label           labels          legacy          live          
  login           match           me              minutes         ms              ok            
  order           parse           payload         per             person          ping          
  pm              position        preview         prompt          push            reason        
  repository      rest            rules           score           scorer          segment       
  selection       server          session         side            slot            start         
  status          stop            sync            toggle          token           totals        
  training        type            upload          value           version         wizard        

## MAALT DANSK — 49 ord

Ord der staar i ordbogen eller i serverens skemaord, men **ER danske.**

  alt             art             cachet          dato            deltager        er            
  fed             gang            gemmer          gule            handling        hjemme        
  i               ind             kant            klub            knap            linje         
  loft            mange           mangler         marker          med             mine          
  navn            nu              ny              og              op              placer        
  positioner      post            prognose        raekke          registrer       sendt         
  skift           slip            sort            standard        stat            tag           
  tal             tid             tom             trin            troeje          typer         
  ude           

**Fire af dem er vaerre end resten**, fordi de laeser rigtigt og betyder noget
andet:

```
sort        dansk BLACK      sortFarve er en SORT farve, ikke en sorteret
standard    dansk DEFAULT    STANDARD_FARVE er standardfarven
loft        dansk CAP        et loft er en graense
handling    dansk ACTION     ikke haandtering
```

**En hybrid er synlig for en laeser. `sortFarve -> sortColor` er det ikke** — den
ser ud som korrekt engelsk og betyder det modsatte. Androids fund.

Og `marker` er en dansk bydeform (`markerHalvleg` = "markér halvlegen"). Den
stod **0 gange** i Androids prosa, saa enhver frekvensmaaling missede den. **Kun
laesningen fandt den.**

## HVAD DER IKKE HOERER HER

**Kollisioner er per platform.** `break` er reserveret i alle tre sprog, men
`set`, `body`, `list`, `text`, `from` kolliderer kun paa nogle. Androids maaling:
*"et ord, der kolliderer paa een platform, kolliderer ikke paa alle. En unoedig
springe-liste ser ud som forsigtighed."* **De hoerer i hver platforms eget
vaerktoej.**

```
SPROG      hoerer HER        et ord er dansk eller engelsk, uanset repo
SYNTAKS    hoerer DER        hvad der kan staa som et navn i Swift/Kotlin/Python
```
