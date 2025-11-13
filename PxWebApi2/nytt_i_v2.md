# Nytt i PxWebApi versjon 2

## GET URL-støtte
Viktigst er innføringen av GET URL-støtte. Det gjør API-et enklere å integrere.

**Merk:** Versjon 2 er ikke bakoverkompatibel med versjon 1, og  

---

## Forbedrede filtre og søkemuligheter

**Bedre maskeringstegn:**
Du kan nå maskere ett og ett tegn med `?`, i tillegg til det eksisterende `*` for flere tegn.

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

## Layout styring for CSV, XLSX og HTML

**Nye parametere gir full kontroll og fleksibilitet:**

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

**HTML format** er nytt. Norsk har tusenskille mellomrom, engelsk komma. Kan stiles, f.eks. :

```
	<style type="text/css">
	    th[scope="col"] {
	        text-align: center;
	    }
	    th[scope="row"] {
	        text-align: left;
	    }
	    td {
	        text-align: right;
	    }
	    caption {
	    	font-weight: bold;
	    }
	</style>
```

## Annet nytt
Det er mer metadata i JSON-stat2, slik som fotnoter. Dette gjelder også for API v1.
Det er også nytt format for bruk av HTTP POST.
Det er også kjekt å vite at URL-ene ikke ser ut til å være sensitive for store og små bokstaver.

---

## Kjente begrensninger

### 1. Statiske URL-er fra Statistikkbanken

URL-er generert i Statistikkbanken er statiske og inkluderer ikke automatisk fremtidige tall. Dette gjør dem:

- Uoversiktlige og vanskelige å vedlikeholde
- Nødvendig å redigere manuelt for å få oppdaterte tall, ved å legge inn filtrene from() og top().

Jeg har laget et optimaliseringsverktøy: https://nesa.no/ssb/forenkle_url.html

### 2. URL begrensning på lengde

- Maksimal lengde: ~2000 tegn
- Kan være problematisk for korttidsstatistikker med lange tidsserier. For månedsstatistikker går grensen litt før Finanskrisen, om de ikke rettes. 

---
Se også [SSBs brukerveiledning til API V2](https://www.ssb.no/en/api/statbank-pxwebapi-user-guide)

SSBs R pakke [PxWebApiData](https://cran.r-project.org/package=PxWebApiData) er også oppdatert til å håndere V2 URLer.
