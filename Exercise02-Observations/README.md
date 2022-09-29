# Objective
* This pipeline takes the JSON data that is in FHIR standard format from our "raw" ADLS container and converts it to parquet.  Since Parquet is a columnar compressed file format this makes it much faster to import and work with the data.  We store the parquet output in our "processed" container in ADLS under a folder called "Observation".
Insert Image


* We plan to eventually load this data into Dedicated SQL Pool in a table called [fhir].[ObservationMain].  We need to extract the data needed for the table, clean it, and write it back to ADLS.  The second activity in our pipeline handles all of this in a single Synapse Spark Notebook.
Insert Image


* Now that the data is prepared and cleaned we are ready to load it into our Dedicated Pool, but we need to create the tables first.  We have a script activity that will run against our Dedicated Pool to create these artifacts for us.
    
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

Insert Image


* We are now all setup with data ready to go and a table to load it in and we'll use a Copy Activity to perform the load.
Insert Image


# STEP 1: Parameter Setup
Prior to running the observations pipeline (FHIR_Pipeline4Observation_Spark_OC) you will need to set the pipeline parameters to use the artifact names you chose during deployment.  Go to the integrate hub, expand the obsevation folder, and select the pipeline to open it.

![image](https://user-images.githubusercontent.com/59613090/192874762-8647fe4e-23c5-4430-b0c1-d557812de371.png)


Once the pipeline opens you will need to click somewhere on the canvas (open space or background) to see the pipeline level parameters.  This means that NONE of the activities should be highlighted or selected.  Now select the Parameters tab in the bottom pane to view the pipeline level parameters.

![image](https://user-images.githubusercontent.com/59613090/192875386-3f5eb80a-1920-40b7-aefc-483b4b5853d4.png)

Change the default value for each of the following five parameters to what you chose during deployment:
* StorageName - This is the name of your Synapse workspace ADLS account
* DatabaseName - This is the name of your database in Synapse Dedicated SQL Pool
* ServerName - This is the name of your Synapse Dedicated SQL Pool
* SparkPoolName - This is the name of your Synapse Spark Pool
* DatasetSize - This is either "1tb" or "30tb" depending on which size dataset you want to use

# STEP 2: Execute Pipeline
* You need to hit the debug button to kick off the pipeline run.
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

![image](https://user-images.githubusercontent.com/59613090/192880611-b693730c-4b2d-4145-b5af-931f6a808050.png)


## Congratulations on completing Exercise 01. You are now ready to move to [Exercise 02 - Observations](https://github.com/ryanjadams/AzureSynapseEndToEndDemo/blob/main/Exercise02-Observations/README.md)
