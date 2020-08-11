# Setup and Configure Alerts for Azure Data Factory

## Step 1: Deploy the templates

1. Open git repository for the project and click on **Deploy to Azure**, this will open up a new window.

2. Select your default Azure account(the one you want to deploy Data factory into), and it will take you to parameters dashboard.

3. Select **"Enable Microsoft Teams Notification"** to yes if you want to send alerts to Microsoft teams too, other select no if you want Email alerts only.

4. Enter your email address in the field titled **Notification Email** to send alerts notification to that email. You cant enter more than one email at the deployment time, but you can add them later on once the deployment is completed. The method for which will be elaborated below.

5. Click on **Purchase** once you are satisfied with all the parameters and wait for the deployment to end.

 Now once the deployment is complete, you will have alerts setup in data factory. There will be different features like send alerts to Microsoft Teams(via Logic App) etc depending on the options you selected at the deploy time.



If you have selected Microsoft Teams notification, then your Logic app needs to be authenticated to your "Microsoft Teams" account for it to be able to send notifications.
## Step 2: Authenticating Microsoft Teams account with Azure Logic App

1. 
