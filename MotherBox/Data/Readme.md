
## Overview
The Motherbox application is composed of a couple of key artifacts that are responsible for
sourcing, processing and landing data from the AIP system:

- Azure Data Factory
- Azure KeyVault
- Azure SQL Database (Hyperscale)

In this section will provide an overview of how the application is architected and built.

### Data Overview
Motherbox data is stored in a traditional star schema. There's are two fact tables that store current year (GlobalUsageDaily) and previous year (GlobalUsageDailyPrevYear) usage data. Supporting these tables is a a set of four dimension tables for Customer, Subscription, Service Hierachy and Date data.

![SQL DB Model Overview](SQL_Data_Model.png)

Given the large dataset, we chose to build the database on SQL DB HyperScale. The database is dynamically scaled up while loading the data to minimize load and then on load completion it's scaled back down.

### Security
All credentials used by ADF to connect to the AIP system as well to the database are stored in Azure Keyvault. We utilized Azure Managed Identities to grant ADF permissions to manage Azure resources as needed.

### ADF Overview
The data is extracted from the AIP system using Kusto queries and it's stored in SQL DB database.
This task is accomplished by an ADF job that's implemented as a series of pipelines. The core/master pipeline is designed as follows:

![ADF Master Pipeline Overview](ADF_Pipeline_Overview.png)

### Azure DevOps Overview

In order to simplify the deployment and operational maintenance, we built an Azure Devops
pipeline that deploys changes committed to the git repository. We built a multi-stage pipeline
that builds out the complete infrastructure.The overview below shows the various tasks used to build out the pipeline.

![DevOps Pipeline Overview](DevOps_Pipeline_Overview.png)