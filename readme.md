Resource Setup
This section covers the initial setup of necessary Azure resources:

Setup:
Create a Databricks workspace named "databrickslearning-ws" (premium tier).

![Image](https://github.com/user-attachments/assets/4509beaa-5e24-4db9-80a2-48f8ac204293)


Note that the storage account is created later in the "Accessing Data Lake from Databricks" section.

Databricks Setup
This section details the configuration of the Databricks environment:

Steps include:
Launch the Databricks workspace from the resource, withfor reference.
Create a cluster with specific configurations, shown in.
Start the cluster, wait for initialization, and create four Python notebooks in a subfolder "Formula1/set-up", covering access methods like access keys, SAS tokens, service principals, and cluster scoped credentials, with.
Accessing Data Lake from Databricks
This section outlines various methods to access Azure Data Lake Storage (ADLS):

Creating Azure Data Lake Storage Gen2:
Create a storage account "formula1dlg2" with standard performance, LRS replication, and enable ADLS Gen2 (hierarchical namespace). Reference.
Creating Containers:
Create containers: raw (bronze), processed (silver), presentation (gold), and demo (practice), with.
Upload "circuits.csv" to the demo container, shown in.
Access Methods:
Using Access Keys: Set Spark config fs.azure.account.key, list files, and read data, with imagesand.
Using SAS Token: Similar steps, with imagesand.
Using Service Principal: Register application, generate secret, set Spark config, and assign role, with.
Using Cluster Scoped Credentials: Set Spark config in cluster, list files, and read data, with imagesand.
Securing Access to Azure Data Lake
This section focuses on enhancing security:

Creating Azure Key Vault:
Create a Key Vault named "formula1-az-key-vault1" in the same resource group, select region (e.g., UK South), and configure access policy. Include images for each step:







Creating Secret Scope for Databricks:
Navigate to workspace, append #secrets/createScope to URL, name scope "formula1-scope", and provide DNS name and Resource ID, with images:





Databricks Secrets Utility:
Create notebook "explore_dbutils_secrets_utility" to manage secrets, avoiding hardcoded values, with images:


Using Secrets in Notebooks:
For access keys, use dbutils.secrets.get and assign to variables, with images:



For SAS token, generate new token, store in Key Vault, and use code:
python

Collapse

Wrap

Copy
formula1dlg2_demo_sas_token = dbutils.secrets.get(scope = 'formula1-scope', key = 'fomula1dlg2-demo-sas-token')
with images:









For service principal, store Client ID, Tenant ID, and Client Secret in Key Vault, replacing hardcoded values, with images:











Using Secrets in Clusters:
Add Spark config in cluster settings, restart cluster, and test access without notebook configurations. Note requirement for mounting in certain subscriptions, with images:



Mounting Data Lake Container to Databricks
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





Tables for Organization
To enhance readability, here is a table summarizing the access methods and their steps:

Method	Key Steps	Image Placeholders
Access Keys	Set Spark config, list files, read data	, 
SAS Token	Set Spark config, list files, read data	, 
Service Principal	Register app, generate secret, set config, assign role	
Cluster Scoped Credentials	Set Spark config in cluster, list files, read data	, 
Another table for Key Vault secrets:

Secret Name	Type	Purpose
formula1dlg2-account-key	Storage Account Key	Secure storage account access
fomula1dlg2-demo-sas-token	SAS Token	Secure demo container access
formula1-app-client-id	Client ID	Service principal authentication
formula1-app-tenant-id	Tenant ID	Service principal authentication
formula1-app-client-secret	Client Secret	Service principal authentication
Conclusion
This comprehensive guide ensures all aspects of the setup are covered, from initial resource creation to secure and efficient data access, with detailed steps and image placeholders. The formatted README.md file is ready for GitHub, providing a professional resource for users to follow, with relative paths for images ensuring accessibility.

Key Citations
Image - Azure Databricks learn how to read image files using Azure Databricks
GitHub - Azure Databricks hands-on step by step workshop
Formula1 Racing Project using Azure Databricks, ADLS & ADF Medium article
GitHub - data pipeline project build on databricks and azure
Read CSV files - Azure Databricks Microsoft Learn
Image | Databricks Documentation AWS learn how to read image files
GitHub - Real World Project on Formula1 Racing using Azure Databricks
Sample datasets - Azure Databricks Microsoft Learn
GitHub - Azure-based solution for ingesting and analyzing Formula 1 data
