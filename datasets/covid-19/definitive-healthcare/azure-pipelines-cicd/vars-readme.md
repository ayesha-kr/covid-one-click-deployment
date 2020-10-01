# Table of Contents

1. How to check which environment variables you need to add in the variables group.
2. How to view the connection strings for the variables

## How to check which environment variables you need to add in the variables group.

Navigate to the following directory in the Git Repo that you have connected with Data factory. (**adf_publish** branch)

```
~/DataFactoryName/  
# Here '~' stands for the root of the repo and 'DataFactoryName' is to be replaced with actual resource name of the Azure Data factory that was connected with the Git Repo.
```

Open the ARM Parameters file i.e **ARMTemplateParametersForFactory.json**

Please note that in order for this file to be generated, you will need to make at least one change in the Data Factory and then publish it so that the data factory generates the ARM templates for the resources. When we set up the Git Repository and publish the changes from the Data Factory, it only creates the **adf_publish** branch in the repo but doesnt generate the ARM templates as no change is detected. Hence, we must make a change in any of the activities or pipelines in the Data Factory and then publish it. E.g we can change the **Description** for any of the activity and publish that.

In this file you can see the different parameters that will need to be overridden for every pipeline execution.

All the parameters found in this file are to be created as variables in the variable group and respective values should be assigned.

For example, in case we had deployed the Customer Environment with SQL DB we would have the following parameters file:-

```
{
	"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"factoryName": {
			"value": "demoCustomerADFyg2mbyywcb6bm"
		},
		"AzureSqlDatabase_connectionString": {
			"value": ""
		},
		"customerStorageLinkedService_connectionString": {
			"value": ""
		},
		"publicStorageLinkedService_sasUri": {
			"value": ""
		}
	}
}
```

The variables that will need to be created are:-

1. AzureSqlDatabase_connectionString

2. customerStorageLinkedService_connectionString

3. publicStorageLinkedService_sasUri

*Please note that the names of the variables in the variable group are not that important. You just have to make sure that they are referenced correctly in step 5 of the pipeline YAML file.*

For the **factoryName** parameter we don't need to create a variable as we already have the **Product** and **Environment** variables in the variable group which when combined make the name for the Data Factory that will deployed as part of the pipeline flow i.e. 'df-$(Product)-(Environment)'.

Updating the Azure pipeline YAML file to use the variables found above.

Navigate to the **Azure pipeline -> Edit**. This will open the the yml file in an editor.

We need to update the **overrideParameters** key in **Step 5** to incorporate the parameter changes. That is, we need to make sure that all the parameters that were found in the **ARMTemplateParametersForFactory.json** file are overriden in this step. To override a parameter, append '-' before the parameter name and then you can provide the name of the environment variable that you wish to replace it with using **$(ReplaceWithVariableName)** syntax.

For the above file the resulting step 5 would be:-

```
# Step 5: Deploy Azure Data Factory Objects like pipelines, dataflows using ARM templates that ADF generate during each publish event
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: ''
    subscriptionId: ''
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'rg-$(ProductName)-$(Environment)'
    location: 'West Europe'
    templateLocation: 'Linked artifact'
    csmFile: '$(build.artifactstagingdirectory)\adf_publish\ARMTemplateForFactory.json'
    csmParametersFile: '$(build.artifactstagingdirectory)\adf_publish\ARMTemplateParametersForFactory.json'
    overrideParameters: '-factoryName "df-$(ProductName)-$(Environment)" -AzureSqlDatabase_connectionString "$(sql-conn-string)" -customerStorageLinkedService_connectionString "$(customer-sa-conn-string)" -publicStorageLinkedService_sasUri "$(public-sa-sas-uri)" '
    deploymentMode: 'Incremental'
  displayName: Deploy ADF Pipelines
  enabled: true
```

# How to view the connection strings for the variables 

## Storage Account (Customer Environment)

**customer-sa-conn-string**

You need to provide the values for the following two keys:-
1. AccountName
2. AccountKey

Example Value:-

```
DefaultEndpointsProtocol=https;AccountName='ToBeREPLACED';AccountKey='ToBeREPLACED'

```

To view these, navigate to the Azure 'Storage accounts' window. Open the storage account that you wish to use as the account for storing curated data in the Customer environment. Copy the value of the Connection String and paste that as the value for the variable in the variable group of the Azure DevOps pipeline.

![](./images/customer-storage-account-keys.png)


## Storage Account (Public Environment)

**public-sa-sas-uri**

To connect to the storage account of the public environment we need to provide the SAS URI as value to this variable. To get the SAS URI please contact the administrator(s) of the Public environment. A SAS URI comprises of two parts i.e 1. URL and 2. SAS token. 

Example value:- 

```
https://abc.blob.core.windows.net/?sv=2019-10-10&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-20T19:39:31Z&st=2020-07-20T11:39:31Z&spr=https&sig=ETbJ2zHLvxjXw4%2BShan5SUeP6g81oFh7nKGBDSpagbc%3D
```

## SQl DB

**sql-conn-string**

Example Value:-
```
data source='ToBeREPLACED'.database.windows.net;integrated security=False;Server=tcp:'ToBeREPLACED'.database.windows.net,1433;Initial Catalog='ToBeREPLACED';Persist Security Info=False;User ID='ToBeREPLACED';Password='ToBeREPLACED';MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

We have to provide the SQL connection string as value for this variable. To view the connection string for a SQL DB, navigate to **SQL Databases** -> Click on the database that you want to use -> **Overview** -> **Show database connection strings**

![](./images/sql-db-conn-string-1.png)

After you have opened the connection strings page, click on ADO.NET to view the SQL authentication string and replace all the values marked as **ToBeREPLACED** with the values shown in the connection string. Please note that you will have to provide the password yourself. For security purposes, the password is not shown in the connection string. In case you do no remember it you can reset the password.

![](./images/sql-db-conn-string-2.png)

## Synapse SQL Pool (SQL DataWarehouse)

**synapse-conn-string**

Example value:- 
```    
    integrated security=False;encrypt=True;connection timeout=30;data source='ToBeREPLACED'.database.windows.net;initial catalog='ToBeREPLACED';user id='ToBeREPLACED';Password='ToBeREPLACED'
```

We have to provide the Synapse Pool connection string as value for this variable. To view the connecting for Synapse Pool, navigate to **Azure Synapse Analytics (formerly SQL DW)** -> open the Synapse pool that you wish to use. Navigate to **Overview** -> **Show database connection strings** -> **ADO.NET** to view the connection string.

![](./images/synapse-conn-string.png)