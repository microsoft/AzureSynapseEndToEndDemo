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
   | Data Explorer Pool Name | **adxpoolmedicaldata** | This is the name the Data Explorer pool will have |
   | Workload | **Compute Optimized** | This workload provides a higher CPU to SSD storage ratio. |
   | Node Size | **Small(4 cores)** | Set this to the smallest size to reduce costs for this quickstart |  
 3. Select **Review + Create > Create.** Your data explorer will start the provisioning process. Once it is complete move on to the next step.

![Creating ADX pool](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/raw/nataliarodri906-patch-1/images/gif1.gif)  

# STEP 2: Create a Data Explorer Database  

1. In Synapse Studio, on the left-side pane, Select **Data**.  
2. Select + (Add new resource) > **Data Explorer Database** and paste the following information:  
   | Setting | Value | Description |
   |:------|:------|:------
   | Data Explorer Pool Name | **adxpoolmedicaldata** | The name of the Data Explorer pool to use |
   | Name | **ObservationData** | This database name must be unique within the cluster. |
   | Default retention period | **365** | The time span (in days) for which it's guaranteed that the data is kept available to query. The time span is measured from the time that data is ingested. |   
   |Default cache period | **31** | The time span (in days) for which to keep frequently queried data available in SSD storage or RAM, rather than in longer-term storage  
3. Select **Create** to create the database. Creation typically takes less than a minute.  

