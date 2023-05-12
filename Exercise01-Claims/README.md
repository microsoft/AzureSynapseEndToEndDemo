# Objective
* This pipeline takes the JSON data that is in FHIR standard format from our "raw" ADLS container and converts it to parquet.  Since Parquet is a columnar compressed file format this makes it much faster to import and work with the data.  We store the parquet output in our "processed" container in ADLS under a folder called claim.
![image](https://user-images.githubusercontent.com/59613090/193112535-e9c68b13-95e0-4463-a572-4cdc1b8d694d.png)


* We plan to eventually load this data into Dedicated SQL Pool across 3 tables representing Diagnosis, Insurance, and Procedures.  We need to extract the data needed for each table, clean it, and write it back to ADLS.  The second activity in our pipeline handles all of this in a single Synapse Spark Notebook.
![image](https://user-images.githubusercontent.com/59613090/193112711-8c6733f5-87e7-4639-a2e3-58afb6f2b414.png)


* Now that the data is prepared and cleaned we are ready to load it into our Dedicated Pool, but we need to create the schema and tables first.  We have a script activity that will run against our Dedicated Pool to create these artifacts for us.
    
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

![image](https://user-images.githubusercontent.com/59613090/193114025-309980e7-7c0a-415a-912b-fa8832c109ea.png)


* We are now all setup with data ready to go and tables to load it in and we'll use a Copy Activity for each table and load them in parallel.
![image](https://user-images.githubusercontent.com/59613090/193114655-add929b4-a317-49b5-8a48-014a5e15ddaa.png)


* There is one last thing in our pipeline.  We have some data engineers that will need to explore the data in the lake to understand how they can enable new work streams for the business.  They are currently skilling up in PySpark, but until then we need to give them the ability to explore the data through TSQL.  We have created a Notebook activity that creates the meta data for SQL Tables on top of the data in our data lake.  You'll be able to play around with some exploration scripts in a later activity.
![image](https://user-images.githubusercontent.com/59613090/193118363-04c00d81-a374-4113-9a97-e3d3f3994807.png)



# STEP 1: Parameter Setup
Prior to running the claims pipeline (FHIR_Pipeline4Claim_Spark_OC) you will need to set the pipeline parameters to use the artifact names you chose during deployment.  Go to the integrate hub, expand the claims folder, and select the pipeline to open it.

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


## Congratulations on completing Exercise 01. You are now ready to move to [Exercise 02 - Observations](/Exercise02-Observations/README.md)
