### Creating resources using terraform 

Ran ```gcloud auth application-default login ``` and entered authorization code

Credentials saved to file: [/home/codespace/.config/gcloud/application_default_credentials.json]

Set GOOGLE_APPLICATION_CREDENTIALS var:
```export GOOGLE_APPLICATION_CREDENTIALS=/home/codespace/.config/gcloud/application_default_credentials.json```

Check var is set:

```echo "$GOOGLE_APPLICATION_CREDENTIALS" ```

Then ran ```terraform init```

And ``` terraform apply``` to create resources

Creating external table in BigQuery 

```
CREATE EXTERNAL TABLE dtc-de-course-411420.demo_dataset_de_kl.green_data_ext 
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://terraform-demo-terra-bucket_de_kl/green_tripdata_2022-*.parquet']
);
```

Checking count of rows 

```
SELECT COUNT(*) 
FROM dtc-de-course-411420.demo_dataset_de_kl.green_data_ext 
```

Result - 840402 rows

Create a table in BQ using the Green Taxi Trip Records for 2022. 
I will use CTAS:

```
CREATE OR REPLACE TABLE dtc-de-course-411420.demo_dataset_de_kl.green_data_tbl 
AS SELECT 
    VendorID,
    lpep_pickup_datetime,
    lpep_dropoff_datetime,
    store_and_fwd_flag,
    RatecodeID,
    PULocationID,
    DOLocationID,
    passenger_count,
    trip_distance,
    fare_amount,
    extra,
    mta_tax,
    tip_amount,
    tolls_amount,
    ehail_fee,
    improvement_surcharge,
    total_amount
FROM dtc-de-course-411420.demo_dataset_de_kl.green_data_ext 
```

### Question 1: What is count of records for the 2022 Green Taxi Data??

```
SELECT COUNT(*) 
FROM dtc-de-course-411420.demo_dataset_de_kl.green_data_tbl 
```
Answer - 840402

### Question 2: Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

2.14 MB for the External Table and 0MB for the Materialized Table

### Question 3: How many records have a fare_amount of 0?
```
SELECT COUNT(DISTINCT PULocationID) 
FROM dtc-de-course-411420.demo_dataset_de_kl.green_data_tbl 
```
Answer = 1622

### Question 4: What is the best strategy to make an optimized table in Big Query if your query will always order the results by PUlocationID and filter based on lpep_pickup_datetime? (Create a new table with this strategy)

Partition by lpep_pickup_datetime Cluster on PUlocationID

```
CREATE TABLE dtc-de-course-411420.demo_dataset_de_kl.green_data_opt
(
VendorID	INTEGER,
lpep_pickup_datetime	TIMESTAMP,
lpep_dropoff_datetime	TIMESTAMP,
store_and_fwd_flag	STRING,
RatecodeID	FLOAT64,
PULocationID	INTEGER,
DOLocationID	INTEGER,
passenger_count	FLOAT64,
trip_distance	FLOAT64,
fare_amount	FLOAT64,
extra	FLOAT64,
mta_tax	FLOAT64,
tip_amount	FLOAT64,
tolls_amount	FLOAT64,
ehail_fee	INTEGER,
improvement_surcharge	FLOAT64,
total_amount	FLOAT64
)
PARTITION BY DATE(lpep_pickup_datetime)
CLUSTER BY
  PULocationID
AS (
   SELECT 
    VendorID,
    lpep_pickup_datetime,
    lpep_dropoff_datetime,
    store_and_fwd_flag,
    RatecodeID,
    PULocationID,
    DOLocationID,
    passenger_count,
    trip_distance,
    fare_amount,
    extra,
    mta_tax,
    tip_amount,
    tolls_amount,
    ehail_fee,
    improvement_surcharge,
    total_amount
  FROM dtc-de-course-411420.demo_dataset_de_kl.green_data_tbl
);
```

### Question 5: Write a query to retrieve the distinct PULocationID between lpep_pickup_datetime 06/01/2022 and 06/30/2022 (inclusive)


```
SELECT DISTINCT PULocationID
FROM  dtc-de-course-411420.demo_dataset_de_kl.green_data_opt
WHERE lpep_pickup_datetime >= '2022-06-01' and lpep_pickup_datetime <= '2022-06-30'



SELECT DISTINCT PULocationID
FROM  dtc-de-course-411420.demo_dataset_de_kl.green_data_tbl
WHERE lpep_pickup_datetime >= '2022-06-01' and lpep_pickup_datetime <= '2022-06-30'

```

Answer - 12.82 MB for non-partitioned table and 1.12 MB for the partitioned table

### Question 6 - Where is the data stored in the External Table you created?

Answer - GCP Bucket

### Question 7: It is best practice in Big Query to always cluster your data:
Answer - false