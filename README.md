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
 ![Azul_Data_Pipeline](https://github.com/ayesha-kr/msft-covid/blob/master/images/Azul%20data%20pipeline%20diagram.png)

## 4. Data Flow deployment steps
  You can see that the repo has three sections public, customer and power BI. Go inside each of the directory to deploy that relavent section.  

## 5. Contact us
We hope that this blog is helpful for you. In case you have any questions, feel free to reach out to us at ayesha.khaliq@emumba.com, hamza.rashid@emumba.com.

