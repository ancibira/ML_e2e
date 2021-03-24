# ML_e2e
End to end approach for Machine Learning on the Azure Platform.

I have deployed the following architecture to demonstrate an end-to-end approach to machine learning on the Azure Platform.

![image](https://user-images.githubusercontent.com/48265599/112255620-aed40880-8cb6-11eb-9bd2-a5d1bacf4276.png)

For this example, I have an XML file on my local machine. We will follow this file through the data engineering and data science lifecycle.


## 1. Ingest the Data

Azure Data Factory can copy your data from on-premises to Azure. This requires the use of a self-hosted integration runtime which you will run on your local machine. 

Due to the semi-structured nature of XML, we have chosen Azure Data Lake Storage Gen 2. 


## 2. Data Engineering

Let's assume that we have massive amounts of data that we would like to transform quickly. Distributed compute is reasonable consideration here, so we are using Azure Databricks which is Spark-based. 

Once we have done the data transformations, we will store the transformed data in ADLSGen2. 


## 3. Data Science

Now that we have a clean dataset, we can register it in Azure Machine Learning for auditability and tracing purposes when we create our ML models. We can create our models via notebooks or through the GUI designer. 

Once we have trained and tested our model, we can register it so we can keep track of the verisons. Then we deploy the model to an Azure Kubernetes Service cluster and inference via REST API or a web service endpoint. 

