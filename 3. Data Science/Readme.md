# Data Science

Now that we have a clean dataset, we can register it in Azure Machine Learning for auditability and tracing purposes when we create our ML models. We can create our models via notebooks or through the GUI designer.

Once we have trained and tested our model, we can register it so we can keep track of the verisons. Then we deploy the model to an Azure Kubernetes Service cluster and inference via REST API or a web service endpoint.

# 1. Create the Azure Machine Learning resource

Navigate to the Azure 
Note: Default AML storage can't be ADLS Gen 2 https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-workspace-cli
