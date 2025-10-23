


# PxWebApi 2.0  
# Brukerhåndbok for PxWebApi v2 - mangelfull brukerveiledning - basert på utkast fra SCB
## Hva denne håndboken dekker  
Dette dokumentet spesifiserer forespørslene og responsformatene, samt endepunktene for **PxWebApi 2.0 beta** hos SSB.

## _NB! Denne er en forløpig oversettelse og bearbeiding_ av  https://github.com/PxTools/PxApiSpecs/blob/master/archive/specs.md . Kan benyttes i tilleg til SSBs brukerveiledning.

# Konsepter
**PxWebApi** bruker noen abstraksjoner for å representere ulike deler av den statistiske databasen. Dette avsnittet gir leseren en bedre forståelse av disse abstraksjonene som grunnlag for å forstå strukturen i API-et og dataene som tilbys.

## Tabell
En **tabell** er representasjonen av en statistisk tabell. Andre kan referere til det samme konseptet som et _datasett_, _kube_ eller _flerdimensjonal kube_. Vi har valgt å kalle det en **tabell** av historiske grunner. En tabell består av to deler: dataene som inneholder tallene, og metadataene som gir dataene kontekst eller mening. F.eks. data `5 550 203` og metadataene "befolkningen i Norge 1. januar 2024."

## Database
En **database** er en samling av tabeller som er organisert og strukturert i henhold til et skjema. En vanlig feil er å blande konseptet database med et relasjonsdatabasesystem som **Oracle Database** eller **Microsoft SQL Server**. Av og til refererer "database" også til en instans av databasen.

## Mappe
**Mapper** (Folder) brukes til å organisere tabeller i en database. En mappe kan inneholde andre mapper eller tabeller. Vanligvis refererer det første nivået med mapper i en database til de tematiske områdene i databasen. Andre kan også kalle dem "temaer" eller "emner".


## Variabel
Tabeller er flerdimensjonale, og **variabler** er konseptet som brukes til å beskrive dataene. Mange kan referere til variabler som _dimensjoner_. Ta eksempelet med dataene `5 550 203` og metadataene "befolkningen i i Norge 1. januar 2024". `5 550 203` beskrives av tre variabler:  
1. Hva vi måler. I vårt tilfelle er dette befolkningen. Denne variabelen er spesiell og refereres til som **statistikkvariabel**. Andre kan benevne denne som _målevariabel_ eller _innholdsvariabel_. 
2. Tidspunktet tallet er assosiert med. I vårt tilfelle er det 31. desember 2021. Denne typen variabel er også spesiell og refereres til som **tidsvariabel**. Det skal alltid være én og kun én tidsvariabel.  
3. Region. I vårt tilfelle er det Norge. Dette er også en spesiell type variabel kalt **geografisk variabel**. En tabell kan ha null eller flere geografiske variabler, men vanligvis bare én hvis de har noen.  

Det finnes en fjerde type variabel, bare kalt **variabel**. Den brukes til å beskrive dataene. Tenk deg at vi hadde delt `5 550 203` etter kjønn slik at vi hadde to dataceller i stedet, `2 795 718` og `2 754 485`, én for menn og én for kvinner. Da ville den fjerde variabelen være kjønn.

## Verdi
Variabler har distinkte verdier som utgjør rommet for dem. F.eks. har vår kjønnsvariabel ovenfor to verdier: én for menn og én for kvinner.

## Kodeliste
En variabel kan ha en tilknyttet **kodeliste** som definerer et nytt rom for variabelen ved å tilby forskjellige sett med verdier. F.eks. forestill deg at du har en regional variabel med verdier for hver kommune i Norge. Da kan du ha en kodeliste som transformerer kommunene til fylker.

## Datacelle
En datacelle er den individuelle målingen i en tabell. Det totale antallet celler for en tabell gis av produktet av antall verdier for hver variabel.

## Datakildetype
Informasjonen kan lagres i ulike formater og teknologier. Vi støtter for øyeblikket to forskjellige datakildetyper: **PX-filbaserte** og **Oracle-databaser** samt **Microsoft SQL Server**, som bruker **Common Nordic Meta Model**.

