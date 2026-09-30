# PlayerData_Shared

Fælles kilde for ting, der **skal være identiske på tværs af platforme**, og som derfor ikke må have fire uafhængige mestre.

Trækkes ind som git submodule i `PlayerData_iOS`, `PlayerData_Android` og `PlayerData_Backend`, pinnet til en commit. En platform opgraderer bevidst og kan se, hvad der ændrede sig.

## Indhold

```
logic/vectors/    Testvektorer. Facit, alle platforme asserter imod.
```

## Hvorfor repoet findes

Backend regenererede vektorfilen, og **begge klienters kopi blev forældet inden for timer**. Begge tests var grønne mod en vektor, der ikke testede det, den var udvidet til at teste (2026-09-30, Issue #15).

Et submodule forhindrer utilsigtet forældelse. Det forhindrer **ikke**, at nogen pinner til en gammel commit og glemmer det — derfor skriver generatoren kravene ind i selve vektorfilen som et `kontrakt`-objekt, så hver platform asserter mod kildens egne krav i stedet for mod tal, de har skrevet af.

De to mekanismer løser hvert sit og skal begge blive.

## Hvad der IKKE ligger her endnu

- **Design-tokens** — kræver først en beslutning om, hvordan `Theme.swift` og `Theme.kt` genereres
- **API-kontrakten** — ligger i `PlayerData_Backend`, hvor koden henviser til den. Flytter, når der er en grund
- **`claude-md/`** — ligger midlertidigt i `PlayerData_Backend`. Hører her på sigt

Mindst mulig ny struktur til at løse et bevist problem.
