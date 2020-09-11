## Deploy Data Factory with optional SQL Server and SQL Database

#### Prerequisites:
1. Resource group for the deployment.

#### To be provided:
1. Resource Group
2. Data Factory Name
3. Storage Account Name
4. SQL Server Name
5. Data Warehouse Name
6. SQL Login Administrator Username
7. SQL Login Administrator Password
8. Account Key for IRI storage account
9. Account key for NewYork Times storage account in public environment

Click the following button to deploy all the resources.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fayesha-kr%2Fcovid-one-click-deployment%2Fmaster%2Fdatasets%2FIRI%2Fcustomer%2Ftemplates%2FIRI_one_click_arm_template.json)

#### Configure Firewall Rule
After deployment, to access the newly created SQL server from your client IP, configure the firewall rule as shown below:

![Firewall Rule](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/IRI/customer/images/firewallRule.gif)


#### Manually Trigger Pipeline

After the deployment you can go in side your resource group open the ADF **Author and monitor** section and trigger the pipeline as shown below.

![Manual Pipeline Trigger 1](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/IRI/customer/images/manualtrigger-1.png)
![Manual Pipeline Trigger 2](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/IRI/customer/images/manualtrigger-2.png)