## Annen terminologi
- **Paxiom**: Objektmodellen som representerer en tabell.  
- **CNMM**: Common Nordic Meta Model er en relasjonsdatamodell for å representere en `database` og `tabeller`. Den brukes og vedlikeholdes av Statistikkbyråene i Sverige, Norge og Danmark.  
- **PX-fil**: En fysisk representasjon av en tabell ved bruk av [PX-filformatet](https://www.scb.se/en/services/statistical-programs-for-px-files/px-file-format/).  
- **PX-fil-database**: En samling av PX-filer som representerer en `database`.  



# API-endepunkter

Url-ene i endepunktet er uavhengig av store og små bokstaver.

**POST** er primært ment for å hente data når forespørselen for å velge data kan bli svært stor. I noen tilfeller kan URL-en overskride det maksimale antallet tegn som er tillatt for en URL. I slike tilfeller kan en http **POST-forespørsel** være løsningen. 

## Konfigurasjonsendepunkt
Hent API-konfigurasjonsinnstillinger. Instanser av API-et kan være konfigurert forskjellig fra hverandre. Klienter kan bruke dette endepunktet for å hente informasjon som kan være nyttig for å styre oppførselen til klienten.  
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/config
```

### Eksempel på respons
```json
{
  "apiVersion": "2.0.0",
  "appVersion": "2.0.1+build.10",
  "languages": [
    {
    "id": "no",
    "label": "Norsk"
    },
    {
    "id": "en",
    "label": "English"
    }
    ],
  "defaultLanguage": "no",
  "maxDataCells": 800000,
  "maxCallsPerTimeWindow": 30,
  "timeWindow": 60,
  "license": "https://www.ssb.no/en/diverse/lisens",
  "sourceReferences": [
    {
    "language": "en",
    "text": "Source: Statistics Norway"
    },
    {
    "language": "no",
    "text": "Kilde: Statistisk sentralbyrå"
    }
    ],
  "defaultDataFormat": "json-stat2",
  "dataFormats": [
    "json-stat2",
    "csv",
    "px",
    "xlsx",
    "html",
    "json-px",
    "parquet"
    ],
  "features": [
    {
    "id": "CORS",
    "params": [
      {
      "key": "enabled",
      "value": "True"
      }
      ]
  }
  ]
}
```

- **apiVersion** angir versjonen av API-et.  
- **languages** lister språkene som kan brukes når man gjør forespørsler til API-et. Nesten alle endepunkter støtter en forespørselsparameter for språk (`lang`) som kan settes til `id` for språket, f.eks. `lang=en` for engelsk.  
- **defaultLanguage** angir hvilket språk som brukes i responsen når ingen språk er angitt.  
- **maxDataCells** spesifiserer maksimalt antall dataceller som kan hentes i én forespørsel.  
- **maxCallsPerTimeWindow** spesifiserer hvor mange forespørsler som kan gjøres i løpet av en tidsramme definert av **timeWindow**.  
- **timeWindow** angir varigheten på tidsrammen, i sekunder, som brukes i beskyttelsen mot overbelastning.  
- **sourceReferences** spesifiserer hvordan man kan sitere data hentet gjennom API-et.  
- **license** spesifiserer lisensen for dataene.  


## Navigasjonsendepunkter - NB! foreløpig ikke implementert i V2

Bla gjennom databasestrukturen.

### Returnerer rotmappen for databasen
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/navigation
```

#### Parametere  
##### lang  
En valgfri språkparameter.

#### Eksempel på respons
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/navigation?lang=en
```

```json
{
  "language": "en",
  "id": "",
  "label": "",
  "description": "",
  "folderContents": [
    {
      "type": "FolderInformation",
      "id": "al",
      "label": "Labour market and earnings",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/al?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "bf",
      "label": "Banking and financial markets",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/bf?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "vf",
      "label": "Establishments, enterprises and accounts",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/vf?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "be",
      "label": "Population",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/be?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "bb",
      "label": "Construction, housing and property",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/bb?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "ei",
      "label": "Energy and manufacturing",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/ei?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "he",
      "label": "Health",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/he?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "if",
      "label": "Income and consumption",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/if?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "in",
      "label": "Immigration and immigrants",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/in?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "js",
      "label": "Agriculture, forestry, hunting and fishing",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/js?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "kf",
      "label": "Culture and recreation",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/kf?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "nk",
      "label": "National accounts and business cycles",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/nk?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "nm",
      "label": "Nature and the environment",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/nm?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "os",
      "label": "Public sector",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/os?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "pp",
      "label": "Prices and price indices",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/pp?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "sk",
      "label": "Social conditions, welfare and crime",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/sk?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "sv",
      "label": "Svalbard",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/sv?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "ti",
      "label": "Technology and innovation",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/ti?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "tr",
      "label": "Transport and tourism",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/tr?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "ud",
      "label": "Education",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/ud?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "ut",
      "label": "External economy",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/ut?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "va",
      "label": "Elections",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/va?lang=en"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "vt",
      "label": "Wholesale and retail trade and service activities",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "en",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/vt?lang=en"
        }
      ]
    }
  ],
  "links": [
    {
      "rel": "self",
      "hreflang": "en",
      "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/?lang=en"
    }
  ]
}
```

### Returner innholdet i en spesifikk mappe i databasen
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/navigation/{id}
```  

Returnerer databasemappen identifisert av **id**.  

#### Parametere  
##### lang  
En valgfri språkparameter.

