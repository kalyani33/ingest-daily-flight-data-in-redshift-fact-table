# ingest-daily-flight-data-in-redshift-fact-table
## Introduction
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
* Step Function
  ![image](https://github.com/kalyani33/ingest-daily-flight-data-in-redshift-fact-table/assets/37569003/767d1264-d21e-4967-9055-0b0fe67ae1b4)
* Visual ETL steps:
  
  ![image](https://github.com/kalyani33/ingest-daily-flight-data-in-redshift-fact-table/assets/37569003/05fb7c0c-a346-4e7e-ad6a-3f740486288e)
## Issue Faced
* Crawling Redshift data for airports dim table data
  I created a JDBC connection  for connecting to redshift dim table to get the airports data.But could not get it as I was facing S3 endpoint issue.
  then I created a S3 endpoint for my VPC to access to Redshift.
  this post helped me to resolve my issue,which has detailed steps [crawling redshift data in AWS](https://medium.com/@shrutighoradkar101/crawling-redshift-data-in-aws-ec30a2e83057)

