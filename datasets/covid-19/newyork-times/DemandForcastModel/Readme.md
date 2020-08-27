## Introduction:

This section describes the complete procedure to execute a machine learning pipeline as part of Azure datafactory all the way from creating the ML pipeline incorporating it to Azure datafactory and making power BI charts out it 

## Prerequisites

This pipeline assumes that you already have the customer SQL packaged deployed via Azure one click deployment. 
The pipeline is designed for SQL package but it can work for Synapse as well by replacing the SQL db with synapse in all the places.

## Resources Needed
  - Machine learning workspace
  - App registration services
  - Azure data factory
  - SQL database
  - Power BI

## Steps

### Customer package deployment

Deploy the customer SQL package by following the instructions given here<hyperlink to NYtimes customer package> 
You should have a resource group created having SQL DB and ADF inside along with the couple of other resources.

### Machine learning batch prediction model

Goto azure portal and search for **Machine Learning**
It will open up the following template. Fill the template as given below.

Provide the resource group name that has been created in the previous step. In this case it is **NewyorkTimes**

[Machine Learning workspace create](./images/ML-ws-create.png)

Once the workspace is created, open the resource and launch it by clicking the **Launch now** button.

[Machine Learning workspace launch](./images/ml-ws-launch.png)


Now go to the left panel and click **Dataset** and click on **New Datastore**.
Provide the parameters as given below and choose the Server name / database name as the one created as part of one click deployment

[NewYork Times datastore creation](./images/nytimes-datastore-creation.png)

Once it's created from the left panel select **Datasets**. 

Click on a Create New Dataset -> from Datastore. Fill out the basic info and select the previously created datastore

[NewYork Times datastore create](./images/nytime-dataset-create.png)

Enter the query given below to get the data

[NewYork Times datastore create2](./images/nytime-dataset-create2.png)

Click next and from schema only chose three columns Record_Date, Cases, Deaths

[Machine learning dataset create](./images/ml-dataset-create4.png)

Click next and confirm.

Now select **Designer (preview)** from the left panel and start designing the model
First drag and drop the nytimes-dataset that is created earlier 

[Machine learning pipeline drag/drop dataset](./images/designer-pipeline-drag-dataset.png)

Now select the **Apply SQL transformation** module  and connect it with a dataset
Click on its setting and write the following query

`select Record_Date, sum(Deaths) as total_deaths, sum(Cases) as total_cases from t2 group by Record_Date`

[Designer pipeline apply sql query](./images/designer-pipeline-apply-sql.png)

Now drag the **Split Data module** and connect it with the **Apply SQL transformation** module go the setting and enter the following

[Designer pipeline split data](./images/designer-pipeline-split-data.png)

Now drag the **Linear Regression** module and **Train model module** and connect them as shown below and do the settings for Train Model

[Pipeline designer linear regression train model](./images/pipeline-design-linear-reg-train-model.png)

Next drag the **Evaluate Model** and connect it with **Score Model**
. 
[Pipeline designer connect with score model](./images/pipeline-design-score-eval.png)

Next drag the **Export Data** module, connect it with **Score Model** and fill the values as given below. 

[Pipeline designer export data module](./images/pipeline-design-exportdata.png)

Also in the Output settings make sure to check the regenerate output option
[Pipeline designer output](./images/pipeline-design-exp-data-output.png)


The pipeline is now ready

Click on the **Submit** button at the top. It will ask to specify the compute target.
Open the Compute target window and do the following settings

[Pipeline designer compute target](./images/design-pipeline-ct.png)

Now submit the pipeline. On submitting the pipeline a window will pop up asking to create an experiment. Create the experiment as given below.

[Pipeline designer experiment create](./images/design-pipeline-exp-create.png)

 You can check the experiment created in the **Experiment** tab

[Pipeline designer experiment created view](./images/exp-created-view.png)

Now submit the pipeline. It will start running as given below.

[Pipeline start running](./images/design-pipeline-start-running.png)

Once its completed you can see a button **Create inference pipeline** gets added on the top.
Click on the button and select the **Batch inference pipeline**

[Convert to batch1](./images/convert-to-batch1.png)

Once it's converted click on submit again and select the existing experiment.

[Batch in existing experiment](./images/batch-in-existing-exp.png)

Once it's done select click on the **Publish** button to publish the pipeline as an endpoint.

[Endpoint creation](./images/endpoint-creation.png)

You can check the endpoint created from the **Pipeline** section

[Endpoint created](./images/ep-created-view.png)




### App reg service 

Now create the Azure **App reg service** that is needed for azure ADF and ML workspace communication and connection. 

To do this click on the **App registration** from azure portal menu.
Click on new registration and do the following settings.

[New app registeration](./images/reg-app-create.png)

Once created go to the overview and note down the Client ID value
[App registeration client](./images/app-reg-client.png)


 Now click on **Certificates and secrets**. Generate a secret and note down its value as well. 
[App registeration add secrets](./images/app-reg-add-secret.png)

Now do the role assignment.
Open the  machine learning work space from the resource group and set this app registration service as its owner.
[App registeration service owner](./images/app-re-ws-permission.png)

Now do the same for ADF. Open ADF from the resource group and set his app registration service as its owner.

[App registeration afd permission](./images/app-reg-adf-permission.png)


### Azure Data Factory Pipeline

Now open the ADF pipeline created as part of one click deployment. This should have an existing pipeline.

Now click on the new pipeline and pick the template option

[Pipeline template option](./images/pipeline-from-template.png)

Pick local template and upload the provide zip file

You need to create  an **AzureMLService1**.
Go ahead click on "Create a new" 
Fill the appropriate values for subscription and workspace for **Service principal ID** and **Service principal key** provide the Client ID and secret key that was saved above during **App reg service** creation.

Click on test connection after its successful make one

Once imported you can see the pipeline. 
This pipeline is executing the sql pipeline as pre req and then invoking the ML model.

Click on the **Machine learning Model execution** and go to **settings**


Here pick the pipeline name and pipeline ID from the drop down populated already and give the experiment name that was provided during the ML model creation

[Machine learning activity settings](./images/ml-activity-setting.png)


Once done click on the publish pipeline and trigger the pipeline.
Once pipeline has run you can check this by running the query in SQL db.

A new table name ml_score will be created and have the results in it.

[Machine learning results store in DB](./images/ml-results-store in db.png)





## Power BI 

Once the data is loaded in to SQL db power BI charts can be drawn from it.
For power BI set up refer to the section **Load data from a Synapse table in Power BI**  (

[Power BI](https://github.com/ayesha-kr/covid-one-click-deployment/tree/stage/datasets/covid-19/newyork-times/powerbi/README.md)

Once connected, graphs can be created for this new data.

[Power BI result](./images/power-bi.png)



