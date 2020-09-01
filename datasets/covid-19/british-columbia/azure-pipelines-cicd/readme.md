# CI/CD Setup for Azure Data Factory

## Step 1: Connect your Dev Azure Data factory with Github
 
1. Open Azure Data Factory that you want to use with the development environment.

2. Click on **Author & Monitor**, this will open the data factory UI's Home.

3. Now go to **Manage** from the menu on the Left side, then click on  **Git Configuration** -> **Set Up Code Repository**. This will show a UI blade with a dropdown listing the supported repository types. As of today it only supports **Github** and **Azure DevOps Git**.

*Note: If you wish to choose a github, please create an empty repo before proceeding to the next steps.*

4. Select the repository type of your choice and provide the required credentials.

5. 
    - Now we have to select a repo to connect this data factory to. Select the repo from the **Git Repository Name** dropdown. (You may create a new one if using Azure DevOps Git)
    
    - Select **master** as the collaboration branch. This branch will be used for publishing to Data factory. By default it is master. Change this if you want to deploy/publish resources from another branch.
    
    - **Root Folder** is the directory where all of the Data factory resources's json files will be copied to. Leave it as '**/**'.

6. Click on **Apply** to save the changes.

Here, we have successfully connected the Azure Data factory to a Git Repo. this has saved all of the resoucres's json files in the branch that we specified. 

To be able to replicate the resources in this data factory we need the ARM templates that are generated when we publish the changes in data factory. When you click on publish, it takes the changes from the collaboration branch i.e master in this case, creates ARM templates and pushes them in the **adf_publish** branch.




Now lets go ahead and publish the changes.

## Step 2: Add the azure pipelines files in the adf_publish repo

1. Clone the repo that you created above and checkout the **adf_publish** branch.

2. Create the following directories.

  - **/cicd**
  - **/resources/arm/blank-adf**

3. Add the following two files in the **/resources/arm/blank-adf** folder.

**template.json**

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "defaultValue": "myv2datafactory",
            "type": "String"
        },
        "location": {
            "defaultValue": "East US",
            "type": "String"
        },
        "apiVersion": {
            "defaultValue": "2018-06-01",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DataFactory/factories",
            "apiVersion": "[parameters('apiVersion')]",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {}
        }
    ]
}

```


**Parameters.json**
```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "value": "df-azdo-datafactory-stg"
        },
        "location": {
            "value": "westeurope"
        },
        "apiVersion": {
            "value": "2018-06-01"
        }
    }
}
```

4. Add the following file in the **/cicd** directory.

**azure-pipelines.yml**

```
# Basic YAML pipeline for Azure Data Factory


# Batching trigger set run only on a adf_publish branch
# cicd folder is not watched
trigger:
  batch: true
  branches:
    include:
      - adf_publish 
    exclude:
      - master
  paths:
    exclude:
      - cicd/* 
    include:
      - "*"


# Link the variable group to read the variables from:
- group: stg-variables



# The build agent is based on Windows OS. 
# Linux agents have some differences in available commands and folder paths expressions, etc
pool:
   vmImage: "windows-latest"



steps:

# Step 1: Checkout code into a local folder src
- checkout: self
  path: src


# Step 2a: Find arm json files for a deployment of blank adf in a src and copy them into the artifact staging folder
- task: CopyFiles@2  
  inputs:
    SourceFolder: '$(Pipeline.Workspace)\src\resources\arm\blank-adf'
    Contents: '**/*.json'
    TargetFolder: '$(build.artifactstagingdirectory)\arm'
    CleanTargetFolder: true
    OverWrite: true
  displayName: 'Extra ARM - Blank ADF Service'
  enabled: true

# Step 2b: Find other adf files, which will deploy pipelines, datasets and so on  in a folder adf_publish and copy them into the artifact folder
- task: CopyFiles@2  
  inputs:
    SourceFolder: '$(Pipeline.Workspace)\src'
    Contents: '**/*ForFactory.json'
    TargetFolder: '$(build.artifactstagingdirectory)\adf_publish'
    CleanTargetFolder: true
    OverWrite: true
    flattenFolders: true
  displayName: 'Extract ARM - ADF Pipelines'
  enabled: true



# Step 3: Debugging - print the output of the command tree of artifacts folder
- powershell: |
    tree "$(build.artifactstagingdirectory)" /F
  displayName: "Debug: Show a directory tree"



# Step 4: Deploy a blank Azure Data Factory instance using ARM templates
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: ''
    subscriptionId: ''
    action: 'Create Or Update Resource Group'
    resourceGroupName: '$(ProductName)-$(Environment)'
    location: 'West Europe'
    templateLocation: 'Linked artifact'
    csmFile: '$(build.artifactstagingdirectory)\arm\template.json'
    csmParametersFile: '$(build.artifactstagingdirectory)\arm\parameters.json'
    overrideParameters: '-name "df-$(ProductName)-$(Environment)"'
    deploymentMode: 'Incremental'
  displayName: Deploy ADF Service
  enabled: true