#### Eksempel på respons
Dette eksempelet viser responsen fra API-forespørselen `https://data.ssb.no/api/pxwebapi/v2/navigation/pp`. Metadata om mappen **pp** returneres sammen med innholdet i mappen **Priser og prisindekser**.

```json
{
  "language": "no",
  "id": "pp",
  "label": "Priser og prisindekser",
  "description": "",
  "folderContents": [
    {
      "type": "FolderInformation",
      "id": "pp01",
      "label": "Boligpriser og boligprisindekser",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/pp01?lang=no"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "pp02",
      "label": "Byggekostnadsindekser",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/pp02?lang=no"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "pp04",
      "label": "Konsumpriser",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/pp04?lang=no"
        }
      ]
    },
    {
      "type": "FolderInformation",
      "id": "pp05",
      "label": "Produsent- og engrosprisindekser",
      "description": "",
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/pp05?lang=no"
        }
      ]
    }
  ],
  "links": [
    {
      "rel": "self",
      "hreflang": "no",
      "href": "https://data.ssb.no/api/pxwebapi/v2/navigation/PP?lang=no"
    }
  ]
}
```

**Responsen forklart**  

Navigasjonsendepunktet returnerer to objekter:  
1. Et **Folder**-objekt som inneholder metadata om mappen som det ble spurt om.  
2. En liste **folderContents** som inneholder innholdet i mappen (undermapper og statistiske tabeller).

Det er tre mulige verdier for **objectType**:  
1. **Folder** – Mappen som det ble spurt om i API-forespørselen.  
2. **FolderInformation** – En undermappe til **Folder**-objektet.  
3. **Table** – En statistisk tabell lokalisert i mappen.

**Metainformasjon for mapper**  

- **id** – Mappe-ID.  
- **objectType** – Kan ha én av to mulige verdier:  
  - **Folder** (mappen som det ble spurt om i API-forespørselen).  
  - **FolderInformation** (undermappe).  
  - **Heading** – En overskrift for å skille innholdet.  
- **label** – Mappetekst.  
- **description** – Beskrivelse av mappen.  
- **tags** – Tagger for mappen (ikke implementert ennå).  
- **links** – Hvordan navigere til mappen.  

**Metainformasjon for tabeller**  

- **id** – Tabell-ID.  
- **objectType** – Vil ha verdien "Table".  
- **label** – Tekst for tabellen.  
- **description** – Beskrivelse av tabellen.  
- **updated** – Når tabellen sist ble oppdatert.  
- **category** – Presentasjonskategori for tabellen. Mulige verdier er:  
  	- internal  
  	- official  
  	- private  
  	- section  
- **firstPeriod** – Den første tidsperioden for dataene i tabellen.  
- **lastPeriod** – Den siste tidsperioden for dataene i tabellen.  
- **discontinued** – Om tabellen vil bli oppdatert med nye data eller ikke.  
- **tags** – Tagger for tabellen (ikke implementert ennå).  
- **links** – Hvordan navigere til tabellen. For tabeller finnes det tre lenker:  
  	- **self** – Hvordan navigere til tabellen.  
  	- **metadata** – Hvordan navigere til tabellens metadata.  
  	- **data** – Hvordan navigere til tabellens data.  


## Tabellendepunkter

### Liste over alle tabeller
Viser alle tabeller i databasen. Listen kan filtreres ved hjelp av forskjellige parametere.  

```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/tables/
```

#### Parametere
Du kan begrense tabellene som returneres ved hjelp av følgende parametere:

##### lang
En valgfri språkparameter.

