# PlayerData_Shared

Fælles kilde for ting, der skal være identiske på tværs af platforme, og som derfor ikke må have flere uafhængige mestre.

Trækkes ind som git submodule i klient- og server-repoerne, pinnet til en commit. En platform opgraderer bevidst og kan se i diffen, hvad der ændrede sig.

## Indhold

```
logic/vectors/    Testvektorer. Facit, alle platforme asserter imod.
claude-md/        DELT-BLOK.md — den fælles blok, ét sted (Issue #139).
contract/         contract.json — API-kontrakten som versioneret build-
                  time-artefakt (Issue #134), genereret af
                  PlayerData_Backend/scripts/generate-contract.py.
```

## Hvorfor repoet findes

Facit blev regenereret ét sted, og kopierne hos de øvrige platforme blev forældet inden for timer. Alle tests var grønne — mod en vektor, der ikke længere testede det, den var udvidet til at teste.

Fejlen var ikke, at nogen glemte at kopiere. Fejlen var, at en kopi kan være forældet uden at det kan ses.

## To mekanismer, ikke én

Et submodule forhindrer **utilsigtet** forældelse. Det forhindrer ikke, at nogen pinner til en gammel commit og glemmer det.

Derfor skriver generatoren kravene ind i selve vektorfilen som et `kontrakt`-objekt, så hver platform asserter mod kildens egne krav i stedet for mod tal, de har skrevet af. En kopi kan så ikke bestå sin egen forældede kontrakt.

De to løser hvert sit problem og skal begge blive.

## To fælder, et submodule ikke fjerner

**Et submodule følger ikke med en almindelig `git clone`.** Brug `--recurse-submodules`, eller `git submodule update --init` bagefter. I CI: `submodules: true` på checkout-trinnet — standarden er `false`, og resultatet er en tom mappe, ikke en fejl.

**Byggesystemer sporer ikke en fil, du læser fra disken i en test**, medmindre du siger det. Deklarér vektorfilen som eksplicit inddata til testopgaven. Ellers står opgaven up-to-date efter en submodule-opdatering og består mod den gamle vektor.

Et submodule flytter kilden. Det fjerner ikke en cache, der ikke ved, at kilden har flyttet sig.

## Sådan efterprøver du, at din test faktisk læser den

Ændr en forventet værdi i vektorfilen, kør testen, og se den blive rød. Stil den tilbage.

Består testen uændret, læser den ikke filen — og så beviser den ingenting.