# Step 5: Deploy Azure Data Factory Objects like pipelines, dataflows using ARM templates that ADF generate during each publish event
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: ''
    subscriptionId: ''
    action: 'Create Or Update Resource Group'
    resourceGroupName: '$(ProductName)-$(Environment)'
    location: 'West Europe'
    templateLocation: 'Linked artifact'
    csmFile: '$(build.artifactstagingdirectory)\adf_publish\ARMTemplateForFactory.json'
    csmParametersFile: '$(build.artifactstagingdirectory)\adf_publish\ARMTemplateParametersForFactory.json'
    overrideParameters: '-factoryName "$(ProductName)-$(Environment)" -AzureSqlDatabase_connectionString "$(sql-conn-string)" -customerStorageLinkedService_connectionString "$(customer-sa-conn-string)" -publicStorageLinkedService_sasUri "$(public-sa-sas-uri)" -RestServiceurl_properties_typeProperties_url "$(rest-url)"'
    deploymentMode: 'Incremental'
  displayName: Deploy ADF Pipelines
  enabled: true
```

## Step 3. Setup CI/CD in Azure DevOps for Data factory.

1. Goto Azure portal, search and open 'Azure DevOps' -> 'My Azure DevOps Organizations'.
![Search Devops](../definitive-healthcare/images/search-azure-devops.png)

![My orgs](../definitive-healthcare/images/My-orgs.png)

2. You can create a new organiztion choose an existing one. 
![Create new Org](../definitive-healthcare/images/new-org.png)

3. Create a new project, choose *Private Visibility*.
![new-project](../definitive-healthcare/images/new-project.png)
![Search Devops](../definitive-healthcare/images/project-visibility.png)



4. Open the project and navigate to **Pipelines -> library**.

![open library](../definitive-healthcare/images/open-library.png)


5. Create a new variable group in named 'stg-variables' and create the following variables in that group:-

```
1. customer-sa-conn-string // Set the connection string for customer storage account
2. Environment // Name of the environment
3. ProductName // Name of the service, in this case it will be the name of the data factory
4. public-sa-sas-uri // SAS URI of the public storage account
5. rest-url // URL for the CSV dataset source. (Leave value as blank if not required)
6. sql-conn-string // Connection string for SQL Database
```
![Create vars](../definitive-healthcare/images/create-vars.png)

6. To create a new pipeline navigate to Pipelines -> Pipelines and click on **New Pipeline**.

[Open pipeline](../definitive-healthcare/images/open-pipelines.png)

[New pipeline](../definitive-healthcare/images/new-pipeline.png)

7. Setup Pipeline

    - Connect: Select your 'Repository Type 
      ![repo type](../definitive-healthcare/images/connect-github.png)

    - Select: Select the repository that you had previously connected the ADF with.
      ![select repo](../definitive-healthcare/images/pipeline-select-repo.png)

    - Configure: Select **Existing Azure Pipelines YAML file** 
      ![select existing yaml](../definitive-healthcare/images/select-existingyaml-options.png)

    - Select **adf_publish** branch, and provide **/cicd/azure-pipelines.yml** as the path.

    This will load the Azure pipeline yaml.

8. Update the  *azureResourceManagerConnection, subscriptionId* keys for all the tasks shown in the pipeline yaml. Todo this select **Settings** shown in the top left corner of every task, this will open a visual yaml editor. Update the aforementioed keys by selecting the relevant subcription. Make sure you do this for all the tasks.

![update subscription step 4](../definitive-healthcare/images/update-subscription.png)
![update subscription step 5](../definitive-healthcare/images/update-subscription-2.png)


9. Save and run the pipeline.

## Adding another environment to the above pipeline


- For adding an extra stage simply repeat the **steps 4 and 5** given in the **azure-pipeline.yml** file and update the following fields:

-  Here, make sure that you create a new variable for each enviroment that you want to create/add in this pipeline. 
    e.g ProdProductName
    This should be created in the variables group and also the following snippet should be updated to use the newly created variable.

*Note: You may update the value of **resourceGroupName** if the expression provided in this template does not match the name of the resource group that you want to create the resources in.*

```
# Step 6: Deploy a blank Azure Data Factory instance using ARM templates
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: ''
    subscriptionId: ''
    action: 'Create Or Update Resource Group'
    resourceGroupName: '$(ProdProductName)-$(Environment)'
    location: 'West Europe'
    templateLocation: 'Linked artifact'
    csmFile: '$(build.artifactstagingdirectory)\arm\template.json'
    csmParametersFile: '$(build.artifactstagingdirectory)\arm\parameters.json'
    overrideParameters: '-name "$(ProdProductName)-$(Environment)"'
    deploymentMode: 'Incremental'
  displayName: Deploy ADF Service
  enabled: true

# Step 7: Deploy Azure Data Factory Objects like pipelines, dataflows using ARM templates that ADF generate during each publish event
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: ''
    subscriptionId: ''
    action: 'Create Or Update Resource Group'
    resourceGroupName: '$(ProdProductName)-$(Environment)'
    location: 'West Europe'
    templateLocation: 'Linked artifact'
    csmFile: '$(build.artifactstagingdirectory)\adf_publish\ARMTemplateForFactory.json'
    csmParametersFile: '$(build.artifactstagingdirectory)\adf_publish\ARMTemplateParametersForFactory.json'
    overrideParameters: '-factoryName "$(ProdProductName)-$(Environment)" -AzureSqlDatabase_connectionString "$(sql-conn-string)" -customerStorageLinkedService_connectionString "$(customer-sa-conn-string)" -publicStorageLinkedService_sasUri "$(public-sa-sas-uri)" -RestServiceurl_properties_typeProperties_url "$(rest-url)"'
    deploymentMode: 'Incremental'
  displayName: Deploy ADF Pipelines
  enabled: true
```


Add the above snipped in the previously created Azure pipeline to add another environment to which the resources are deployed.

