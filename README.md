# ingest-daily-flight-data-in-redshift-fact-table
## Introduction
* Data Ingestion:Fully automated airlines daily_flights data ingestion from S3 to a Redshift datawarehouse,where it can be accessed and analyzed
* Data Transformation:flights data processing and transformations with Glue ETL efficiently
* Data Loading into Data Warehouse:Amazon Redshift to analyze exabytes of data and run complex analytical queries
## Architecture
![daily-flights](https://github.com/kalyani33/ingest-daily-flight-data-in-redshift-fact-table/assets/37569003/11a1cc13-5559-4f32-abd1-a4889603cf42)

## Tech Stack
* S3
* EventBridge
* Step Functions
* Glue Crawler
* Glue Visual ETL
* Redshift
* SNS
## Components
### S3
  S3 will be recieving daily-flights data in a hive-style partition ,whenever a new .csv file uploaded then an event bridge rule gets triggered.
  And also load the airports.csv file into redshift dim table
### Event Bridge
Below is the event bridge rule for listening to s3 flights csv file uplode and triggers the step function
```
{
  "source": ["aws.s3"],
  "detail-type": ["Object Created"],
  "detail": {
    "bucket": {
      "name": ["airline-data-landing-zn"]
    },
    "object": {
      "key": [{
        "suffix": ".csv"
      }]
    }
  }
}
```
### Step Function
Step function workflow for ochestrating the data load to redshift and notify the users about the success/failed status.  

  ![image](https://github.com/kalyani33/ingest-daily-flight-data-in-redshift-fact-table/assets/37569003/767d1264-d21e-4967-9055-0b0fe67ae1b4)
### Visual ETL steps:
  * Crawl and get the data from data catalogue,transform it where departure-delay > 60 sec
  * Join the daily-flights data with airports dim data to get the flights fact data
  ![image](https://github.com/kalyani33/ingest-daily-flight-data-in-redshift-fact-table/assets/37569003/05fb7c0c-a346-4e7e-ad6a-3f740486288e)
### Redshift
Below are the redshift cmds i used for loading data into redshift fact & dim tables.

```
create schema airlines;

CREATE TABLE airlines.airports_dim (
    airport_id BIGINT,
    city VARCHAR(100),
    state VARCHAR(100),
    name VARCHAR(200)
);

COPY airlines.airports_dim
FROM 's3://airlines-dataset-gds/airports.csv' 
IAM_ROLE 'arn:aws:iam::348532040329:role/service-role/AmazonRedshift-CommandsAccessRole-20230820T081203'
DELIMITER ','
IGNOREHEADER 1
REGION 'us-east-1';

--------------

CREATE TABLE airlines.daily_flights_fact (
    carrier VARCHAR(10),
    dep_airport VARCHAR(200),
    arr_airport VARCHAR(200),
    dep_city VARCHAR(100),
    arr_city VARCHAR(100),
    dep_state VARCHAR(100),
    arr_state VARCHAR(100),
    dep_delay BIGINT,
    arr_delay BIGINT
);
```
## Issue Faced
* I faced issue while crawling Redshift data for airports dim table data
  even though I created a JDBC connection  for connecting to redshift dim table to get the airports data.But could not get it as I was facing S3 endpoint issue.
  then I created a S3 endpoint for my VPC to access to Redshift.
  this post helped me to resolve my issue,which has detailed steps [crawling redshift data in AWS](https://medium.com/@shrutighoradkar101/crawling-redshift-data-in-aws-ec30a2e83057)
* Apart from this, open redhsift port in security group inbound rules to allow redshift port

