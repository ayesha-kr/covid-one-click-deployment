# Clone Azure Notebook

The following button will take you to the hosted Azure notebook project. From there you can click 'Clone' to cloen the project into your own Azure Notebook account.

[![Azure Notebooks](https://notebooks.azure.com/launch.svg)](https://notebooks.azure.com/anon-cbd95a/projects/load-azure-blob-to-azure-synap)

## This notebook documents the URLs and sample code to access the COVID-19 data published by Definitive Health Care and how it can be loaded into Azure Synapse or Azure SQL DB

URL of the curated data

CSV: https://covidtrackingdefinitive.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv?sv=2019-12-12&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-27T20:27:49Z&st=2020-07-27T12:27:49Z&spr=https&sig=%2ForvhRm%2BPnVJRLiocMHayJxTDozY1iCEwtBMtCEI60w%3D

JSON: https://covidtrackingdefinitive.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json?sv=2019-12-12&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-27T20:27:49Z&st=2020-07-27T12:27:49Z&spr=https&sig=%2ForvhRm%2BPnVJRLiocMHayJxTDozY1iCEwtBMtCEI60w%3D

PARQUET: https://covidtrackingdefinitive.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet?sv=2019-12-12&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-27T20:27:49Z&st=2020-07-27T12:27:49Z&spr=https&sig=%2ForvhRm%2BPnVJRLiocMHayJxTDozY1iCEwtBMtCEI60w%3D

JSONL: https://covidtrackingdefinitive.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl?sv=2019-12-12&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-27T20:27:49Z&st=2020-07-27T12:27:49Z&spr=https&sig=%2ForvhRm%2BPnVJRLiocMHayJxTDozY1iCEwtBMtCEI60w%3D

Download the dataset file using the built-in capability download from a http URL in Pandas. Pandas has readers for various file formats:

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_json.html (use lines=True for json lines)


## Introduction

This notebook loads the data from a CSV file into a dataframe and then writes it to Azure Synapse /Azure SQL DB.


Note: To write the data to Azure Synapse or SQL DB, please provide the connection details in the notebook.