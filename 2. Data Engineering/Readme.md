# Data Engineering

Let's assume that we have massive amounts of data that we would like to transform quickly. Distributed compute is reasonable consideration here, so we are using Azure Databricks which is Spark-based.

Once we have done the data transformations, we will store the transformed data in ADLSGen2.


## 1. Create Databricks Resource

Navigate to the Azure Portal and search for Databricks in the marketplace. Follow the prompts to create a Databricks resource in the resource group and region of your choice. 


## 2. Mount ADLS Gen 2 Storage for ADFS

Once the Databrics reasource is deployed, navigate to it and open the workspace. We need to create a compute cluster to run any of the notebooks we create. 

Navigate to the clusters tabe and create a new cluster. Here you can choose a cluster or a cluster pool. If you choose a cluster pool a node will always be sitting idle so you will always be consuming resources (which costs money). I have selected a generic and small cluster to keep costs down. 

![image](https://user-images.githubusercontent.com/48265599/112589585-79b1ec80-8e55-11eb-93d4-460782fcde2a.png)

Now, we need to mount the ADLS Gen 2 storage. I tried followed this tutorial: https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-use-databricks-spark

This is best practice as it leverages Service Principals to connect the two resources. For security reasons, this is the best approach.

However, I don't have permissions to create app registrations so I looked at the non-production ready, non-service princple approach: https://docs.databricks.com/data/data-sources/azure/adls-gen2/azure-datalake-gen2-get-started.html

Then I got lazy and just coded in my details (very bad, I know). Navigate to the worskpace tab and create a new python notebook. 

```
try:
  dbutils.fs.mount(
    source = "wasbs://onpremcopy@" + storageName + ".blob.core.windows.net/",
    mount_point = "/mnt/onpremcopy",
    extra_configs = {"fs.azure.account.key." + storageName + ".blob.core.windows.net": accessKey})
except Exception as e:
  import re
  result = re.findall(r"^\s*Caused by:\s*(.*)$", e.message, flags=re.MULTILINE)
  if result:
    print(result[-1])
  else:
    print(e)
 ```

Return to your ADLSGen2 and create a second container for your transformed data. Rerun the storage mount code above to mount the output container. 


If you stuff up the mounts and need to clear them, try this: https://kb.databricks.com/data-sources/access-blob-fails-wasb.html                                                                            
```
dbutils.fs.unmount('/mnt')
```

## 3. Use Databricks to convert XML file to Parquet and save in ADLS Gen2

Create a new notebook to perform the data engineering. 

Read the data from the mounted file system:
```
inputFile = "dbfs:/mnt/onpremcopy/test.txt"
initialDF = (spark.read
            .option("header", "true")
            .option("inferSchema", "true")
            .option("delimeter", ",")
            .csv(inputFile)
            )

display(initialDF)
```

Do some basic data engineering. In thise case I am creating a new column based on the existing COUNTRY column
```
import pyspark.sql.functions as F
from pyspark.sql.types import *

def somefunc(value):
  if   value == "USA": 
      return "United States of America"
  elif value == "UK":
      return "United Kingodm"
  else:
      return "European Union"
    
#convert to a UDF Function by passing in the function and return type of function
udfsomefunc = F.udf(somefunc, StringType())

modifiedDF = initialDF.withColumn("COUNTRY_FULL", udfsomefunc("COUNTRY"))
display(modifiedDF)
```

Write out transformed data to storage in parquet format.
```
modifiedDF.write.mode("overwrite").parquet("dbfs:/mnt/engineereddata/parquet")
```

Note that we have used distributed compute to do the data engineering. This means that the data is partitioned. We are writing back in a flat file so those partitions have to come together. There is a bunch of forums/doco talking about why this is not great from a performance perspective when dealing with large data and what the considerations are. One example is demonstrated below, where the output file is the 'part-00000...' file. You can't change this folder structure. 

![image](https://user-images.githubusercontent.com/48265599/112592647-68b7aa00-8e5a-11eb-85a4-264469daf299.png)


