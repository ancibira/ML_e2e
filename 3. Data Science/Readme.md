# Data Science

Now that we have a clean dataset, we can register it in Azure Machine Learning for auditability and tracing purposes when we create our ML models. We can create our models via notebooks or through the GUI designer.

Once we have trained and tested our model, we can register it so we can keep track of the verisons. Then we deploy the model to an Azure Kubernetes Service cluster and inference via REST API or a web service endpoint.

## 1. Create the Azure Machine Learning resource

Navigate to the Azure Portal and search for Azure Machine Learning in the marketplace. Follow the prompts to create an AML resource in the resource group and region of your choice.

Note: Default AML storage can't be ADLS Gen 2 https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-workspace-cli


## 2. Register the transformed dataset in AML

Navigate to the newly created resource and open the AML Studio. This is where you do all your authoring work.

Select datasets from the left hand pane and follow the wizard to create a dataset from a datastore. 

![image](https://user-images.githubusercontent.com/48265599/112593105-1f1b8f00-8e5b-11eb-970b-482e771bbeb7.png)

You will have to create a new datastore, but this just involves linking your ADLSGen2 service. Follow the wizard to complete the remainder of the steps.


## 3. Create Compute Instance

Navigate to compute on the left hand pane and create a new compute instance. If you are doing some beefy AI/ML and need GPUs, check out your Azure quotas and request more if required: https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-quotas
![image](https://user-images.githubusercontent.com/48265599/112593358-7e799f00-8e5b-11eb-8d07-b2241c80fe6d.png)


## 4. Use Notebooks to Train and Deploy an ML model

The notebook is attached. 
