# Nytt i PxWebApi versjon 2

## Viktigste nyhet: GET URL-støtte
Innføringen av GET URL-støtte. Det gjør API-et enklere å integrere.

**Merk:** Versjon 2 er ikke bakoverkompatibel med versjon 1, og det er også nytt format for bruk av HTTP POST. 

---

## Forbedrede filtre og søkemuligheter

**Bedre wildcards:**
Du kan nå maskere enkeltegn med `?`, i tillegg til eksisterende `*` for flere tegn.

**Nye filtre:** 
- `from()` – hent data fra og med et startpunkt
- `range([,])` – definer et spesifikt intervall, f.eks. fra kommuneliste
- `to()` - til og med (inclusive)
- `bottom()` - motsatt av det eksisterende `top()`

---

## Mer fleksibrel henting av data

- Hent forhåndsdefinerte datasett uten å spesifisere parametere
- Flere metadata i JSON-stat2, bl.a. fotnoter (gjelder også versjon 1)
- Metadata i API vises nå som JSON-stat2 
- Kodelister i metadata. Tilgjengelig via `Codelists` (kan gi komplekse URL-er)

---

## Layout-styring for CSV, XLSX og HTML

HTML format er nytt.

**Nye parametere gir full kontroll:**

**Visningsalternativer:**
- `UseCodes` – vis kun koder
- `UseTexts` – vis kun tekst
- `UseCodesAndTexts` – vis både koder og tekst
- `IncludeTitle` – inkluder tabelltittel

**Strukturkontroll:**
- `stub` – bestem hvilke variabler som vises i forspalten
- `heading` – bestem hvilke variabler som vises i tabellhodet
- **Tips:** Plasser alle variabler i `stub` for å få en pivotvennlig tabell

**CSV-skilletegn:**
- `SeparatorTab` – tabulator
- `SeparatorSpace` – mellomrom
- `SeparatorSemicolon` – semikolon

**Eksempel:**
```
outputformat=csv
outputformatparams=separatorsemicolon,usecodesandtexts
heading=ContentsCode
stub=VareGrupper2,Tid
```

---


## Kjente begrensninger

### 1. Statiske URL-er fra Statistikkbanken
URL-er generert i Statistikkbanken er statiske og inkluderer ikke automatisk fremtidige tall. Dette gjør dem:
- Uoversiktlige og vanskelige å vedlikeholde
- Nødvendig å redigere manuelt for å få oppdaterte tall

Jeg har laget et optimeringsverktøy: https://nesa.no/ssb/forenkle_url.html

### 2. URL begrensning på lengde
- Maksimal lengde: ~2000 tegn
- Spesielt problematisk for korttidsstatistikker med lange tidsserier. For månedsstatistikk går grensen litt før Finanskrisen.


---
