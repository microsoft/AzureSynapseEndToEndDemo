# Objective
* This pipeline takes the JSON data that is in FHIR standard format from our "raw" ADLS container and converts it to parquet.  Since Parquet is a columnar compressed file format this makes it much faster to import and work with the data.  We store the parquet output in our "processed" container in ADLS under a folder called "Patient".
![image](https://user-images.githubusercontent.com/59613090/193136231-4a965468-3d2e-4f24-80a3-c76d0bd8a387.png)


* We plan to eventually load this data into Dedicated SQL Pool across 2 tables representing Patient Addresses and Patient Indentifiers.  We need to extract the data needed for each table, clean it, and write it back to ADLS.  The second activity in our pipeline handles all of this inside a Data Flow Activity.  This could have been done in a Spark notebook like the previous 2 activities, but this will let you compare the two methods.
![image](https://user-images.githubusercontent.com/59613090/193136520-d0bcbab6-fc6f-4896-8ab4-19798833c384.png)


* Now that the data is prepared and cleaned we are ready to load it into our Dedicated Pool, but we need to create the tables first.  We have a script activity that will run against our Dedicated Pool to create these artifacts for us.
    
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

![image](https://user-images.githubusercontent.com/59613090/193136622-d84e952a-60f2-47f9-8895-717bf9e81c6b.png)


* We are now all setup with data ready to go and a table to load it in and we'll use a Copy Activity to perform the load.
![image](https://user-images.githubusercontent.com/59613090/193144477-0a98c0d6-1f98-45b6-8e9a-42745960b837.png)


# STEP 1: Parameter Setup
Prior to running the Patient pipeline (FHIR_Pipeline4Patient_DataFlow_OC) you will need to set the pipeline parameters to use the artifact names you chose during deployment.  Go to the integrate hub, expand the patient folder, and select the pipeline to open it.

![image](https://user-images.githubusercontent.com/59613090/193138455-cbb13596-0a2c-4353-808d-92958a7772f6.png)


Once the pipeline opens you will need to click somewhere on the canvas (open space or background) to see the pipeline level parameters.  This means that NONE of the activities should be highlighted or selected.  Now select the Parameters tab in the bottom pane to view the pipeline level parameters.

![image](https://user-images.githubusercontent.com/59613090/193144874-eb863277-d90b-4f32-a208-6d8d8f2aff96.png)


Change the default value for each of the following five parameters to what you chose during deployment:
* StorageName - This is the name of your Synapse workspace ADLS account
* DatabaseName - This is the name of your database in Synapse Dedicated SQL Pool
* ServerName - This is the name of your Synapse Dedicated SQL Pool
* SparkPoolName - This is the name of your Synapse Spark Pool
* DatasetSize - This is either "1tb" or "30tb" depending on which size dataset you want to use

# STEP 2: Execute Pipeline
* Since this pipeline has a data flow we'll kick it off a bit differently than the previous exercises.  You will want to flip the radio button for "Data Flow Debug", hit the drop down arrow next to debug, and select the last option "Use Activity Runtime".
    >*Note: Make sure your Dedicated Pool is running prior to executing this pipeline.  You can see this in the SQL Pools tab under the Manage Hub.*

![image](https://user-images.githubusercontent.com/59613090/193149525-ada67b9c-90b6-466e-bde7-5546f33fac56.png)


## Congratulations on completing Exercise 03.
