# Consumption of this dataset using Azure notebook

The following button will take you to the hosted Azure notebook project. From there you can click 'Clone' to clone the project into your own Azure Notebook account.

[![Azure Notebooks](https://notebooks.azure.com/launch.svg)](https://notebooks.azure.com/anon-cbd95a/projects/load-azure-blob-to-azure-synap)

## This notebook documents the URLs and sample code to access the COVID-19 data published by Definitive Health Care and how it can be loaded into Azure Synapse or Azure SQL DB

URL's of the curated data

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





# Consumption of this dataset using Azure Synapse Notebook

## Introduction

A Synapse Studio (preview) notebook is a web interface for you to create files that contain live code, visualizations, and narrative text. Notebooks are a good place to validate ideas and use quick experiments to get insights from your data. Notebooks are also widely used in data preparation, data visualization, machine learning, and other Big Data scenarios.

With an Azure Synapse Studio notebook, you can:

- Get started with zero setup effort.
- Keep data secure with built-in enterprise security features.
- Analyze data across raw formats (CSV, txt, JSON, etc.), processed file formats (parquet, Delta Lake, ORC, etc.), and SQL tabular data files against Spark and SQL.
- Be productive with enhanced authoring capabilities and built-in data visualization.

## Setup

1. Click here to open the Azure [Synapse Notebook](https://github.com/ayesha-kr/covid-one-click-deployment/blob/4d955ae3fecc6cfaddcfffc6958fbf72ad5d857d/datasets/covid-19/definitive-healthcare/azure-notebook/AzureSynapse-pyspark-definitive-health-care.ipynb). To save the file, **right-click -> Save Link as** to save the file.


2. Open Azure Synpase Workspaces.

![Open Synapse Workspaces](./images/open-synapse-workspaces.png)

3. Click on **Add** to create a new workspace.

![Add Synapse workspace](./images/add-synapse-workspace.png)

4. Fill in the required values, you may use exisng ADlS Gen 2 storage account or create a new one.

![Create Synapse Workspace](./images/create-synpase-workspace.png)

5. Create SQl credentials.

![Fill SQl credentials](./images/create-synapse-sql-credentials.png)

6. Create worskpace.

![Review and create workspace](./images/create-synapse-final.png)

7. Open Synapse Workspace.

![Open workspace](./images/open-newly-workspace.png)

8. Launch Synapse Studio.

![Launch Synapse studio](./images/luanch-synapse-studio.png)

9. Import notebook, open the file downloaded in **Step 1**.

![Import Synapse notebook](./images/import-notebook.png)

![Import Synapse notebook](./images/select-ipynb-file.png)

*Note: You can not update the name of the notebook after its published. So make sure you update that before publishing the notebook.*

10. To run the notebook you need to attach the Synpase notebook to a SPark SQL Pool. Go to Manage pools -> New

![Manage Spark Pool](./images/manage-pools.png)

![Create Spark Pool](./images/new-pool.png)

11. The base version of the spark pool is enough to run this notebook, you can increase it later if needed.

![Synapse Notebook](./images/create-pool-basics.png)

12. Additional Settings: Keep Auto-pause enable and change the 'Number of idle minutes' to **10**, you can leave the rest of the fields as default. 

![Synapse Notebook](./images/create-pool-add-settings.png)

13. Click on 'Create' to create the Spark pool.

![Synapse Notebook](./images/create-pool-final.png)

14. Go to Develop and Open the imported notebook.

![Synapse Notebook](./images/goto-develop.png)

15. Attach notebook to the Spark Pool you have created above.

![Synapse Notebook](./images/attach-spark-pool.png)

16. Publish the notebook.

![Synapse Notebook](./images/publish-notebook.png)

17. Run the notebook.

![Synapse Notebook](./images/run-all.png)

18. After you have run the notebook, to view the data that has been loaded in to Synapse, Go to **Data** -> **Databases** -> **covid19** -> **Tables** -> **definitivehealthcare**. Right-Click on the table name i.e. **definitivehealthcare** -> **New Notebook** -> **Load to DataFrame**. This will open a new notebook pre-filled with code that loads the data from table into data frame

![Load data frame](./images/goto-data-load-frame.png)

19. Run the new notebook to view data from the database.

![Run: Load data frame](./images/open-frame-run-all.png)

20. Output

![Load data frame](./images/output-load-frame.png)
