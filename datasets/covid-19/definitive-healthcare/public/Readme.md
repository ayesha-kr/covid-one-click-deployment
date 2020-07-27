## Deploy Data Factory with optional SQL Server and SQL Database

#### Prerequisites:
1. Resource group for the deployment.

#### To be provided:
1. Resource Group
2. Data Factory Name
3. Storage Account Name
4. Option (Yes or No) to deploy or not to deploy SQL Server, SQL Database and SQL sink within the pipeline.
5. SQL Server Name (If selected 'Yes')
6. SQL Database Name (If selected 'Yes')
7. SQL Login Administrator Username (If selected 'Yes').
8. SQL Login Administrator Password (If selected 'Yes').

**NOTE** - If you go with SQL sink, the name of the table where data is written is _**covid_tracking**_.

Click the following button to deploy all the resources.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fayesha-kr%2Fcovid-one-click-deployment%2Fmaster%2Fdatasets%2Fcovid-19%2Fdefinitive-healthcare%2Fpublic%2Ftemplates%2Fazuredeploy.json)


#### Configure Firewall Rule
After deployment, to access the newly created SQL server from your client IP, configure the firewall rule as described in the following GIF:

![Firewall Rule](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/public/images/firewallRule.gif)


#### Manually Trigger Pipeline

After the deployment you can go in side your resource group open the ADF auther and monitor section and trigger the pipeline as given below.

![Manual Pipeline Trigger](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/public/images/manual-ADF-public-env-trigger.png)



#### Activate Trigger for the Pipeline

If your trigger is deployed along with pipeline, you have to explicitly activate that trigger as shown below.

![Activate Trigger](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/public/images/activateTrigger.gif)
