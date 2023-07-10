# Objective 1: Dedicated SQL Pool  
* This pipeline takes the JSON data that is in FHIR standard format from our "raw" ADLS container and converts it to parquet.  Since Parquet is a columnar compressed file format this makes it much faster to import and work with the data.  We store the parquet output in our "processed" container in ADLS under a folder called "Observation".
![image](https://user-images.githubusercontent.com/59613090/193125969-460256eb-b025-4e56-8e16-ad10677b54f2.png)


* We plan to eventually load this data into Dedicated SQL Pool in a table called [fhir].[ObservationMain].  We need to extract the data needed for the table, clean it, and write it back to ADLS.  The second activity in our pipeline handles all of this in a single Synapse Spark Notebook.
![image](https://user-images.githubusercontent.com/59613090/193125858-14673041-4408-4afb-a0f2-3017de0c4550.png)


* Now that the data is prepared and cleaned we are ready to load it into our Dedicated Pool, but we need to create the tables first.  We have a script activity that will run against our Dedicated Pool to create these artifacts for us.
    
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

![image](https://user-images.githubusercontent.com/59613090/193132332-12689085-0516-45ac-ab6d-ddd91ba3928d.png)


* We are now all setup with data ready to go and a table to load it in and we'll use a Copy Activity to perform the load.
![image](https://user-images.githubusercontent.com/59613090/193132405-61afde90-c500-4097-8060-58bc00f0411e.png)


# STEP 1: Parameter Setup
Prior to running the observations pipeline (FHIR_Pipeline4Observation_Spark_OC) you will need to set the pipeline parameters to use the artifact names you chose during deployment.  Go to the integrate hub, expand the obsevation folder, and select the pipeline to open it.

![image](https://user-images.githubusercontent.com/59613090/193133194-68a05a70-e2c7-43b9-81fa-393b2050b231.png)


Once the pipeline opens you will need to click somewhere on the canvas (open space or background) to see the pipeline level parameters.  This means that NONE of the activities should be highlighted or selected.  Now select the Parameters tab in the bottom pane to view the pipeline level parameters.

![image](https://user-images.githubusercontent.com/59613090/193143735-1d23e579-ba28-442e-94bb-e7f95c8c1be5.png)


Change the default value for each of the following five parameters to what you chose during deployment:
* StorageName - This is the name of your Synapse workspace ADLS account
* DatabaseName - This is the name of your database in Synapse Dedicated SQL Pool
* ServerName - This is the name of your Synapse Dedicated SQL Pool
* SparkPoolName - This is the name of your Synapse Spark Pool
* DatasetSize - This is either "1tb" or "30tb" depending on which size dataset you want to use  

# STEP 2: Execute Pipeline
* You need to hit the debug button to kick off the pipeline run.
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

![image](https://user-images.githubusercontent.com/59613090/193143925-bcab20eb-d2d1-4b40-81fd-b464d2ad90d2.png)

# Objective 2: ADX Pool  
* Objective 2 focuses on the steps to ingest, analyze and visualize the Observations data (time-series data) utilizing the Data Explorer Pool on Azure Synapse Analytics.  
* More specifically, we will be ingesting the data within the "Observation_Main" folder that is in the *curated* container within our Azure Data Lake Storage Gen 2. This "Observation_Main" folder contains the bulk of the Observations data already cleaned and prep and in parquet format.

# STEP 1: Create a Data Explorer Pool

1. In Synapse studio, on the left-side pane, select **Manage > Data Explorer pools**
2. Select **New**, and then enter the following details on the **Basics** tab:  
   | Setting | Value | Description |
   |:------|:------|:------
   | Data Explorer Pool Name | adxpoolmedicaldata | This is the name the Data Explorer pool will have |
   | Workload | Compute Optimized | This workload provides a higher CPU to SSD storage ratio. |
   | Node Size | Small(4 cores) | Set this to the smallest size to reduce costs for this quickstart |  
 3. Select **Review + Create > Create.** Your data explorer will start the provisioning process. Once it is complete move on to the next step.

![Creating ADX pool](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/raw/nataliarodri906-patch-1/images/gif1.gif)  

# STEP 2: Create a Data Explorer Database  

1. In Synapse Studio, on the left-side pane, Select **Data**.  
2. Select + (Add new resource) > **Data Explorer Database** and paste the following information:  
   | Setting | Value | Description |
   |:------|:------|:------
   | Data Explorer Pool Name | adxpoolmedicaldata | The name of the Data Explorer pool to use |
   | Name | ObservationData | This database name must be unique within the cluster. |
   | Default retention period | 365 | The time span (in days) for which it's guaranteed that the data is kept available to query. The time span is measured from the time that data is ingested. |   
   |Default cache period | 31 | The time span (in days) for which to keep frequently queried data available in SSD storage or RAM, rather than in longer-term storage  
3. Select **Create** to create the database. Creation typically takes less than a minute.  

![Creating Data Explorer Pool](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/raw/nataliarodri906-patch-1/images/gif2.gif)  

# STEP 3: Ingesting Data

1. In Synapse studio, on the left-side pane, select **Data**
   
3. Right-click ADX database (ObservationData) and click on **Open in Azure Data Explorer**. This opens the Azure Data Explorer web UI.
   
5. Once in the web UI click on the **Data** tab on the left. This opens the ADX "One-Click UI", where you can quickly ingest data, create database tables, and automatically map the table schema.
   
7. Click on **Ingest data**, and then enter the following details:
   
   | Setting | Value | Description |
   |:------|:------|:------
   | Cluster | adxpoolmedicaldata | Enter name of Data Explorer pool created or use the *Add connection* button to add Connection URI |
   | Database | ObservationData | Enter name of database created |
   | New Table | ObservationsTable | Enter the name for the table that will hold the taxi trip data |

9. Select **Next**, and then enter the following information for **Source**:
   
   | Setting | Value | Description |  
   |:------|:------|:------
   | Source Type | ADLS Gen2 Container | Choose the correct source type  
   | Ingestion Type | One-time + Continuous | Choose the correct ingestion type  
   | Select source | Select Container | This allows you to select the container from your Azure Subscription  
   | Storage subscription | (Enter your subscription name) | Enter your Azure subscription name 
   | Storage account | storagemedicaldata | Enter your storage account name 
   | Container | curated | Enter this container name as this where the data resides  
   | Sample Size | NA | Leave blank |
   | Folder path | fhir/1tb/Observation_main | Find this under Directory Properties for this folder
   | File extension | .parquet | This is the format of the data
            
8.  Select **Next: Schema** and leave all the information as default. This page displays the schema and a partial data preview of the **taxitriptable** that will be created.
9.  Select **Next: Start Ingestion** and this will begin the ingestion process for the data. It is complete once all the files display a green checkmark. Click **Close** to complete.
10.  Validate ingestion by selecting **Query** on the left-side pane, clicking on the taxitripdatabase -> taxitriptable and you will see all of the data within the table. The same will be found in Synapse studio. 

![Ingesting Data](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/raw/nataliarodri906-patch-1/images/gif3.gif)  

# STEP 4: Analyze Data using KQL

# STEP 5: Visualization


  
## Congratulations on completing Exercise 02. You are now ready to move to [Exercise 03 - Patients](/Exercise03-Patients/README.md)
