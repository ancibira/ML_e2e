# Data Ingestion


We will take a sample XML file stored on our local machine and use Azure Data Factory to copy it to Azure Data Lake Storage Gen 2. 

## 1. Obtain Sample File

For this example I am using a sample XML file which you can download from this folder. Remeber where this file is. 

## 2. Create the Azure Data Lake Storage Gen 2

Create an Azure Storage resource through the Azure Portal and searching for storage in the market place. Follow the prompts to install and configure the resource in your resource group and region of your choosing. Ensure this is an ADLSGen2 account by selecting hierarchical namespace. 

![image](https://user-images.githubusercontent.com/48265599/112587911-96005a00-8e52-11eb-8609-8b2041394fc7.png)

Once the resource has been deployed, navigate to it and create a container called 'onpremcopy' (or whatever you want to call it). This will be the container where your on-prem file will be copied to.


## 2. Create Azure Data Factory Resource

Create an Azure Data Factory resource through the Azure Portal and searching for data factory in the market place. Follow the prompts to install and configure the resource in your resource group and region of your choosing. 

![image](https://user-images.githubusercontent.com/48265599/112586977-d6f76f00-8e50-11eb-888b-6414cf71a295.png)

Once the resource has been deployed, navigate to it and open the authoring tool. From the landing page, select 'Copy Data' to open a wizard you can click through to create the copy pipeline. 

![image](https://user-images.githubusercontent.com/48265599/112587223-5422e400-8e51-11eb-8819-5e1b1677dc81.png)

## 3. Create Source 

Create a linked service by following the steps. For the source service, select File System and proceed to set up a self-hosted integration runtime. The self-hosted integration runtime is required because you are moving data from on-premesis. For cloud to cloud movement you can use the Azure integration runtime. Read more here: https://docs.microsoft.com/en-us/azure/data-factory/concepts-integration-runtime

If you have issues with the Express setup (like I did because I had an existing integration runtime on my machine that I had to delete), then follow the manual setup process. This link explains it in more detail: https://www.mssqltips.com/sqlservertip/5812/connect-to-onpremises-data-in-azure-data-factory-with-the-selfhosted-integration-runtime--part-1/

Note: the username is actually the email address of your local machine login. 

Follow the steps to select and configure the appropriate dataset - which is the XML file we saved earlier.


### 4. Create Sink

Continue to follow the wizard to link the ADLS Gen 2 resource and the container you created earlier. 


### 5. Review Pipeline

Manually trigger the pipeline and inspect the results in your data lake storage. 

![image](https://user-images.githubusercontent.com/48265599/112588916-50449100-8e54-11eb-9d06-c3bc1f8206d3.png)


The issues I had to fix are:

Select collection reference in the pipeline mapping. 
![image](https://user-images.githubusercontent.com/48265599/112588276-36567e80-8e53-11eb-8719-9d3f09ffbd47.png)

Ensure you've selected 'first row as header' and 'no escape character' (could choose others, just be aware of the formatting of the XML file) for the source dataset. 
![image](https://user-images.githubusercontent.com/48265599/112588321-4b331200-8e53-11eb-87a9-28421819995a.png)



