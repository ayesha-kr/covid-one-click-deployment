# Introduction
This document entails the steps that can be followed to set up Continuous Integration and Continuous Delivery for Azure Data Factory. The document comprises of two main parts, the first part entails connecting an existing Azure Data Factory with a Git repository. The second part entails setting up  an Azure DevOps Pipeline that publishes the changes from one ADF to another thereby maintaining multiple environments e.g dev, stage, prod etc.

# Connect Azure Data Factory to a Git Repo

## Prerequisites
  1. Create a Git Repository:-

 - [Create Azure DevOps Git repo](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-new-repo?view=azure-devops)
  
 - [Create a Github repo](https://docs.github.com/en/enterprise/2.15/user/articles/create-a-repo)
 
  2. Initialize the Git Repository.
  3. Make sure that the Git repo has a master branch before you proceed with the next steps.

**Note:** *Please make sure that you do not connect to a Git Repository that was previously connected to another Azure Data Factory, this may cause conflicts in the data factory's resources.*

1. Open the Azure Data Factory that you want to connect with a Git repository.

2. Click on **Author & Monitor**, this will open the data factory UI's Home.

3. Now go to **Manage** from the menu on the left side, then click on  **Git Configuration** -> **Set Up Code Repository**. 

![Select Repo type](./images/set-up-code-repo.png)

This will show a UI blade with a dropdown listing the supported repository types. As of today it only supports **Github** and **Azure DevOps Git**.

![Select Repo type](./images/repo-type.png)

*Note: If you wish to choose Github, please create an empty repo before proceeding to the next steps.*

4. Select the repository type of your choice and when prompted, provide the required credentials.


5. 
    - Now we have to select a repo to connect this data factory to. Select the repo from the **Git Repository Name** dropdown. (You may create a new one if using Azure DevOps Git)
    
    - Select **master** as the collaboration branch. This branch will be used for publishing to Azure Data factory. By default it is master. You may change this if you want to deploy/publish resources from another branch.
    
    - **Root Folder** is the directory where all of the Data factory resource's JSON files will be copied to. Leave it as '**/**'.
![Select Repo type](./images/repo-settings.png)
6. Click on **Apply** to save the changes.

Here, we have successfully connected an Azure Data factory to a Git Repo. this has saved all of the resource's JSON files in the collaboration branch that we specified. Whenever the ADF is published, it takes all the changes from the collaboration branch, creates the ARM templates for the resources found in the collaboration branch and pushes them into the **adf_publish** branch.

When we set up the Git Repository and publish the changes from the Data Factory, it only creates the **adf_publish** branch in the repo but doesn't generate the ARM templates as no change is detected. Hence, we must make a change in any of the activities or pipelines in the Data Factory and then publish it. E.g we can change the **Description** for any of the activity and publish that.

7. Make a change and publish the changes from the ADF. When we click on Publish from the ADF UI, it automatically creates the **adf_publish** branch, creates teh ARM templates for all the resources found in the collaboration branch and pushes them into the **adf_publish branch**.


# Setting up Azure DevOps Pipeline for publishing releases to multiple environments.
 
![CICD Flow](./images/cicd-flow.png)

To be able to replicate the resources in this data factory we need the ARM templates that are generated when we publish the changes in the Azure Data Factory. When you click on publish, it takes the changes from the collaboration branch i.e master in this case, creates ARM templates, and pushes them in the **adf_publish** branch.



## Step 2: Add the Azure pipelines' files in the *adf_publish* branch

1. Clone the repo on your computer, that you created above and checkout the **adf_publish** branch.

- git clone <URL-OF-YOUR-REPO> 
- git checkout adf_publish

2. Download the files required for Azure DevOps Pipeline by clicking [here](./pipeline-files.zip).

The above link will take you to a Github page that contains a zip archive of the required files. Download the zip archive.

![](./images/zip-download.png)

3. Extract the contents of the zip archive downloaded in the previous step, in to the root of the repo you have connected with the Azure Data Factory. Please make sure that when you commit the files, the line endings must be set to **CRLF**.

After having exctracted the files in the repo. The repository should look like this (Branch= adf_publish):- 

![](./images/extracted-contents.png)

## Step 3. Set up CI/CD in Azure DevOps for Azure  Data factory.

1. Navigate to Azure portal, search and open 'Azure DevOps' -> 'My Azure DevOps Organizations'.
![Search Devops](../../definitive-healthcare/azure-pipelines-cicd/images/search-azure-devops.png)

![My orgs](../../definitive-healthcare/azure-pipelines-cicd/images/My-orgs.png)

2. You can create a new organization or choose an existing one. 
![Create new Org](../../definitive-healthcare/azure-pipelines-cicd/images/new-org.png)

3. Create a new project, choose *Private Visibility*.
![new-project](../../definitive-healthcare/azure-pipelines-cicd/images/new-project.png)
![Search Devops](../../definitive-healthcare/azure-pipelines-cicd/images/project-visibility.png)



4. Open the project and navigate to **Pipelines -> library**.

![open library](../../definitive-healthcare/azure-pipelines-cicd/images/open-library.png)


5. Create a new variable group named 'stg-variables' and create the following variables in that group as per the deployment configuration:-



*Note:- For making sure what variables you will need to have in this variable group you can follow the section **How to view the connection strings for the variables** in the guide provided in the following link: [Environment Variables Guide](./vars-readme.md)* 




### Case: Customer Environment with SQL DB

<p align="center">
  <img width="560" height="250" src="./images/sql-vars.png">
</p>

```
1. customer-sa-conn-string 

    // Set the connection string for the customer storage account

  Example value:- 
    DefaultEndpointsProtocol=https;AccountName='';AccountKey=''

2. dataFactoryName 

    // Name of the service, in this case, it will be the name of the data factory to which you the resources to be deployed to.
    Please note that

3. public-sa-sas-uri 

    // SAS URI of the public storage account

    Example value:- 
      https://abc.blob.core.windows.net/?sv=2019-10-10&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-20T19:39:31Z&st=2020-07-20T11:39:31Z&spr=https&sig=ETbJ2zHLvxjXw4%2BShan5SUeP6g81oFh7nKGBDSpagbc%3D

5. sql-conn-string 

    // Connection string for SQL Database
  
    Example value:-  
      integrated security=False;encrypt=True;connection timeout=30;data source=''.database.windows.net;initial catalog='';user id='';Password=''
```




### Case: Customer Environment with Synapse Pool (SQL DataWarehouse)

<p align="center">
  <img width="560" height="250" src="./images/synapse-vars.png">
</p>

```
1. customer-sa-conn-string 

    // Set the connection string for the customer storage account

    Example value:- 
      DefaultEndpointsProtocol=https;AccountName='';AccountKey=''

2. dataFactoryName 
    
    // Name of the service, in this case, it will be the name of the data factory

3. public-sa-sas-uri 

    // SAS URI of the public storage account

    Example value:- 
      https://abc.blob.core.windows.net/?sv=2019-10-10&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-20T19:39:31Z&st=2020-07-20T11:39:31Z&spr=https&sig=ETbJ2zHLvxjXw4%2BShan5SUeP6g81oFh7nKGBDSpagbc%3D

4. synapse-conn-string 

    // Connection string for SQL Database

    Example value:- 
      integrated security=False;encrypt=True;connection timeout=30;data source=''.database.windows.net;initial catalog='';user id='';Password=''
```




### Case: Customer Environment with Synapse DataWarehouse and SQL DB

<p align="center">
  <img width="560" height="250" src="./images/synapseAndSQl-vars.png">
</p>

```
1. customer-sa-conn-string 

    // Set the connection string for the customer storage account

    Example value:- 
      DefaultEndpointsProtocol=https;AccountName='';AccountKey=''

2. dataFactoryName 

    // Name of the service, in this case, it will be the name of the data factory

3. public-sa-sas-uri // SAS URI of the public storage account

    Example value:- 
      https://abc.blob.core.windows.net/?sv=2019-10-10&ss=bfqt&srt=sco&sp=rwdlacupx&se=2025-07-20T19:39:31Z&st=2020-07-20T11:39:31Z&spr=https&sig=ETbJ2zHLvxjXw4%2BShan5SUeP6g81oFh7nKGBDSpagbc%3D

4. synapse-conn-string // Connection string for SQL Database

    Example value:- 
      integrated security=False;encrypt=True;connection timeout=30;data source=''.database.windows.net;initial catalog='';user id='';Password=''

5. sql-conn-string // Connection string for SQL Database
  
    Example value:- 
      integrated security=False;encrypt=True;connection timeout=30;data source=''.database.windows.net;initial catalog='';user id='';Password=''
```

![Create vars](../../definitive-healthcare/azure-pipelines-cicd/images/create-vars.png)



6. To create a new pipeline navigate to **Pipelines -> Pipelines** and click on **New Pipeline**.

![Open pipeline](../../definitive-healthcare/azure-pipelines-cicd/images/open-pipelines.png)

![New pipeline](../../definitive-healthcare/azure-pipelines-cicd/images/new-pipeline.png)

7. Setup Pipeline

    - Connect: Select your 'Repository Type 
      ![repo type](../../definitive-healthcare/azure-pipelines-cicd/images/connect-github.png)

    - Select: Select the repository that you had previously connected the ADF with.
      ![select repo](../../definitive-healthcare/azure-pipelines-cicd/images/pipeline-select-repo.png)

    - Configure: Select **Existing Azure Pipelines YAML file** 
      ![select existing yaml](../../definitive-healthcare/azure-pipelines-cicd/images/select-existingyaml-options.png)

    - Select **adf_publish** branch, and select the file that matches your deployment configuration.
      i.e 
      **azure-pipeline-customer-SQL.yml** in case the environment was deployed with SQL DB as data loader.
      **azure-pipeline-customer-Synapse.yml** in case the environment was deployed with Synapse as data loader.
      **azure-pipeline-customer-SynapseAndSQl.yml** in case the environment was deployed with both Synapse and SQL DB as data loader.

    This will load the Azure pipeline YAML.

8. Update the  **azureResourceManagerConnection**, **subscriptionId** and **resourceGroupName** keys for all the tasks shown in the pipeline YAML. To do this select **Settings** shown in the top left corner of every task, this will open a visual YAML editor. Update the aforementioned keys by selecting the relevant subscription. Make sure you do this for all the tasks.

![update subscription step 4](../../definitive-healthcare/azure-pipelines-cicd/images/update-subscription.png)
![update subscription step 5](../../definitive-healthcare/azure-pipelines-cicd/images/update-subscription-2.png)


9. Save and run the pipeline.

![Run pipeline](./images/run-pipeline.png)

In case you receive the following error, authorize it and run the pipeline again.

![Run pipeline](./images/authorize-resources.png)



# Adding another environment (UAT/PROD) to the CI/CD

For configuring the CI/CD to publish changes to another environment you will need to replicate the steps that are given in the previous section with the following changes.

1. Change the name of the variables group. 
  
  *Please note that the variables within the variable group will remain the same. You only have to update the name of the variable group both while creating the variable group and in the pipeline YAML file as well.*

![](./images/prod-vars.png)

2. Update the pipeline YAMl to use the newly created variable group.

![](./images/update-var-name.png)

This is all you need to update while creating a new pipeline that updates another environment.

Note: This pipeline will be using the SQL DB or Synapse SQL Pool that you specify in the variables. Please make sure that it doesn't use the same DB or Synapse as the one created earlier to avoid data conflicts.