![Creating Data Explorer Pool](https://github.com/Azure/Test-Drive-Azure-Synapse-with-a-1-click-POC/raw/nataliarodri906-patch-1/images/gif2.gif)  

# STEP 3: Ingesting Data

1. In Synapse studio, on the left-side pane, select **Data**
   
2. Right-click ADX database *ObservationData* and click on **Open in Azure Data Explorer**. This opens the Azure Data Explorer web UI.
   
3. Once in the web UI click on the **Data** tab on the left. This opens the ADX "One-Click UI", where you can quickly ingest data, create database tables, and automatically map the table schema.
   
4. Click on **Ingest data**, and then enter the following details:
   
   | Setting | Value | Description |
   |:------|:------|:------
   | Cluster | **adxpoolmedicaldata** | Enter name of Data Explorer pool created or use the *Add connection* button to add Connection URI |
   | Database | **ObservationData** | Enter name of database created |
   | New Table | **ObservationCurated** | Enter the name for the table that will hold the taxi trip data |

5. Select **Next**, and then enter the following information for **Source**:
   
   | Setting | Value | Description |  
   |:------|:------|:------
   | Source Type | **ADLS Gen2 Container** | Choose your source type
   | Ingestion Type | **One-time + Continuous** | Choose the type of ingestion you would like to perform (view i for more info.)
   | Select source | **Select Container** | This allows you to select the container from your Azure Subscription  
   | Storage subscription | *NA* | Enter your Azure subscription name 
   | Storage account | **storagemedicaldata** | Enter your storage account name 
   | Container | **curated** | Enter the *curated* container name as this where the data resides  
   | Sample Size | *NA* | Leave blank 
   | Folder path | **fhir/1tb/Observation_main** | Find this path under Directory Properties for the *Observation_main* folder
   | File extension | **.parquet** | This is the format of the data
            
6.  Select **Next: Schema**, this page displays the schema and a partial data preview of the **ObservationCurated** that will be created.
    * On the left hand menu you will see *Compression Type*, *Data format*, *Nested Levels* and *Mapping name* leave these configurations as displayed for this demo.
    * Change the data type for **Observation_id**, **encounter_id_reference** and **patient_id_reference** columns from string to **guid** data type. Do this by right-clicking on each of these columns and clicking on the **Change Data Type** button.  
    * Similarly, change the data type for **issued** and **effectiveDateTime** columns from string to **datetime**.
    * Click on the carot on top right-hand corner to open the *Command viewer*. Here you can view the KQL code that is running in the background, such as the Create Table Command. This command is creating the table with all of the data types where the data will be stored: *ObservationCurated*.
    
8.  Select **Next: Start Ingestion** and this will begin the ingestion process for the data. It is complete once all the files display a green checkmark. This should take approximately 10 minutes. Click **Close** to complete.
   * Note: You can also ingest the data using the pipeline named: *ObservationsData_ToSDXPool** which uses a COPY activity to bring the data into ADX. However, you must manually create a table in your ADX database prior to copying the data. Under KQL scripts you can find the *Observation Table Creation* script to create the table.  After the table has been successfully created with the correct data types for the columns you can run the piepeline with your respective parameters.

![Ingesting Data](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/IngestingGif.gif)  

# STEP 4: Analyze & Visualize Data using KQL
1. In Synpase studio, on the left-side pane, select **Develop**. 
2. Under 'Notebooks' dropdown on the left side of the screen, click on the KQL notebook named **'Observations Analytics w KQL'**.  
3. Once in the notebook, ensure you are connected to your ADX pool **'adxpoolmedicaldata'** and database **'ObservationData'** and then run each of the sections (a-i) of the script separately and observe the results:
   
   *a.* Get a quick preview of the **'ObservationCurated'** table.  
   
      ![Take](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif01.gif)
   
   *b.* Counts the number of Observations in the **'ObservationCurated'** table.  
   
      ![count](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif02.gif)  

   *c.* Summarizes the minimum and maximum of the *'issued'* column(date issued) in the **'ObservationCurated'** table.   
   
      ![summarize1](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif03.gif)  
   
   *d.* Summarizes the count of records in the **'ObservationCurated'** table by grouping them into daily intervals based on the *'issued'* column.  
   
      ![summarize2](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif04.gif)    
      
   *e.* Visualizes the timeseries chart for **'ObservationCurated'** table based on issued date. More specifically, it filters the table to select records with issued datetime between July 15th, 1910 and June 20th, 2021, (which are the min and max issued dates found in step c), then counts the number of observations for every 30 day intervals within that time range, and finally visualizes the results as a time chart.
   
      ![timechart1](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif05.gif) 
      
   *f.* Now we are trimming the dataset to analyze daily observations during a relatively normal time period (8 years between 2011 and 2019). Ultimately, we visualize the timechart again and it shows the pattern of observations day by day for 8 years.  
   
      ![timechart2](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif06.gif)  
   
   *g.* We are now identifying anomalies between these 8 years (2011 and 2019) using the timeseries chart developed in the previous step. More specifically, it uses the *"series_decompose_anomalies"* function to identify anomalies in the observations count data with a threshold of 1.5. Then, it visualizes the anomalies as an anomaly chart titled "Anomalies for daily medical observations during 8 years". Anomalies can be seen as red dots on the chart.  
   
     ![anomalieschart](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif07.gif)  
     
   *h.* Now are listing the anomalies in a table. More specifically, 
 it uses the *"series_decompose_anomalies"* function to identify anomalies in the observations count data and extends the table with an *'anomalies'* column. The *"mv-expand"* function is used to expand the table to separate rows for each observations count and its corresponding anomaly value and issued datetime. The code then filters the table to only include rows where the anomaly value is not equal to zero. 
   
      ![anomalieslist](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif08.gif)  
      
   *i.* Finally, we are using this query to separate anomaly properties. More specifically, it uses the *"series_decompose_anomalies"* function to decompose the observations data into anomalies, score, and baseline values. The table is expanded to separate rows for each issued datetime, observations count, score, baseline and anomaly (where the anomalies column is set to null if the anomaly value is 0). This query can later be used to visualize the timeseries chart with anomalies on powerBI (reference FHSI_Dual_Dims v2).  
   
      ![decomposeanomalies](https://github.com/nataliarodri906/AzureSynapseEndToEndDemo/blob/7dd6b66ab99ce0b5aed9feb2b0aa43811dccb5f5/Images/KQLgif09.gif)  

# Summarization

- Overall, we were able to see how Synapse Studio is an incredibly powerful tool that brings immense value to analytical workloads in Azure.  It offers a comprehensive suite of analytical components, including Pipelines, Spark, SQL, Data Explorer, and Power BI, all within a single Azure Resource. This integrated approach enhances efficiency and delivers exceptional benefits to users. 
- More specifically, as we saw here Azure Data Explorer (ADX) is a valuable component offered by Synapse Studio. ADX proved to be a fast and highly scalable analytics service optimized for querying and analyzing large volumes of diverse data in real-time. In this case, we saw that it is particularly well-suited for working with time series data due to its efficient storage and querying capabilities. Additionally, its integration with Synapse Studio allows users to perform ad-hoc data exploration and gain instant insights from massive datasets, facilitating rapid decision-making.
- By providing these purpose-built analytical components within a single Azure Resource, Synapse Studio eliminates the complexity and overhead associated with managing multiple tools and integrations. It offers a cohesive environment for end-to-end data analytics, catering to diverse workload needs efficiently and effectively.



  
## Congratulations on completing Exercise 02. You are now ready to move to [Exercise 03 - Patients](/Exercise03-Patients/README.md)
