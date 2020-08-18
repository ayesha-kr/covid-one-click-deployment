# One Click Deployment of Covid-19 Data Flow
This is a guide for processing the following open Covid-19 datasets, using the Microsoft Azure data factory. 


## 1. Introduction
We have seen COVID-19 changing the landscape around us in a matter of a few months and as anticipated, businesses and communities are trying to adapt to this situation and continue their operations while minimizing the risks associated with the current situation and the long term pandemic recovery phase, the future. We live in a data-driven world, where many organizations are trying to fight this pandemic and start the recovery process. This blog post is about utilizing open COVID-19 datasets in Microsoft Azure data factory to get meaningful insights that can help organizations make informed decisions and initiate the recovery phase.


## 2. Open COVID-19 datasets
We have published the ARM templates for utilizing the following dataset. The details include, one click deployment for public and customer environment, and procedure to create powerBI dashboard.

Dataset | Descriptions | Link | Status
------- | ------------ | ---- | ------
[Definitive Healthcare USA](https://coronavirus-resources.esri.com/datasets/definitivehc::definitive-healthcare-usa-hospital-beds?geometry=110.039%2C-16.820%2C-135.000%2C72.123) | The latest data on COVID-19 about USA Hospital Beds. | [definitive-healthcare](https://github.com/ayesha-kr/msft-covid/tree/master/datasets/covid-19/definitive-healthcare) | Available


## 3. Data Flow Architecture Diagram
 ![Azul_Data_Pipeline](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/images/arch.png)

## 4. Data Flow deployment steps
  You can see that the repo has three sections public, customer and power BI. Go inside each of the directory to deploy that relavent section.  

## 5. CI/CD setup for Azure Data Factory

We have provided an Azure pipelines yaml that can be used to set up CI/CD for Azure Data Factory. The pipeline is triggered when you publish changes in the Dev data factory (The one that is connected to Github). THe pipelines reads teh generated ARM templates from teh Dev Data factory and deploys it to Prod. 

You can follow the instructions given in the following link to set up CI/CD using Azure Pipelines:-

[Azure Data Factory CICD](./datasets/covid-19/definitive-healthcare/AzurePipelines-CICD/readme.md)

## 6. Connect Azure Boards with the Github Repo

Boost your team's productivity with Boards, Backlogs, and Sprints for even the most complex project. Simply connect your GitHub repo to Azure Boards and start linking commits and PRs to work items.

By connecting your Azure Boards project with GitHub.com repositories, you support linking between GitHub commits and pull requests to work items. You can use GitHub for software development while using Azure Boards to plan and track your work.

When you make the connection from Azure Boards, the list of GitHub repositories correspond to ones that you allow Azure Boards to access. You can limit which repositories Azure Boards can access overall, and limit what a particular project can access or split the management of work across different Azure Boards projects.

Please follow the instructions given in the following link to connect Azure Boards to Github:-

[Connect Azure Boards to Github](https://docs.microsoft.com/en-us/azure/devops/boards/github/connect-to-github?view=azure-devops)


## 7. Configure Data Share

If you are using data share to get data from public environment into customer environment then you need setup data share account and a share on the public side and send a notification to the customer side where too a data share account must be present to accept the invitation. To deploy Data Share account on either of the two environments, you will need to select the option Yes/No to do so. If Yes is selected at public side, then the data share account and a share will be deployed at public side while if you select Yes on the customer side, only a data share account will be deployed and the data pipeline that will be deployed will the one with copy data activity that copies data from public staorage to the customer one.

You can follow the instructions given in the following link to set up Data share account and create/accept invitation and other configurations:-

[Azure Data Factory CICD](./datasets/covid-19/definitive-healthcare/customer/readme.md)

1. Open the Data Share Account.

2. Click **Start Sharing your data**.

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/1.png)

3. Click on the share named **demo_public_share** (or any other name you have provided while deploying).

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/2.png)

4. Under **Datasets** tab, click **Add datasets** and then select **Azure Blob Storage** as dataset type and click *Next*. Then select subscription, resource group and storage account deployed with current deployment and click *Next*.

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/3.png)

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/4.png)

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/5.png)

5. Select **public** container and click *Next*. And now click **Add datasets**.

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/6.png)

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/7.png)

6. Optionally you can enable the share subscription under *Settings* or *Share Subscriptions* tab.

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/7-a.png)


7. Now under **Invitations** tab click **Add recipient**.

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/8.png)

8. In the blade opened, click **Add recipient** and provide the customer side email and click **Add and send invitation**.

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/9.png)

![data share public](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/10.png)


### Step 1 - Customer Side

1. Go to Data Share Invitations.

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/11.png)

2. Click on **demo_public_share** (or any other name you have provided while deploying at public side).

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/12.png)

3. Agree to the terms of use and provide subscription, resource group, data share account and received share name. Click **Accept and configure**.

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/13.png)

4. Under the *Datasets* tab, check mark the dataset and click **Map to target**.

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/14.png)

5. Provide the storage account name (the one deployed currently) along with other options and give the container name as **staging**. Click *Map to target*.

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/15.png)

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/16.png)

6. Now under *Details* tab, click **Trigger snapshot** and then click **Full copy**.

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/17.png)

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/18.png)

7. Optionally you can enable the snapshot schedule if configured at public side. For that, check mark the **Daily** schedule under *Snapshot schedule* tab and click *Enable*.

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/19.png)

![data share customer](https://github.com/ayesha-kr/covid-one-click-deployment/blob/master/datasets/covid-19/definitive-healthcare/customer/images/data%20share/20.png)



## 8. Contact us
We hope that this blog is helpful for you. In case you have any questions, feel free to reach out to us at ayesha.khaliq@emumba.com OR hamza.rashid@emumba.com.

