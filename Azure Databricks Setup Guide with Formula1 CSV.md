# Azure Databricks Setup Guide with Formula1 CSV - Azure Databricks, PySpark, Python, Data Lake Storage

# Resource Setup

## Setup

1. First, I created “**databrickslearning-ws”** for Databricks workspace resource **(premium tier)**

For the Storage account resource I created this later on in the section*: ***Accessing Data Lake from Databricks**

![image.png](https://github.com/DarrenDavy12/Azure-Databricks-Setup-Guide-with-Formula1-CSV/blob/main/images/image.png)

Overview in resource groups:

![image.png](https://github.com/DarrenDavy12/Azure-Databricks-Setup-Guide-with-Formula1-CSV/blob/main/images/image%201.png)

## Databricks Setup

1. Once those resources are created and deployed I launched the Databricks workspace from the resource.

![image.png](https://github.com/DarrenDavy12/Azure-Databricks-Setup-Guide-with-Formula1-CSV/blob/main/images/image%202.png)

1. Created a cluster and these were the configurations

![image.png](https://github.com/DarrenDavy12/Azure-Databricks-Setup-Guide-with-Formula1-CSV/blob/main/images/image%203.png)

1. Once done, I started the cluster waited for it start up and then created my notebooks.

I made four python notebooks inside a freshly created subfolder “Formula1/set-up”, containing different ways of accessing in Data Lake including **access keys**, **SAS Authentication token**, **service principal** or by **cluster scoped credentials**.  

![image.png](image%204.png)

# Accessing Data Lake from Databricks:

## 1. Creating Azure Data Lake Storage Gen2

I created a storage account called **“formula1dlg2”,** chose **standard performance** while selecting **LRS (Locally zone redundant)** as replication setting and Azure Data Lake Storage Gen2 (check the **hierarchical namespace** box), left everything else as default and created the resource. 

![image.png](image%205.png)

## 2. Creating the Containers inside Storage account (medallion Architecture)

1. raw - for raw data (bronze)
2. processed - for transformed data (silver)
3. presentation - for analysed data (gold)
4. demo - *this is used for practicing with data lake outside of the project*

![image.png](image%206.png)

I went into the demo container to upload a .csv file called ‘circuits.csv’. 

![image.png](image%207.png)

## 3. Access ADLS (Data Lake) using access keys

1. Set the spark config `fs.azure.account.key`
2. List files from demo container
3. Read data from circuits.csv file

![image.png](image%208.png)

![image.png](image%209.png)

## 4. Access ADLS (Data Lake) using SAS token

1. Set the spark config for SAS token
2. List files from demo container
3. Read data from circuits.csv file

![image.png](image%2010.png)

![image.png](image%2011.png)

## 5. Access ADLS (Data Lake) using Service Principal

1. Register Azure AD Application / Service Principal
2. Generate a secret / password for the application
3. Set Spark Config wit App / Client Id, Directory / Tenant Id & Secret
4. Assign Role 'Storage Blob Data Contributor' to the Data Lake.

![image.png](image%2012.png)

## 6. Access ADLS (Data Lake) using cluster scoped credentials

1. Set the spark config `fs.azure.account.key` in the cluster
2. List files from demo container
3. Read data from circuits.csv

![image.png](image%2013.png)

![image.png](image%2014.png)

# Securing Access to Azure Data Lake:

## 1. Creating Azure Key Vault

![image.png](image%2015.png)

1. Selected the same resource group I created at the start of the project.
2. Gave the name `formula1-az-key-vault1` and selected the closest region which in my case is `UK South`

![image.png](image%2016.png)

1. In the next tab, on access configuration I selected ‘vault access policy’

![image.png](image%2017.png)

1. Left all other tabs as default and created the key vault.

![image.png](image%2018.png)

1. Once the key vault resource is deployed, I went inside the resource and selected the secrets tab to generate a new secret. 

![image.png](image%2019.png)

![image.png](image%2020.png)

1. Copied key inside `spark conf` in the first notebook: “access_adls_using_access_keys”

![image.png](image%2021.png)

1. Back inside the key vault on another tab I pasted the key, gave a name to the secret and content type. 

Secret name: “formula1dlg2-account-key”

Secret value: [Copied key from notebook 1] 

Content type: “Storage Account Key”

![image.png](image%2022.png)

## 2. Creating Secret Scope for Databricks

1. Firstly, I went back to the **Databricks workspace homepage**. 
2. Added  `#secrets/createScope` to the end of the URL of the tab on my browser.

![image.png](image%2023.png)

1. On the create secret scope page, I named the scope ‘**formula1-scope**’, selected ‘**All Workspace Users**’ as the manage Principal and provided the DNS name and the Resource ID. 

![image.png](image%2024.png)

1. Getting the DNS name and the Resource ID from my key vault in which I created earlier in the properties tab 
    
    

![image.png](image%2025.png)

1. Afterwards I pasted the DNS name and resource ID inside the secret scope and hit ‘create’

![image.png](image%2026.png)

![image.png](image%2027.png)

## 3. Databricks Secrets Utility

1. I made a new notebook called “explore_dbutils_secrets_utility” 

![image.png](image%2028.png)

1. Next, I made sure that any hard code like the account key value and secrets that are in plain text are now avoided and not exposed to people outside of the organization. But anyone that has access to this Databricks workspace has access to the secret scope, I can manage those users within the organization at any time. 

![image.png](image%2029.png)

## 4. Using Secrets to access Azure Data Lake using notebooks (for access keys notebook)

1. First, I added a new line at the top of my first notebook: access azure data lake using access keys and want to add a get command. This get command is the same command I ran earlier inside of the fifth notebook named “explore_dbutils_secrets_utility” including bringing the scope alias and the secret key alias. This brought back the secret value. 

![image.png](image%2030.png)

1. Then I assigned that get command to a variable called “formula1dlg2_account_key” and executed that line again. Replaced the actual value of the key in the next line with the new variable. Once that was done, I ran all the code in the notebook, and all commands were successfully executed so I can still read the .csv file. 

![image.png](image%2031.png)

![image.png](image%2032.png)

I can use secrets from the secret scope and the key vault and use that within this notebook to successfully access the storage account and data from here, with no hard-coded values present. 

## 5. Using Secrets to access Azure Data Lake using notebooks (for SAS token and service principal notebooks)

1. **SAS token Notebook** - I need to make another SAS token because I configured the token only be valid for 8 hours and it has expired so I made another SAS token.

1. So, I headed to my storage account → containers → demo → three dot menu → generate SAS token.

![image.png](image%2033.png)

1. I applied the read and list permissions, along with expire time being 8 hours like the previous token. 

![image.png](image%2034.png)

1. Then I clicked “generate SAS token and URL and copied the top URL. 

![image.png](image%2035.png)

1. Back on the key vault resource I created earlier, I generate/import a new secret, called the secret “fomula1dlg2-demo-sas-token”, with the secret value being the copied URL from the storage account demo container alongside a content type called “SAS Token”. 

![image.png](image%2036.png)

![image.png](image%2037.png)

![image.png](image%2038.png)

1. Just like the get command in the access keys notebook, ran a get command that was assigned by a variable:

`formula1dlg2_demo_sas_token = dbutils.secrets.get(scope = 'formula1-scope', key = 'fomula1dlg2-demo-sas-token')`

![image.png](image%2039.png)

1. In the next line replaced the hard-coded SAS token with the variable instead, once I did that, I ran all the code, and they all were executed successfully. 

![image.png](image%2040.png)

![image.png](image%2041.png)

1. **Service principal notebook** - On the service principal notebook, I copied the client ID value and stored it in the key vault. Gave the secret a name called “formula1-app-client-id” and gave the content type the name “Client ID” 

![image.png](image%2042.png)

![image.png](image%2043.png)

1. I did the same for the other two key values in the service principal notebook, “tenant_id” and “client_secret”.

**Tenant ID key vault details** - name as “formula1-app-tenant-id” and content type: “Tenant ID” 

![image.png](image%2044.png)

![image.png](image%2045.png)

![image.png](image%2046.png)

**Client Secret vault details:** name as “formula1-app-client-secret” and content type as “Client Secret” 

![image.png](image%2047.png)

![image.png](image%2048.png)

![image.png](image%2049.png)

1. Next replaced the first cell in the notebook with three get commands for each key. 

![image.png](image%2050.png)

![image.png](image%2051.png)

1. Ran all the cells and they executed successfully.

![image.png](image%2052.png)

## 6. Using secrets utility in Clusters

1. Inside the cluster configuration, in advanced options/Spark config I added a new line “fs.azure.account.key.formula1dlg2.dfs.core.windows.net {{secrets/formula1-scope/formula1dlg2-account-key}}” and clicked “confirm” 

![image.png](image%2053.png)

1. After that I restarted the cluster, once the cluster was up and running again, I went into the “**Access Azure Data Lake using cluster scoped credentials**” notebook to see if I can access the data without providing any cluster configurations in the notebook. To do that I simply just ran all cells in the notebook, without any spark configuration commands like (’spark.conf’) and they executed successfully. 

![image.png](image%2054.png)

1. Afterwards I tidied up the cluster advanced options to default settings by just removing that line I added in the spark config section, and restarted the cluster again. 

***Quick note:** I cannot mount container to Databricks **without** the added line in the spark config “fs.azure.account.key.formula1dlg2.dfs.core.windows.net {{secrets/formula1-scope/formula1dlg2-account-key}}” if I have a company or student subscription with azure.* 

![image.png](image%2055.png)

# Mounting Data Lake container to Databricks:

**Benefits:** 

**Access data without requiring credentials** 

**Access files using file semantics storage URLs (e.g./mnt/storage1)**

**Stores files to object storage (e.g. Azure Blob), so you get all benefits from Azure**

**Recommended solution for access Azure Storage until the introduction of Unity Catalog** 

## 1. Databricks File System (DBFS)

1. First, I made a new notebook called “explore_dbfs_root” and ran “display(dbutils.fs.ls(’/’))” to list all the folders in the databricks file storage root.

![image.png](image%2056.png)

1. Afterwards I needed to enable the use of DBFS file browser in settings → advanced → DBFS File Browser and set that to on. 

![image.png](image%2057.png)

1. Next, I went to the catalog tab in Databricks workspace and clicked on “Browse DBFS” in the top right. 

![image.png](image%2058.png)

1. So, I clicked on “upload” and uploaded a file called ‘circuits.csv’ 

![image.png](image%2059.png)

![image.png](image%2060.png)

![image.png](image%2061.png)

1. Back in the “explore_dbfs_root” notebook I ran the list files command again and now the file storage path is now added. 

![image.png](image%2062.png)

1. The next command I ran was too list only the folders inside file store.

![image.png](image%2063.png)

1. Then I ran the spark read csv command to list all the data from the circuits.csv file. I also removed the dbfs path to check if the file is mounted in which it was and executed successfully. 

*With dbfs path in spark read command:* 

![image.png](image%2064.png)

*Without dbfs path in spark read command:*

![image.png](image%2065.png)

This is to show that you can upload files to file store using DBFS browser enabled. This is a way to avoid using access keys or SAS tokens to read files within the notebook. Now the downside is that I can’t restrict access to specific users, so make sure files I am putting into file store is allowed to be used by the rest of the team who has access to this workspace, also this folder and the storage will be dropped when I delete the workspace. 

**This is not ideal to keep customer data here.** 

**It is good to use the file store folder in two scenarios:**

**One for quick analysis on a set of small volume of data and no stress of authentication.**

**The other scenario is for keeping files in the file store folder as part of markdown.** 

## 2. Mounting Azure Data Lake Storage Gen2

1. First off, I clone the service principal notebook I created earlier. Naming the notebook “mount_adls_service_principal”. 

![image.png](image%2066.png)

1. Got client_Id, tenant_Id and client_secret from key vault

![image.png](image%2067.png)

1. Set Spark Config with App/ Client id, Directory/ Tenant Id, and secret

![image.png](image%2068.png)

1. Called file system utility mount to mount the storage

![image.png](image%2069.png)

I ran the first three cells to mount my storage account container to the Databricks workspace and I will be able to use the file system semantics rather than the ABFS protocol. 

I my case the directory was already mounted. 

![image.png](image%2070.png)

1. Explored other file system utilities related to mount (list all mounts, unmount)

![image.png](image%2071.png)

1. Even added the ‘[spark.read](http://spark.read)’ command to read the .csv from the mount point “/mnt/”.

![image.png](image%2072.png)

1. To list all mounts, I ran “display(dbutils.fs.mounts())” now I have a view off all mounts in the file system. 

![image.png](image%2073.png)

*Quick note: To unmount storage account container, I ran the “dbutils.fs.unmount()” command. I unmounted the demo container containing the .csv file.* 

![image.png](image%2074.png)

## 3. Mounting Azure Data Lake Storage Gen2 with Python Function

1. I wrote out a python function to mount data lake storage in new notebook.

*This is for the demo container:* 

![image.png](image%2075.png)

This is good for mounting different containers in a work environment. It stops me having to hard code every time I need to mount a container like in the previous section. 

1. Next, I mounted the raw container using the “mount_adls” function I created in the last step, alongside the values being my storage account name and the container name. 

![image.png](image%2076.png)

Now I can pass through any container by running this python function I created and pass through the desired storage account name and container. 

1. I also added a line in the first cell containing the python function “mount_adls” and it makes sure that any mount which already mounted will unmount and mount again if I run the python function to avoid the error saying mount already mounted. 

Example: 

![image.png](image%2077.png)

To fix this I added this line in the python function above in the first cell. It passed through with no error.

![image.png](image%2078.png)

1. Afterwards I ran “mount_adls” function again but for the processed container instead in a new cell. 

![image.png](image%2079.png)

1. Same again for the presentation container in a new cell too. 

![image.png](image%2080.png)

So, in a team in real world environment, one person will write out the function and the others can just invoke the function afterwards.