##### query
Velger bare tabeller som samsvarer med et kriterium angitt i søkeparameteren.  Søk er basert på Lucene .Net, og gir mange avanserte søkemuligheter. Se [syntaks for søk](https://lucenenet.apache.org/docs/4.8.0-beta00017/api/queryparser/Lucene.Net.QueryParsers.Classic.html).
```
https://data.ssb.no/api/pxwebapi/v2/tables?query=befolkning
```

##### pastDays
Velger bare tabeller som er oppdatert et visst antall dager før forespørselens tidspunkt. Gyldige verdier for **pastDays** er heltall mellom 1 og ?. *(vil returnere feil hvis ukjent verdi)*.  
```
https://data.ssb.no/api/pxwebapi/v2/tables?pastDays=5
```

##### includeDiscontinued
`true` eller `false`, avhengig av om utgåtte tabeller skal inkluderes. Tabeller som ikke eksplisitt har satt egenskapen **discontinued**, behandles som om de ikke er utgått.  

##### pageSize
Hvor mange treff eller tabeller som skal inkluderes på en side av responsen.

##### pageNumber
Et tall som spesifiserer hvilken side av responsen som skal vises. Standardverdien er 1.

#### Eksempel på respons
Et eksempel som filtrerer på "befolkning" til side 2 med en sidestørrelse på 3:  
```json
{
  "language": "no",
  "tables": [
    {
      "type": "Table",
      "id": "12631",
      "label": "12631: Andel av befolkningen (mellom 16 og 79 år) på ferie (prosent) 2013-2023",
      "description": "",
      "updated": "2024-02-26T07:00:00Z",
      "firstPeriod": "2013",
      "lastPeriod": "2023",
      "category": "public",
      "variableNames": [
        "feriemål",
        "statistikkvariabel",
        "år"
      ],
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/12631?lang=no"
        },
        {
          "rel": "metadata",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/12631/metadata?lang=no&outputFormat=json-px"
        },
        {
          "rel": "metadata",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/12631/metadata?lang=no&outputFormat=json-stat2"
        },
        {
          "rel": "data",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/12631/data?lang=no&outputFormat=json-stat2"
        }
      ]
    },
    {
      "type": "Table",
      "id": "13760",
      "label": "13760: Arbeidsstyrken, sysselsatte, arbeidsledige og utførte ukeverk, etter kjønn, alder og type justering. Bruddjusterte tall 2006M01-2024M09",
      "description": "",
      "updated": "2024-10-24T06:00:00Z",
      "firstPeriod": "2006M01",
      "lastPeriod": "2024M09",
      "category": "public",
      "variableNames": [
        "kjønn",
        "alder",
        "type justering",
        "statistikkvariabel",
        "måned"
      ],
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/13760?lang=no"
        },
        {
          "rel": "metadata",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/13760/metadata?lang=no&outputFormat=json-px"
        },
        {
          "rel": "metadata",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/13760/metadata?lang=no&outputFormat=json-stat2"
        },
        {
          "rel": "data",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/13760/data?lang=no&outputFormat=json-stat2"
        }
      ]
    },
    {
      "type": "Table",
      "id": "05803",
      "label": "05803: Endringer i befolkningen i løpet av året 1735-2024",
      "description": "",
      "updated": "2024-02-21T07:00:00Z",
      "firstPeriod": "1735",
      "lastPeriod": "2024",
      "category": "public",
      "variableNames": [
        "statistikkvariabel",
        "år"
      ],
      "links": [
        {
          "rel": "self",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/05803?lang=no"
        },
        {
          "rel": "metadata",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/05803/metadata?lang=no&outputFormat=json-px"
        },
        {
          "rel": "metadata",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/05803/metadata?lang=no&outputFormat=json-stat2"
        },
        {
          "rel": "data",
          "hreflang": "no",
          "href": "https://data.ssb.no/api/pxwebapi/v2/tables/05803/data?lang=no&outputFormat=json-stat2"
        }
      ]
    }
  ],
  "page": {
    "pageNumber": 2,
    "pageSize": 3,
    "totalElements": 363,
    "totalPages": 121,
    "links": [
      {
        "rel": "next",
        "hreflang": "no",
        "href": "https://data.ssb.no/api/pxwebapi/v2/tables/?lang=no&query=befolkning&pagesize=3&pageNumber=3"
      },
      {
        "rel": "previous",
        "hreflang": "no",
        "href": "https://data.ssb.no/api/pxwebapi/v2/tables/?lang=no&query=befolkning&pagesize=3&pageNumber=1"
      },
      {
        "rel": "last",
        "hreflang": "no",
        "href": "https://data.ssb.no/api/pxwebapi/v2/tables/?lang=no&query=befolkning&pagesize=3&pageNumber=121"
      }
    ]
  },
  "links": [
    {
      "rel": "self",
      "hreflang": "no",
      "href": "https://data.ssb.no/api/pxwebapi/v2/tables/?lang=no&query=befolkning&pagesize=3&pageNumber=2"
    }
  ]
}
```

**Respons forklart**  

- **Tables** – En liste over tabellobjekter som inneholder grunnleggende metadata for en tabell.  
- **Page** – Informasjon om paginering for resultatene:  
  - **pageNumber** – Sidetallet som ble returnert.  
  - **pageSize** – Antall resultater per side.  
  - **totalElements** – Det totale antallet tabeller.  
  - **totalPages** – Det totale antallet sider.  
  - **links** – Lenker til relevante sider: neste, forrige og siste side i resultatsettet.

**Merk**  
Informasjonen kan være hurtigbufret for å forbedre ytelsen. Dette kan føre til forsinkelser i responsen rett etter publisering av nye data.

### Liste grunnleggende informasjon for en tabell
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/tables/<table-id>
```

#### Parametere  
##### lang  
En valgfri språkparameter.

#### Eksempel på respons
Et eksempel som returnerer grunnleggende informasjon for tabellen **03013**:  
```json
{
  "type": "Table",
  "id": "03013",
  "label": "03013: Konsumprisindeks, etter konsumgruppe (2015=100) 1979M01-2024M10",
  "description": "",
  "sortCode": "000010",
  "updated": "2024-11-11T07:00:00Z",
  "firstPeriod": "1979M01",
  "lastPeriod": "2024M10",
  "category": "public",
  "variableNames": [
    "konsumgruppe",
    "statistikkvariabel",
    "måned"
  ],
  "links": [
    {
      "rel": "self",
      "hreflang": "no",
      "href": "https://data.ssb.no/api/pxwebapi/v2/tables/03013?lang=no"
    },
    {
      "rel": "metadata",
      "hreflang": "no",
      "href": "https://data.ssb.no/api/pxwebapi/v2/tables/03013/metadata?lang=no&outputFormat=json-px"
    },
    {
      "rel": "metadata",
      "hreflang": "no",
      "href": "https://data.ssb.no/api/pxwebapi/v2/tables/03013/metadata?lang=no&outputFormat=json-stat2"
    },
    {
      "rel": "data",
      "hreflang": "no",
      "href": "https://data.ssb.no/api/pxwebapi/v2/tables/03013/data?lang=no&outputFormat=json-stat2"
    }
  ],
  "language": "no"
}
```

**Merk**  
Informasjonen kan være hurtigbufret for å forbedre ytelsen, noe som kan føre til korte forsinkelser i responsen rett etter publisering av nye data.

### Liste metadata for en tabell
Vis metadata for en spesifisert tabell.  
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/tables/<table-id>/metadata
```

#### Parametere  
##### lang  
En valgfri språkparameter.

##### outputFormat
Ett av følgende formater: `json-px` eller `json-stat2`. Standardverdi vil bli spesifisert av konfigurasjonsendepunktet.

#### Eksempel på respons
Metadata for tabell med id **11118** som json-stat2

```json
{
  "version": "2.0",
  "class": "dataset",
  "href": "https://data.ssb.no/api/pxwebapi/v2/tables/11118/metadata?lang=no&outputFormat=json-stat2",
  "label": "11118: Konsumprisindeks for varer og tjenester, etter Leveringssektor, statistikkvariabel og år",
  "source": "Statistisk sentralbyrå",
  "updated": "2024-01-10T07:00:00Z",
  "link": {
    "data": [
      {
        "href": "https://data.ssb.no/api/pxwebapi/v2/tables/11118/data?lang=no&outputFormat=json-stat2"
      }
    ]
  },
  "note": [
    "F.o.m. 2017 er referanseår 2015=100. Se den avsluttede tidsserien 03363 for tall t.o.m 2015 etter gammel klassifisering av leveransesektor med 1998=100."
  ],
  "role": {
    "time": [
      "Tid"
    ],
    "metric": [
      "ContentsCode"
    ]
  },
  "id": [
    "Leveringssektor",
    "ContentsCode",
    "Tid"
  ],
  "size": [
    14,
    2,
    9
  ],
  "dimension": {
    "Leveringssektor": {
      "label": "Leveringssektor",
      "category": {
        "index": {
          "B1": 0,
          "B11": 1,
          "B111": 2,
          "B1111": 3,
          "B1112": 4,
          "B112": 5,
          "B12": 6,
          "B121": 7,
          "B122": 8,
          "B2": 9,
          "B21": 10,
          "B22": 11,
          "B221": 12,
          "B222": 13
        },
        "label": {
          "B1": "Varer",
          "B11": "Norske varer",
          "B111": "Norske varer uten energivarer",
          "B1111": "Norske jordbruksvarer",
          "B1112": "Norske varer uten jordbruks og energivarer",
          "B112": "Energivarer",
          "B12": "Importerte varer",
          "B121": "Importerte jordbruksvarer",
          "B122": "Importerte varer uten jordbruksvarer",
          "B2": "Tjenester",
          "B21": "Husleie",
          "B22": "Tjenester uten husleie",
          "B221": "Tjenester hvor arbeidskraft dominerer",
          "B222": "Tjenester med andre viktige priskomponenter"
        }
      },
      "extension": {
        "elimination": false,
        "show": "value",
        "codeLists": [ ]
      }
    },
    "ContentsCode": {
      "label": "statistikkvariabel",
      "category": {
        "index": {
          "LevIndAar": 0,
          "Aarsendring": 1
        },
        "label": {
          "LevIndAar": "Konsumprisindeks (2015=100)",
          "Aarsendring": "Årsendring (prosent)"
        },
        "unit": {
          "LevIndAar": {
            "base": "indeks",
            "decimals": 1
          },
          "Aarsendring": {
            "base": "prosent",
            "decimals": 1
          }
        }
      },
      "extension": {
        "elimination": false,
        "refperiod": {
          "LevIndAar": "Gjennomsnitt for året",
          "Aarsendring": "Gjennomsnitt for hvert år"
        },
        "show": "value",
        "codeLists": [ ]
      },
      "link": {
        "describedby": [
          {
            "extension": {
              "LevIndAar": "urn:ssb:conceptvariable:vardok:2755"
            }
          }
        ]
      }
    },
    "Tid": {
      "label": "år",
      "category": {
        "index": {
          "2015": 0,
          "2016": 1,
          "2017": 2,
          "2018": 3,
          "2019": 4,
          "2020": 5,
          "2021": 6,
          "2022": 7,
          "2023": 8
        },
        "label": {
          "2015": "2015",
          "2016": "2016",
          "2017": "2017",
          "2018": "2018",
          "2019": "2019",
          "2020": "2020",
          "2021": "2021",
          "2022": "2022",
          "2023": "2023"
        }
      },
      "extension": {
        "elimination": false,
        "show": "code",
        "codeLists": [ ]
      }
    }
  },
  "extension": {
    "px": {
      "infofile": "None",
      "tableid": "11118",
      "decimals": 1,
      "official-statistics": true,
      "aggregallowed": false,
      "language": "no",
      "contents": "11118: Konsumprisindeks for varer og tjenester,",
      "descriptiondefault": false,
      "heading": [
        "statistikkvariabel",
        "år"
      ],
      "stub": [
        "Leveringssektor"
      ],
      "matrix": "LevIndAar",
      "subject-code": "al",
      "subject-area": "Arbeid og lønn"
    },
    "contact": [
      {
        "name": " Konsumprisindeksen",
        "phone": "62 88 56 34",
        "mail": "konsumprisindeksen@ssb.no",
        "raw": " Konsumprisindeksen, Statistisk sentralbyrå# +47 62 88 56 34#konsumprisindeksen@ssb.no"
      }
    ]
  }
}
```

### Hent data for en spesifikk tabell
```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/tables/<table-id>/data/
```

#### Parametere
Variablene i tabellen kan brukes til å forespørre en spesifikk del av tabellen ved hjelp av følgende parametere. Hvis ingen parametere er angitt, velges et standardområde i tabellen.  

#### Eksempel på respons

Data uten andre parametere enn språk og outputFormat for tabell med id **11118** som json-stat2

```
HTTP GET https://data.ssb.no/api/pxwebapi/v2/tables/11118/data?lang=no&outputformat=json-stat2
```

```json
{
  "version": "2.0",
  "class": "dataset",
  "label": "11118: Konsumprisindeks for varer og tjenester, etter Leveringssektor, år og statistikkvariabel",
  "source": "Statistisk sentralbyrå",
  "updated": "2024-01-10T07:00:00Z",
  "note": [
    "F.o.m. 2017 er referanseår 2015=100. Se den avsluttede tidsserien 03363 for tall t.o.m 2015 etter gammel klassifisering av leveransesektor med 1998=100."
  ],
  "role": {
    "time": [
      "Tid"
    ],
    "metric": [
      "ContentsCode"
    ]
  },
  "id": [
    "Leveringssektor",
    "Tid",
    "ContentsCode"
  ],
  "size": [
    14,
    1,
    2
  ],
  "dimension": {
    "Leveringssektor": {
      "label": "Leveringssektor",
      "category": {
        "index": {
          "B1": 0,
          "B11": 1,
          "B111": 2,
          "B1111": 3,
          "B1112": 4,
          "B112": 5,
          "B12": 6,
          "B121": 7,
          "B122": 8,
          "B2": 9,
          "B21": 10,
          "B22": 11,
          "B221": 12,
          "B222": 13
        },
        "label": {
          "B1": "Varer",
          "B11": "Norske varer",
          "B111": "Norske varer uten energivarer",
          "B1111": "Norske jordbruksvarer",
          "B1112": "Norske varer uten jordbruks og energivarer",
          "B112": "Energivarer",
          "B12": "Importerte varer",
          "B121": "Importerte jordbruksvarer",
          "B122": "Importerte varer uten jordbruksvarer",
          "B2": "Tjenester",
          "B21": "Husleie",
          "B22": "Tjenester uten husleie",
          "B221": "Tjenester hvor arbeidskraft dominerer",
          "B222": "Tjenester med andre viktige priskomponenter"
        }
      },
      "extension": {
        "elimination": false,
        "show": "value"
      }
    },
    "Tid": {
      "label": "år",
      "category": {
        "index": {
          "2023": 0
        },
        "label": {
          "2023": "2023"
        }
      },
      "extension": {
        "elimination": false,
        "show": "code"
      }
    },
    "ContentsCode": {
      "label": "statistikkvariabel",
      "category": {
        "index": {
          "LevIndAar": 0,
          "Aarsendring": 1
        },
        "label": {
          "LevIndAar": "Konsumprisindeks (2015=100)",
          "Aarsendring": "Årsendring (prosent)"
        },
        "unit": {
          "LevIndAar": {
            "base": "indeks",
            "decimals": 1
          },
          "Aarsendring": {
            "base": "prosent",
            "decimals": 1
          }
        }
      },
      "extension": {
        "elimination": false,
        "refperiod": {
          "LevIndAar": "Gjennomsnitt for året",
          "Aarsendring": "Gjennomsnitt for hvert år"
        },
        "show": "value"
      },
      "link": {
        "describedby": [
          {
            "extension": {
              "LevIndAar": "urn:ssb:conceptvariable:vardok:2755"
            }
          }
        ]
      }
    }
  },
  "extension": {
    "px": {
      "infofile": "None",
      "tableid": "11118",
      "decimals": 1,
      "official-statistics": true,
      "aggregallowed": false,
      "language": "no",
      "contents": "11118: Konsumprisindeks for varer og tjenester,",
      "descriptiondefault": false,
      "heading": [
        "Tid",
        "ContentsCode"
      ],
      "stub": [
        "Leveringssektor"
      ],
      "matrix": "LevIndAar",
      "subject-code": "al",
      "subject-area": "Arbeid og lønn"
    },
    "contact": [
      {
        "name": " Konsumprisindeksen",
        "phone": "62 88 56 34",
        "mail": "konsumprisindeksen@ssb.no",
        "raw": " Konsumprisindeksen, Statistisk sentralbyrå# +47 62 88 56 34#konsumprisindeksen@ssb.no"
      }
    ]
  },
  "value": [
    133.3,
    5.8,
    150,
    3.4,
    125.5,
    8.1,
    131.6,
    9.7,
    123.6,
    7.7,
    235.2,
    -7,
    123.6,
    7,
    133.1,
    12.9,
    122.9,
    6.6,
    125.6,
    5.2,
    116.8,
    3.8,
    131.9,
    6.2,
    126.9,
    3.1,
    134.2,
    7.6
  ]
}
```

##### lang
En valgfri språkparameter.

##### valueCodes
Et utvalg som spesifiserer et område i tabellen som skal returneres. Alle variabler som ikke kan elimineres, må ha et utvalg spesifisert. Utvalget for en variabel gis i følgende form:  
```
valueCodes[VARIABLE-CODE]=ITEM-SELECTION-1,ITEM-SELECTION-2,ITEM-SELECTION-3, etc
```  
der `VARIABLE-CODE` er koden for variabelen, og `ITEM-SELECTION-X` er enten en verdikode eller et utvalguttrykk. Hvis verdikoden inneholder komma, må den være i hakeparenteser, f.eks. `[RANGE(1,12)]`.  

##### codelist
En annen kodeliste kan spesifiseres for en variabel, f.eks. for å bruke en annen aggregering. Kodelisten spesifiseres slik:  
```
codelist[VARIABLE-CODE]=CODELIST-ID
```  
Hvor `VARIABLE-CODE` er koden for variabelen, og `CODELIST-ID` er ID-en til kodelisten som skal brukes.


## POST-endepunktet for tabelldata


### Hent data for en spesifikk tabell (POST-metode)
Dette endepunktet ligner på GET-metoden, men utvalgsuttrykkene sendes i selve forespørselens kropp som et JSON-objekt.  

```
HTTP POST https://data.ssb.no/api/pxwebapi/v2/tables/<table-id>/data?<outputFormat>
```

#### Utvalgsuttrykk
JSON-objektet som spesifiserer forespørselen, gis i følgende form:  
```json
# POST API spørring  - fryst og fersk laks siste 53 uker
payload = {"selection": [
			{"variableCode": "VareGrupper2", 
             "valueCodes": [
                 "01", "02"
             ] 
            },
			{"variableCode": "ContentsCode", 
             "valueCodes": [
                 "Vekt", "Kilopris"
             ] 
            },
			{"variableCode": "Tid", 
             "valueCodes": ["2024*"] }
		]
		}

```

#### Parametere

##### lang
En valgfri språkparameter. Angis i endepunkt.

##### outputFormat
Angis i endepunktet. Formatet resultatet skal være i. Standardverdien er spesifisert av konfigurasjonsendepunktet. Se også konfigurasjonsendepunktet for tilgjengelige formater. 

Merk at til forskjell fra API-versjon 1, så angis nå outputformat og evt. språk i endepunktsurl.

### Utvalgsuttrykk
I stedet for å spesifisere alle verdikoder, kan man bruke et utvalgsuttrykk. Følgende utvalgsuttrykk er tilgjengelige:

#### Jokertegnuttrykk
Et jokertegn kan brukes for å matche alle koder. For eksempel:  
- `*01` matcher alle koder som slutter med `01`.  
- `*2*` matcher alle koder som inneholder `2`.  
- `A*` matcher alle koder som starter med `A`.  
- `*` matcher alle koder.  
- `202*` matcher alle koder som beynner på 202, f.eks. `2024M12`.

Maksimalt to jokertegn kan brukes.

#### Nøyaktig samsvar
Et spørsmålstegn (`?`) kan brukes til å matche nøyaktig ett tegn. For eksempel:  
- `?` matcher nøyaktig ett tegn.  
- `????M12` matcher fire tegn og som slutter med `M12`, f.eks. `2024M12`.

#### TOP
Uttrykket `TOP(N, Offset)` velger de første `N` verdiene med et forskyvning på `Offset`. For eksempel:  
- `TOP(5)` vil velge de første fem verdiene.  
- `TOP(5,3)` vil velge den tredje til den åttende verdien.  
Offset er som standard `0` og trenger ikke spesifiseres. Merk at for tidsvariabelen vil de nyeste verdiene komme først.

#### BOTTOM
`BOTTOM` fungerer på samme måte som `TOP`, men velger verdier fra bunnen av verdilisten.

#### RANGE
`RANGE(X,Y)` velger alle verdier mellom verdikodene `X` og `Y`, inkludert `X` og `Y`.

#### FROM
`FROM(X)` velger alle verdikoder fra og med `X` og nedover, inkludert `X`.

#### TO
`TO(X)` velger alle verdikoder fra starten av listen opp til og med `X`.

---

### Eliminasjon
Hvis eliminering er satt til `true`, kan variabelen elimineres. Da er det er ikke nødvendig å spesifisere noe utvalg for denne variabelen. Resultatet vil da ikke inneholde denne variabelen. Hvis variabelen har en verdi som angir at den er eliminasjonsverdi, vil denne verdien bli valgt for å eliminere variabelen. Hvis det ikke er spesifisert eliminasjonsverdi, vil variabelen elimineres ved å summere opp alle datapunkter for alle verdier av den variabelen. Hvis en variabel har eliminering satt til `false`, må minst én verdi velges for den variabelen.


### Kodeliste-endepunkter

#### Liste over kodelister for en tabell
Gir informasjon om en spesifikk kodeliste.  

```
HTTP GET /api/v2/codelists/<codelist-id>
```

##### Parametere  
###### lang  
En valgfri språkparameter.

#### Eksempel på respons
Et eksempel på respons for kodelisten **agg_FylkerGjeldende**:  
```json
ikke implementert i v2
```



# Responskoder
- **400** - Feil i spørring
- **403** - Forbidden
- **404** – Ressurs ikke funnet.  
- **429** – For mange forespørsler.
- **50X** – Intern serverfeil.  

    | Status code    | Description       | Reason                      |
    | -------        | -----------       | ---------------------       |
    | 200            | Success           | Endepunktet har gitt respont til forespørselen                      |
    | 400            | Bad request       | Forspørselen er ikke gyldig, feil i spørring |
    | 403            | Forbidden         | Antall celler overstiger grensene i APIet |
    | 404            | Not found         | URL i forespørsel eksisterer ikke |
    | 429            | Too many requests | For mange forspørsler innen tidsperioden |
    | 50X            | Internal Server Error | Tjenesten kan være nede |

# Restriksjoner

Noen begrensninger i versjon 2.0 av API-et sammenlignet med den første versjonen av API-et:  

- **Kun én database** – Støtten for flere databaser som kan betjenes av samme instans av API-et er fjernet. Dette reduserer kompleksiteten i API-ets interne funksjoner, siden det ikke trenger å ta hensyn til ulike konfigurasjoner for forskjellige databaser. De fleste organisasjoner har bare én database, så effekten er begrenset til et fåtall instanser av API-et.  
  - Anbefalt løsning for organisasjoner med flere databaser: Ha flere instanser av API-et med forskjellig konfigurasjon.  

- **Ny tabellidentifikasjon** – Identifikatoren for en tabell er endret. I tilfelle en PX-fil brukes, benyttes **MATRIX** i stedet for filnavnet, og i tilfelle **CNMM** brukes, benyttes **TableId** i stedet for **MainTable**. Den nye ID-en skal være stabil og ikke gjenbrukes. Hvis en tabell endres på en måte som gjør den inkompatibel, bør den dupliseres og tildeles en ny ID. På denne måten vil API-et kunne identifisere tabellen selv om den er flyttet i databasen.  

- **Navn på aggregeringsfiler må være unike** – Navnene på aggregeringsfilene i en database må være unike, siden de vil tjene som ID for aggregeringsfilteret.  

- **VARIABLETYPE må formaliseres** – Variabeltypene må defineres og standardiseres.  

- **Tid er ikke lenger eliminerbar** - Det må nå alltid velges en verdi for tidsvariabelen. 

## Referanser
- [https://www.dst.dk/en/Statistik/statistikbanken/api](https://www.dst.dk/en/Statistik/statistikbanken/api)  
- [http://api.statbank.dk/console#data](http://api.statbank.dk/console#data)  
- [https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)