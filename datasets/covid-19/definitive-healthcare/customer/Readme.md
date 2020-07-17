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

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fosamaemumba%2Farm-template-button-with-condition%2Fmaster%2FdefinitiveHC%2Fone_factory_complete_conditional_sql_sink_arm_template.json)

#### Configure Firewall Rule
After deployment, to access the newly created SQL server from your client IP, configure the firewall rule as described in the following GIF:

![Firewall Rule](https://github.com/osamaemumba/arm-template-button-with-condition/blob/master/firewallRule.gif)
