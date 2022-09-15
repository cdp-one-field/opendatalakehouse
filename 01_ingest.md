# 01_ingest

In this lab, we will build an end-to-end ingetion workflow using Cloudera Data flow. The primary goal of this workflow is to build a end\-to\-end automated ETL solution using data flow(nifi) to connect to the source external DB, export the table data, keep a copy of the data in AWS s3 bucket, use the csv data to create a dynamic schema, enrich the data and create hive tables for further analysis, visualization and data science. To achieve that goal, this project demonstrates creation of end\-to\-end ETL workflow for ingetion and enrichment of realtime data to allow users to perform analysis, visualize and create machine learning models.

## Part 1:

Configuring the Nifi flow to execute ingestion workflow pre-requisites.

![cdpone_nifi.png](image/01_ingest/cdpone_nifi.png)

Step 1: Click on Build and Monitor Data flows with Nifi from the CDP One Console for creating the ingestion workflow

![cdpone_nifi_registry.png](image/01_ingest/cdpone_nifi_registry.png)

Step 2: From Nifi GUI, click on Add Process Group

![processor_group.png](image/01_ingest/processor_group.png)

Step 3: Then, upload the flow file definition from Nifi Schema Registry
    Note: Select the Airline-Ingestion-Workflow-Pre-requisite flow with the latest version

![import_dataflow.png](image/01_ingest/import_dataflow.png)

Step 4: Below workflow downloads the JDBC Driver to the Nifi Instances
    Note: But, before starting the flow, we need to modify and enable some parameters and controller services

![Download_connector_for_Database_source.png](image/01_ingest/Download_connector_for_Database_source.png)

		1. Enable Default NiFi SSL Context Service
		2. Update the driver_location and url_to_download_jdbc variables for downloading the right database driver 
			1. In this scenario flow is using /tmp/jdbc directory and considering postgres is the external database
			2. By using this URL (https://jdbc.postgresql.org/download/postgresql-42.2.26.jre6.jar) to download the postgres jdbc driver

Step 5: Start the workflow and validate the driver in nifi instance by logging to the cdpone Edge node

![start_flow.png](image/01_ingest/start_flow.png)

## Part 2:

Configuring the Nifi flow to execute Airline-Ingestion-Workflow-Stage1.

![cdpone_nifi.png](image/01_ingest/cdpone_nifi.png)

Step 1: From Nifi GUI, click on Add Process Group

![processor_group.png](image/01_ingest/processor_group.png)

Step 2: Then, upload the flow file definition from Nifi Schema Registry
    Note: Select the Airline-Ingestion-Workflow-Stage1 flow with the latest version

![select_flow_version.png](image/01_ingest/select_flow_version.png)    

Step 3: Below workflow connects the source external database and exports the tables in csv format to AWS S3 bucket
    Note: But, before starting the flow, we need to modify and enable some parameters and controller services

![Load_from_source_DB_to_CDPOne_Landing_zone.png](image/01_ingest/Load_from_source_DB_to_CDPOne_Landing_zone.png)

		1. Enable all the controller services shown in the below diagram
![controller_versions.png](image/01_ingest/controller_versions.png)        
		2. Validate and Modify the below parameters
			1. update the existing source db_username, db_password, db_hostname, db_port

![select_stage1_variables.png](image/01_ingest/select_stage1_variables.png)           

			2. By using this URL (https://jdbc.postgresql.org/download/postgresql-42.2.26.jre6.jar) to download the postgres jdbc driver
            3. update the AWS S3 bucket with right name - (landing-directory-path)
            4. provide Cloudera cdpone tenant username and password ((cdp-username & cdp-password))

Step 4: Before running the flow, validate the Database connection controller service
    Note: This service will validate the DB connection by using the details from paramater context and DB User should have privileges to read and export the data

![controller_versions.png](image/01_ingest/controller_versions.png)    

Step 5: Start the workflow and validate the AWS S3 bucket for the database table data (s3a://cdponedemo-cdp-private-default-3hxxiqv/landing/airlines)

![aws_s3_files_csv.png](image/01_ingest/aws_s3_files_csv.png)


## Part 3:

Configuring the Nifi flow to execute Airline-Ingestion-Workflow-Stage2.

![cdpone_nifi.png](image/01_ingest/cdpone_nifi.png)

Step 1: From Nifi GUI, click on Add Process Group

![processor_group.png](image/01_ingest/processor_group.png)

Step 2: Then, upload the flow file definition from Nifi Schema Registry
    Note: Select the Airline-Ingestion-Workflow-Stage2 flow with the latest version

![import_dataflow.png](image/01_ingest/import_dataflow.png)    

Step 3: Below workflow connects with AWS s3 to fetch the table data(CSV), create the dynamic schema, convert table data to avro format, convert avro data to orc files by removing the null data, along with schema and create hive database and tables in hive

![Data_Engineering_ETL/ELT.png](image/01_ingest/Data_Engineering_ETL/ELT.png)

    Note: But, before starting the flow, we need to modify and enable some parameters and controller services
		1. Enable all the controller services shown in the below diagram

![stage2_controller_services.png](image/01_ingest/stage2_controller_services.png)               

		2. Validate and Modify the below parameters

![stage2_variables.png](image/01_ingest/stage2_variables.png)        

			1. Update the existing hive connection uri (hive_connection_uri)
			2. provide Cloudera cdpone tenant username and password (cdp-username & cdp-password)
            3. update the AWS S3 bucket with right bucket name(s3-bucket-private)

Step 4: Before running the flow, validate the Hive Database connection controller service
	Note: This service will validate the DB connection by using the hive connection uri from paramater context
Step 5: Start the workflow and validate Hive airlines database

![aws_orc_files.png](image/01_ingest/aws_orc_files.png)

![hive_datasets.png](image/01_ingest/hive_datasets.png)

![hive_data.png](image/01_ingest/hive_data.png)
