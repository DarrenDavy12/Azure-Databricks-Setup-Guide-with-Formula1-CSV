# Azure Databricks Setup Guide with Formula1 CSV - Azure Databricks, PySpark, Python, Data Lake Storage

## Azure resources setup:

Create a Databricks workspace named "databrickslearning-ws" (premium tier)

![Image](https://github.com/user-attachments/assets/4509beaa-5e24-4db9-80a2-48f8ac204293)


Note that the storage account is created later in the "Accessing Data Lake from Databricks" section.

![Image](https://github.com/user-attachments/assets/df8c79e1-3006-4b06-a17c-60badfe29415)

## Databricks Setup
This section details the configuration of the Databricks environment:

## Steps include:
Launch the Databricks workspace from the resource.
![Image](https://github.com/user-attachments/assets/255462fd-930a-4137-ade3-52daf6a99663)

Create a cluster with specific configurations.
![Image](https://github.com/user-attachments/assets/83fc8d6c-6f91-4734-bca3-85d8c7ce4148)


Start the cluster, wait for initialization, and create four Python notebooks in a subfolder "Formula1/set-up", covering access methods like access keys, SAS tokens, service principals, and cluster scoped credentials.
![Image](https://github.com/user-attachments/assets/cdf528bf-396e-4b2c-a74a-9219cfc01f67)


Accessing Data Lake from Databricks
This section outlines various methods to access Azure Data Lake Storage (ADLS):
![Image](https://github.com/user-attachments/assets/86b521be-fc21-4d88-aa50-ee37cc7fa2d3)


Creating Azure Data Lake Storage Gen2:
Create a storage account "formula1dlg2" with standard performance, LRS replication, and enable ADLS Gen2 (hierarchical namespace).
![Image](https://github.com/user-attachments/assets/c731e0f9-3ac1-4e44-a596-a545c78e324e)


Creating Containers:
Create containers: raw (bronze), processed (silver), presentation (gold), and demo (practice)
![Image](https://github.com/user-attachments/assets/2cf94c30-945b-48ab-b61e-d8db8970fb0f)


Upload "circuits.csv" to the demo container.
![image](https://github.com/user-attachments/assets/16107883-d8e3-432f-b106-fb2ace704bd4)


Access Methods:
Using Access Keys: Set Spark config fs.azure.account.key, list files, and read data.
![image](https://github.com/user-attachments/assets/50aaf024-27ef-46cf-874b-2c8e7aa731b2)

![Image](https://github.com/user-attachments/assets/69e0c7de-79a9-4e8d-9928-be810db3cb71)

Using SAS Token: Similar steps, with images.
![Image](https://github.com/user-attachments/assets/eb103377-440e-41a4-9c0e-aff5c59f4b2b)

![Image](https://github.com/user-attachments/assets/f3d1e397-d787-415a-85a1-d704f6856618)


Using Service Principal: Register application, generate secret, set Spark config, and assign role.
![Image](https://github.com/user-attachments/assets/4e829266-171f-4d22-98e6-d024f1bb39c7)

Using Cluster Scoped Credentials: Set Spark config in cluster, list files, and read data.
![Image](https://github.com/user-attachments/assets/a3ec9cc9-89cb-4c5a-bb2e-549f3109bf7b)

![Image](https://github.com/user-attachments/assets/d6b797c2-b588-4cb5-9c2e-f2c9ca76d211)

Securing Access to Azure Data Lake
This section focuses on enhancing security:

![Image](https://github.com/user-attachments/assets/2c4a81f7-5658-4326-98fb-4f72f696aa3a)

![Image](https://github.com/user-attachments/assets/e84da5b3-ffe5-49c5-b7c2-eb4615447bf3)

## Creating Azure Key Vault:
Create a Key Vault named "formula1-az-key-vault1" in the same resource group, select region (e.g., UK South), and configure access policy. 

![Image](https://github.com/user-attachments/assets/bdf6cf4c-f9a5-4225-a252-7ed838b931b9)

![Image](https://github.com/user-attachments/assets/4ae8af80-36d3-461f-b7a6-55b5da005d0d)


## Creating Secret Scope for Databricks:
Navigate to workspace, append #secrets/createScope to URL, name scope "formula1-scope", and provide DNS name and Resource ID.

![Image](https://github.com/user-attachments/assets/584e4359-4879-45a9-86b2-9faee4e1bf4e)



## Databricks Secrets Utility:
Create notebook "explore_dbutils_secrets_utility" to manage secrets, avoiding hardcoded values.

![Image](https://github.com/user-attachments/assets/393107ff-ef90-4697-a920-9de61fb3b76b)


## Using Secrets in Notebooks:
For access keys, use dbutils.secrets.get and assign to variables.
![Image](https://github.com/user-attachments/assets/30228de8-38c8-4a52-84e5-130fb977cd49)


For SAS token, generate new token, store in Key Vault, and use code:
python

Collapse

Wrap

Copy
formula1dlg2_demo_sas_token = dbutils.secrets.get(scope = 'formula1-scope', key = 'fomula1dlg2-demo-sas-token')
with images:









For service principal, store Client ID, Tenant ID, and Client Secret in Key Vault, replacing hardcoded values, with images:











## Using Secrets in Clusters:
Add Spark config in cluster settings, restart cluster, and test access without notebook configurations. Note requirement for mounting in certain subscriptions, with images:



## Mounting Data Lake Container to Databricks
This section covers mounting for easier data access:

Benefits (unordered list):
Access data without requiring credentials
Access files using file semantics storage URLs (e.g., /mnt/storage1)
Stores files to object storage (e.g., Azure Blob), leveraging all Azure benefits
Recommended solution until Unity Catalog introduction
Databricks File System (DBFS):
Create notebook "explore_dbfs_root", list root folders with display(dbutils.fs.ls('/')), enable DBFS File Browser, and upload files. Note limitations for customer data, with images:










Mounting Azure Data Lake Storage Gen2:
Clone service principal notebook, use Key Vault credentials, mount with dbutils.fs.mount, and explore utilities. Include images:









Mounting with Python Function:
Create mount_adls function for dynamic mounting, shown in, and use for other containers, adding unmount logic to avoid errors, with images:







