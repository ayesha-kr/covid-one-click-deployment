## Table Of Contents:
1. Deploy Azure Data Factory with optional SQL Server and SQL Database
2. Setup and Configure Alerts for Azure Data Factory
3. Configure Data Share


## Deploy Data Factory with optional SQL Server and SQL Database

#### Prerequisites:
1. Resource group for the deployment.

#### To be provided:
1. Resource Group
2. Data Factory Name
3. Storage Account Name
4. Location
5. Option (Yes or No) to deploy or not to deploy SQL Server, SQL Database, and SQL sink within the pipeline.
6. If selected **Yes**, please provide

   - SQL Server Name 

   - SQL Database Name
   
   - SQL Login Administrator Username
   
   - SQL Login Administrator Password

7. Notification Email
8. Option (Yes or No) to enable Microsoft Teams Notifications
9. Logic App Name
10. Data Share Account Name.
11. Share Name
12. Option (Yes or No) to deploy and use data share.


**NOTE** - If you go with SQL sink, the name of the table where data is written is _**covid_tracking**_.

Click the following button to deploy all the resources.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fayesha-kr%2Fcovid-one-click-deployment%2Fmaster%2Fdatasets%2Fcovid-19%2Fnewyork-times%2Fpublic%2Ftemplates%2Fazuredeploy.json)


#### Configure Firewall Rule
After deployment, to access the newly created SQL server from your client IP, configure the firewall rule as described in the following GIF:-

![Firewall Rule](./images/firewallRule.gif)


#### Manually Trigger Pipeline

After the deployment, you can go inside your resource group open the ADF author and monitor section and trigger the pipeline as shown below:-

![Manual Pipeline Trigger](./images/manual-ADF-public-env-trigger.png)



#### Activate Trigger for the Pipeline

If the trigger is deployed along with the pipeline, you have to explicitly activate that trigger as shown below:-

![Activate Trigger](./images/activateTrigger.gif)



## Setup and Configure Alerts for Azure Data Factory



#### Step 1: Deploy the templates

1. Open git repository for the project and click on **Deploy to Azure**, this will open up a new window.

2. Select your default Azure account(the one you want to deploy Data factory into), and it will take you to the parameters dashboard.

3. Select **"Enable Microsoft Teams Notification"** to yes if you want to send alerts to Microsoft teams too, select no if you want Email alerts only.

4. Enter your email address in the field titled **Notification Email** to send alerts notification to that email. You can't enter more than one email at the time of deployment, but you can add them later on once the deployment is complete. The method for which will be elaborated below.

5. Click on **Purchase** once you are satisfied with all the parameters and wait for the deployment to end.

 Now once the deployment is complete, you will have alerts set up in Azure Data Factory. There will be different features like send alerts to Microsoft Teams (via Logic App) etc. depending on the options you selected at deployment time.



If you have selected Microsoft Teams notification, then your Logic app needs to be authenticated to your "Microsoft Teams" account for it to be able to send notifications.



#### Step 2: Authenticating Microsoft Teams account with Azure Logic App

1. First, navigate to the resource group that contains your deployment and find the resource titled **"msftTeamsConnectionAuth"**. Click on it and navigate to its **"Edit API connection"** option from the sidebar. 

![connection_image](./images/editapiconnections.jpg)

2. In the window there will be a button titled **"Authorize"**, click on it and it will open up Microsoft sign-in page. Enter the MS Teams account credentials and it will authorize you to your MS Teams account.

![authorize_image](./images/authorize.jpg)

3. Click on **"Save"** to save the authorization information and navigate to the resource group.

4. Now click on the deployed logic app, the default name of which is **"TeamsNotify"**. Click on the option **"Logic app designer"** from the sidebar under the heading **"Development tools"**. This will open a visual editor, if there was a problem connecting to teams then it will display a connection error. In that case, refer back to step 1.

![designer_image](./images/logicappdesigner1.jpg)


