# Table of Contents

1. How to check which environment variables you need to add in the variables group.
2. How to view the connection strings for the variables.
3. Create an Azure Resource Manager Service Connection.

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


# Create an Azure Resource Manager Service Connection

1. Navigate to your **Azure DevOps Organization -> Your Project**.

2. Navigate to **Project Settings** shown in the bottom-left of the screeen.

![](./images/project-settings.png)

3. In the **Project Settings** menu navigate to **Service Connections**.

 ![](./images/service-connections.png)

4. Click on Create a new Service Connection.

![](./images/new-service-connection.png)

5. Select **Azure Resource Manager** as the service or connection type.

 ![](./images/arm-connection-type.png)

6. Select **Service Principal (Automatic)** as the Authentication method.


![](./images/service-principal.png)

7. Under Scope Level, select **Subscription**.

	- **Subscription**: Click on the subscription dropdown and select the subscription in which you are deploying the Azure DevOps Pipeline.

	- **Resource Group**: The resource group can be left empty. If you want to restrict it to a specific resource group you may select that resource group from the dropdown. Please note that the resource group must already exists for it to shown in the dropdown.

	- **Security**: Select/Checkmark **Grant access permission to all pipelines**. 

![](./images/service-connection-subcription.png)

Click on **Save** to create the service connection.

Now that you have created the service connection, we have to update this in the pipeline YAML file. 

## Updating the Azure Pipeline YAML to use the created service connection.

Navigate to Azure DevOps Organizations -> *Your Project* -> Azure Pipelines.

### Case: Updating a Pre-existing Azure Pipeline.

1. Navigate to Azure Pipelines.

2. Open the pipeline that you wish to update, from the menu button on the top-right select **Edit**. This will open the visual pipeline YAMl editor.

3. Update the following keys for all the tasks that have these keys in the pipeline YAML. To do this select **Settings** shown in the top-left corner of every task, this will open a visual YAML editor. Update the  keys by selecting the relevant subscription. Make sure you do this for all the tasks that reference these keys.

	- **azureResourceManagerConnection** 
		This is the name of the Service Connection we created earlier.
	
	- **subscriptionId**
		This is the actual Subscription ID associated with Service Connection.

	- **resourceGroupName**
		The name of the resource group in which you want to deploy the new Data Factory. 

The above pipeline does an incremental deployment meanning that it will create new resources if they are not present and will update the existing resources according to recieved changes.


![update subscription step 4](./images/update-subscription-manual-1.png)

![update subscription step 5](./images/update-subscription-manual-2.png)


### Case: New Azure Pipeline using manually created service connection.

6. To create a new pipeline navigate to **Pipelines -> Pipelines** and click on **New Pipeline**.

![Open pipeline](./images/open-pipelines.png)

![New pipeline](./images/new-pipeline.png)

7. Setup Pipeline

    - Connect: Select your 'Repository Type 
      ![repo type](./images/connect-github.png)

    - Select: Select the repository that you had previously connected the ADF with.
      ![select repo](./images/pipeline-select-repo.png)

    - Configure: Select **Existing Azure Pipelines YAML file** 
      ![select existing yaml](./images/select-existingyaml-options.png)

    - Select **adf_publish** branch, and select the file that matches your deployment configuration.
      i.e 
      **azure-pipeline-customer-SQL.yml** in case the environment was deployed with SQL DB as data loader.
      **azure-pipeline-customer-Synapse.yml** in case the environment was deployed with Synapse as data loader.
      **azure-pipeline-customer-SynapseAndSQl.yml** in case the environment was deployed with both Synapse and SQL DB as data loader.

    This will load the Azure pipeline YAML.

8. Update the following keys for all the tasks that have these keys in the pipeline YAML. To do this select **Settings** shown in the top left corner of every task, this will open a visual YAML editor. Update the  keys by selecting the relevant subscription. Make sure you do this for all the tasks that reference these keys.

	- **azureResourceManagerConnection** 
		This is the name of the Service Connection we created earlier.
	
	- **subscriptionId**
		This is the actual Subscription ID associated with Service Connection.

	- **resourceGroupName**
		The name of the resource group in which you want to deploy the new Data Factory. 

The above pipeline does an incremental deployment meanning that it will create new resources if they are not present and will update the existing resources according to recieved changes.



![update subscription step 4](./images/update-subscription-manual-1.png)

![update subscription step 5](./images/update-subscription-manual-2.png)



9. Save and run the pipeline.


