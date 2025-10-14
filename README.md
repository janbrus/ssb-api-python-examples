# ssb-api-python-examples

<!--- Language: no --->
### Norsk - for [English see below](#English)

# Hvordan bruke Python mot SSBs API-er, vist med Jupyter notebooks

Statistisk sentralbyrå (SSB) tilbyr tre API-er for å hente ut og integrere SSBs data med egne systemer. API-ene er åpne og krever ikke registrering:

- **API for å poste spørringer i JSON mot alle Statistikkbankens 7000 tabeller (PxWebApi)**: Dette API-et lar deg sende spørringer i JSON-format mot alle tabeller i Statistikkbanken.
- **API med ferdige datasett**: Dette enkle API-et gir deg tilgang til 200 oppdaterte datasett med fast URL, hentet fra de mest brukte tabellene i Statistikkbanken. Dette skal avvikles, og erstattes 
- **REST API for statistiske klassifikasjoner og kodelister**: Dette API-et gir deg tilgang til statistiske klassifikasjoner og kodelister (Klass).


SSB har kommet med en **[versjon 2](https://www.ssb.no/api/pxwebapi/pxwebapi-2.0) av PxWebApi**. Dette håndterer også http GET. Formatet på http POST er endret. Se **egen [README](PxWebApi2/README.md)** for eksempler som benytter versjon 2. 

Det kommer fortsatt mer metadata i JSON-stat2. Det gjelder både versjon 1 og 2 av APIet. Noen av eksemplene under er oppdatert, bl.a. for å vise dette. Flere oppdateringer vil komme.

Forøvrig er R-pakken [PxWebApiData](https://CRAN.R-project.org/package=PxWebApiData) oppdatert til versjon 1.1 i oktober 2025. Den håndterer også v2 GET Url'er.



Alle eksempler bruker Python [Pandas](https://pandas.pydata.org/).

## API med ferdige datasett - CSV - skal avvikles
- [Varehandel](eks1_doi_csv_nor.ipynb) - veldig enkelt eksempel med lite datasett.
- Se i stedet [Versjon 2 eksempler](PxWebApi2/README.md)


## JSON-stat eksempler for å poste spørringer mot [PxWebApi](https://www.ssb.no/en/omssb/tjenester-og-verktoy/api/px-api)

- [apidata](apidata-no.ipynb) - dapla-statbank-client er en python pakke laget av SSB og gjør det enkelt å hente JSON-stat2 datasett via PxWebApi.

- [Laks](laks-no.ipynb) - Post spørring og få Pandas dataframe i retur
- [JsonStatToPandas](jsonstatToPandas_funksjon.ipynb) - To funksjoner for å poste JSON spørring og få Pandas dataframe i retur
- [komm-nr-id](komm-nr-id-nor.ipynb) - Hvordan vise **både** kommunenummer/-kode og kommunenavn i en dataframe, dvs. vise kode og tekst i JSON-stat
- [Kombiner to tabeller](two-tables-one-chart_nor.ipynb) - Spørre mot to ulike Statstikkbank-tabeller og vise resultatet i en figur med Pandas pivot og Matplotlib.
- [Konkurs-datokonv](konkurs-datokonv.ipynb) - Funksjon for å konvertere Tid fra kategori til datoformat. Viser forskjellen i to figurer med ukentlige konkurser.
- [Nasjonalregnskap-datokonvertering](nr-datokonv.ipynb) - Viser samme spørring mot tabeller med ulik frakvense, her BNP-endring i månedlig, kvartalsvis og årlig nasjonalregnskap. Viser dateconv() som konverterer fra kategori til dataoformat og setter Pandas Period.
- [text-code](text-code-nor.ipynb) - Få Kode og Tekst i JSON-stat - eksempel med HS-varekoder i månedlig Utenrikshandel


## Klassifikasjoner og kodelister (Klass)
- [Klasskommune](klass_kommune2020.ipynb) - Standard for kommuneinndeling  til Pandas.
- [KOSTRA-koder](kostra-kode-nor.ipynb) - KOSTRA koder for regnskapsarter og -funksjoner. Hvordan hente og filtrere KOSTRA arter og funksjoner med definisjoner via Klass API (KOSTRA - Municipality-State-Reporting)



#### Lenker for mer informasjon:
- [SSBs API-er med åpne data](https://www.ssb.no/api/).
- [PxWebApi brukerveiledning](https://www.ssb.no/omssb/_attachment/248256).
- [Slik bruker du SSBs statistikkbank](https://www.ssb.no/statbank/hvordan-bruke-statistikkbanken).

**R-bruker?** 
Bruk i stedet R-pakken [PxWebApiData](https://CRAN.R-project.org/package=PxWebApiData) og se denne [Introduksjonen](https://cran.r-project.org/package=PxWebApiData/vignettes/Introduction.html). Tips: Har du problem med æøå i API setlocale i R til "no_NO.UTF8"

<!--- Language: en --->
# English
## Jupyter notebooks on how to use Python to access Statstics Norways APIs

Statistics Norway offers three APIs that allow you to retrieve and integrate SSB's data with your own systems. These APIs are open and do not require registration. Here is a brief overview of the three APIs:

1. **API for posting queries in JSON to all of Statbank Norway's 7000 tables ([PxWebApi](https://www.ssb.no/en/omssb/tjenester-og-verktoy/api/px-api))**: This API allows you to send queries in JSON format to all of Statistikkbanken's tables.
2. **API for ready-made datasets**: This API provides access to 200 datasets with fixed URLs. This API will be discontinued.
3. **REST API for statistical classifications and code lists**: This API provides access to statistical classifications and code lists.


## Examples using CSV from the [API for Readymade datasets](https://data.ssb.no/api/v0/dataset/?lang=en)

- [Basic](eks1_doi_csv1-en.ipynb) a very basic example on Index of retail sales.
- [Economic trends](kt-csv-nor.ipynb) Import a readymade CSV dataset, Main Economic Forecasts, to Pandas. Shows basic plots using Pandas plot and Plotly Express.

## Examples using http POST to query [PxWebApi](https://www.ssb.no/en/omssb/tjenester-og-verktoy/api/px-api)
All examples are using JSON-stat output and the library [pyjstat](https://pypi.org/project/pyjstat/) 

- [apidata](apidata-en.ipynb) - dapla-statbank-client is a package made by Statistics Norway and makes it easier to get JSON-stat2 datasets.

- [basic](laks-en.ipynb) - a basic example on using http POST to query PxWebApi.
- [jsonstatToPandas_function](jsonstatToPandas_function-en.ipynb) - General function to read JSON-stat to Pandas dataframes
- [two-tables-one-chart](two-tables-one-chart-en.ipynb) - http POST example, combines two datasets from two Statbank tables using Pandas pivot, and plot them using Matplotlib.
- [bankrupties](konkurs-datokonv-en.ipynb) - shows the use of a general function for converting the time variable from category to date, and the difference of the two in two plots. The table used is on weekly bankruptcies.
- [gdp-dateconv](nr-datokonv-en.ipynb) - shows posting of the same query for GDP changes to tables with different frequency, and the use of the function dateconv() which converts from Categories to dateformat and set a Pandas PeriodIndex.
- [text-code](text-code-en.ipynb) - shows how to get both text and code from JSON-stat. Example using HS-code from monthly Foreign trade by country.

## Example on Statistics Norway's [Statistical Classifications and Codelists](https://www.ssb.no/en/klass/) API
- [Klasskommune](klass_kommune2020.ipynb) get municipality names and codes.


**Using R ?** Try Statistics Norway's R-package [PxWebApiData](https://CRAN.R-project.org/package=PxWebApiData) and see [Introduction](https://cran.r-project.org/package=PxWebApiData/vignettes/Introduction.html) which is now in version 1.1.


