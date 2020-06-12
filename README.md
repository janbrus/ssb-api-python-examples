# ssb-api-python-examples

## Jupyter notebooks on how to use Python to access Statstics Norways APIs

Statistics Norway has got three external APIs. The most important one is the [API:Create your own dataset](https://www.ssb.no/en/omssb/tjenester-og-verktoy/api/px-api) or PxWebApi. Here are examples on how to use the APIs from Python. 

The plan is to give all examples both in Norwegian and in English.
<<<<<<< HEAD
- The [Norsk](eks1_doi_csv_nor.ipynb) / [in English](eks1_doi_csv1-en.ipynb) a very basic example on Index of retail sales towards the [API: Readymade datasets](https://data.ssb.no/api/v0/dataset/?lang=en).
- [Norsk](kt-csv-nor.ipynb) / [English](kt-csv-nor.ipynb) Import a readymade CSV dataset, Main Economic Forecasts, to Pandas. Shows basic plots using Pandas plot and Plotly Express.
- [Norsk](two-tables-one-chart_nor.ipynb) / [English](two-tables-one-chart-en.ipynb) http POST example, combines two datasets from two Statbank tables using Pandas pivot, and plot them using Matplotlib.
- [Konkurs-datokonv](konkurs-datokonv.ipynb) / [English](konkurs-datokonv-en.ipynb) shows the use of a general function for converting the time variable from category to date, and the difference of the two in two plots. The table used is on weekly bankruptcies.
- [nr-dateconv-nor](nr-datokonv.ipynb) / [English](nr-datokonv-en.ipynb) shows posting of the same query for GDP changes to tables with different frequency, and the use of the function dateconv() which converts from Categories to dateformat and set a Pandas PeriodIndex.
=======
- The [Norsk](eks1_doi_csv_nor.ipynb) [in English](eks1_doi_csv1-en.ipynb) examples in this repository are a very basic towards the [API: Readymade datasets](https://data.ssb.no/api/v0/dataset/?lang=en).
- [Norsk](kt-csv-nor.ipynb) / [English](kt-csv-nor.ipynb) Import a readymade CSV dataset, Main Economic Forecasts, to Pandas. Shows basic plots using Pandas plot and Plotly Express.
- [Norsk](two-tables-one-chart_nor.ipynb) / [English](two-tables-one-chart-en.ipynb) http POST example, combines two datasets from two Statbank tables using Pandas pivot, and plot them using Matplotlib.


Example, for the time beeing, in Norwegian only
- [Laks-no](laks-no.ipynb) is a basic example on using http POST to query API:Create your own datasets or [PxWebApi](https://www.ssb.no/en/omssb/tjenester-og-verktoy/api/px-api).


#### Examples on Statistics Norways [Statistical Classifications and Codelists](https://www.ssb.no/en/klass/) API
- [Klasskommune](klass_kommune2020.ipynb) how to get and filter municipality IDs.
- [KOSTRA arter og funksjoner](kostra-kode-nor.ipynb) hvordan hente og filtrere KOSTRA arter og funksjoner med definisjoner via Klass API (KOSTRA - Municipality-State-Reporting)