5. Next, if the connection is successful, click on **switch** button that's in the designer panel. It will open 6 different cases, click on a case and you will see a box labeled **"Post a message (V3)"**, click on that. Next to select **"Team"** and **"Channel"**, click on the cross button at the right side of these two fields to open up the drop-down menu for available Team and Channel in Teams account. If you cant see your "Team" and "Channel", go to step 1, there might be a problem with authentication. Do this for all 6 cases.

![designer_image](./images/logicappdesigner2.jpg)

6. Finally, click on save and your logic app setup is completed.

With this our setup of Alerts is complete.


Next, we elaborate on how to add multiple emails to the action group.

#### Adding multiple emails to the action group

Follow these steps to add multiple emails to receive alerts on.

1. First type "Alerts" in the Azure search bar. Click on "Alerts" and it will take you to the main alerts dashboard.

2. In the top buttons, there is a button **"Manage actions"**, click on that.

![manageactions_image](./images/alertstopbar.jpg)

3. Once in the manage actions pane, there will be a list of all the action groups. Select your action group.

4. Finally at the bottom in section **Notifications**, there is already an email created which is the default email you entered at the deployment time. Here you can add as many emails as you want to send alert notifications.

![email_image](./images/email.jpg)


## Configure Data Share

If you are using data share to get data from a public environment into the customer environment, you need to follow the steps given below after you have run the public side pipeline:-

### Pre-Requisites

#### The user at sending side (public) as well as receiving side (customer) must have 'Owner' role for the data share workflow to function successfully. All the tasks from beginning of the data share workflow at sending side till triggering the snapshot must be performed by the users having 'Owner' role.

### Step 1 - Public Side

1. Open the Data Share Account.

2. Click **Start Sharing your data**.

![data share public](../customer/images/data%20share/1.png)

3. Click on the share named **demo_public_share** (or any other name you have provided while deploying).

![data share public](../customer/images/data%20share/2.png)

4. Under **Datasets** tab, click **Add datasets** and then select **Azure Blob Storage** as dataset type and click *Next*. Then select subscription, resource group, and storage account deployed with current deployment and click *Next*.

![data share public](../customer/images/data%20share/3.png)

![data share public](../customer/images/data%20share/4.png)

![data share public](../customer/images/data%20share/5.png)

5. Select **public** container and click *Next*. And now click **Add datasets**.

![data share public](../customer/images/data%20share/6.png)

![data share public](../customer/images/data%20share/7.png)

6. Now under **Invitations** tab click **Add recipient**.

![data share public](../customer/images/data%20share/8.png)

7. In the blade opened, click **Add recipient** and provide the customer side email and click **Add and send invitation**.

![data share public](../customer/images/data%20share/9.png)

![data share public](../customer/images/data%20share/10.png)



### Step 2 - Customer Side

1. Go to Data Share Invitations.

![data share customer](../customer/images/data%20share/11.png)

2. Click on **demo_public_share** (or any other name you have provided while deploying at the public side).

![data share customer](../customer/images/data%20share/12.png)

3. Agree to the terms of use and provide subscription, resource group, data share account, and received share name. Click **Accept and configure**.

![data share customer](../customer/images/data%20share/13.png)

4. Under the *Datasets* tab, checkmark the dataset and click **Map to target**.

![data share customer](../customer/images/data%20share/14.png)

5. Provide the storage account name (the one deployed currently) along with other options and give the Path as **receivedcopy**. Click *Map to target*. *Note* that the Path is the container name and cannot contain capital letters.

![data share customer](../customer/images/data%20share/15.png)

![data share customer](../customer/images/data%20share/16.png)

6. Now under *Details* tab, click **Trigger snapshot** and then click **Full copy**.

![data share customer](../customer/images/data%20share/17.png)

![data share customer](../customer/images/data%20share/18.png)

7. Optionally you can enable the snapshot schedule if configured at the public side. For that, checkmark the **Daily** schedule under *Snapshot schedule* tab and click *Enable*.

![data share customer](../customer/images/data%20share/19.png)

![data share customer](../customer/images/data%20share/20.png)

