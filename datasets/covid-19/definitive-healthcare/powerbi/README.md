# Prerequisites

  1. Customer Azure Data Factory Pipeline is deployed and data has been successfully written to Synapse.
---------------------------------------------
# Load data from a Synapse table in Power BI

1. Open Power BI Destop

2. Click on 'Get Data'

![Get Data](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/get-data.png)

3. Select **Azure** -> **Azure SQL Data Warehouse** -> **Connect**

![Select data source type](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/get-data-sources.png)

4. Provide the following to connect to Synapse.

- Synapse Server Name
- Synapse Database name
- Database username
- Database password

![Enter Synapse server details](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/synapse-credentials.png)

![Enter Synapse credentials](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/data-source-credentials.png)

Upon providing the credentails, Power BI will connect to Synapse and show a database schema.

5. Select the tables to load the data from. In our case this is the **operationaldhc** table, you may choose any of the tables to load data from. After selecting the table click on **Load** button to load the data.

![Select Synapse Table](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/load-synapse-operational-table.png)

After this Power BI will start loading the data e.g.

![Loading Rows](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/loading-rows.png)

------------------------------------------------------------

# Create Visualization
After the data has been loaded into Power BI, you can view the data model in the **Fields** pane, located in the right hand side.


![View Data Model](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/view-fields.png)


In this case we are creating a Stacked Column Chart that shows the the **Average Ventilator Usage** by **State Name**.


![Create report](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/powerbi/images/create-visualization.gif)


