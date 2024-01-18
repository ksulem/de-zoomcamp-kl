## Question 1. Knowing docker tags

```
docker --help
docker build --help
docker run --help
```

Which tag has the following text? - Automatically remove the container when it exits

```
docker run --rm
```

## Question 2. Understanding docker first run

### Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash. 

```
docker run -it --entrypoint /bin/bash python:3.9
pip list
```

What is version of the package wheel ?

| Package    | Version |
| ---------- | ------- |
| pip        | 23.0.1  |
| setuptools | 58.1.0  |
| wheel      | 0.42.0  |

Run with Dockerfile

Navigate `cd homeworks/hw1`

```
docker build -t test:pyth .

docker run -it test:pyth
```

### Prepare Postgres

Downloded data into homeworks/hw1/ny_taxi_init_dataset

Docker command to run postgres 

```
docker run -it \
-e POSTGRES_USER="drwho" \
-e POSTGRES_PASSWORD="drwho" \
-e POSTGRES_DB="ny_taxi" \
-v /workspaces/de-zoomcamp-kl/homeworks/hw1/postgres/ny_taxi_postgres_data:/var/lib/postgresql/data \
-p 5432:5432 \
postgres:13
```

To connect from cli to postresql:

```
pgcli -h localhost -p 5432 -u drwho -d ny_taxi
```

Created Jupyter notebook - main.ipynb

## Question 3. How many taxi trips were totally made on September 18th 2019?

```
SELECT COUNT(*) 
FROM green_tripdata 
WHERE date_trunc('day', lpep_pickup_datetime) = '2019-09-18' 
AND date_trunc('day', lpep_dropoff_datetime) = '2019-09-18'
```

15612

## Question 4. Largest trip for each day
```
SELECT lpep_pickup_datetime, trip_distance, row_number() OVER ( ORDER BY trip_distance DESC) AS rn
FROM green_tripdata 
ORDER BY rn
LIMIT 1
```

2019-09-26


## Question 5. Which were the 3 pick up Boroughs that had a sum of total_amount superior to 50000?
```
SELECT tz."Borough"
FROM green_tripdata AS gt
JOIN taxi_zone AS tz
      ON gt."PULocationID" = tz."LocationID"
WHERE tz."Borough" <> 'Unknown'
      AND date_trunc('day', gt.lpep_pickup_datetime) = '2019-09-18'
GROUP BY  tz."Borough"
HAVING SUM(gt.total_amount) > 50000
```

"Brooklyn" "Manhattan" "Queens"

## Question 6 - For the passengers picked up in September 2019 in the zone name Astoria which was the drop off zone that had the largest tip? We want the name of the zone, not the id.
```
SELECT doz."Zone"
FROM green_tripdata AS gt
JOIN taxi_zone AS tz
      ON gt."PULocationID" = tz."LocationID"
JOIN taxi_zone AS doz
      ON gt."DOLocationID" = doz."LocationID"
WHERE tz."Zone" = 'Astoria'
      AND date_trunc('day', gt.lpep_pickup_datetime) >= '2019-09-01'
      AND date_trunc('day', gt.lpep_pickup_datetime) < '2019-10-01'
ORDER BY tip_amount DESC
LIMIT 1
```

JFK Airport


### Terraform

Installed Google Cloud code in workspace
Installed Google CLI 

Ran ```gcloud auth application-default login ``` and entered authorization code

Credentials saved to file: [/home/codespace/.config/gcloud/application_default_credentials.json]

Set GOOGLE_APPLICATION_CREDENTIALS var:
```export GOOGLE_APPLICATION_CREDENTIALS=/home/codespace/.config/gcloud/application_default_credentials.json```

Check var is set:

```echo "$GOOGLE_APPLICATION_CREDENTIALS" ```

Then ran ```terraform init```

Then ```terraform plan``` to check what will be depployed 

And ``` terraform apply``` to create resources

## Question 7 - Creating Resources

Output:

```
Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_bigquery_dataset.demo_dataset_de_kl will be created
  + resource "google_bigquery_dataset" "demo_dataset_de_kl" {
      + creation_time              = (known after apply)
      + dataset_id                 = "demo_dataset_de_kl"
      + default_collation          = (known after apply)
      + delete_contents_on_destroy = false
      + effective_labels           = (known after apply)
      + etag                       = (known after apply)
      + id                         = (known after apply)
      + is_case_insensitive        = (known after apply)
      + last_modified_time         = (known after apply)
      + location                   = "US"
      + max_time_travel_hours      = (known after apply)
      + project                    = "dtc-de-course-411420"
      + self_link                  = (known after apply)
      + storage_billing_model      = (known after apply)
      + terraform_labels           = (known after apply)
    }

  # google_storage_bucket.demo-bucket-dez-kl will be created
  + resource "google_storage_bucket" "demo-bucket-dez-kl" {
      + effective_labels            = (known after apply)
      + force_destroy               = true
      + id                          = (known after apply)
      + location                    = "US"
      + name                        = "terraform-demo-terra-bucket_de_kl"
      + project                     = (known after apply)
      + public_access_prevention    = (known after apply)
      + self_link                   = (known after apply)
      + storage_class               = "STANDARD"
      + terraform_labels            = (known after apply)
      + uniform_bucket_level_access = (known after apply)
      + url                         = (known after apply)

      + lifecycle_rule {
          + action {
              + type = "AbortIncompleteMultipartUpload"
            }
          + condition {
              + age                   = 1
              + matches_prefix        = []
              + matches_storage_class = []
              + matches_suffix        = []
              + with_state            = (known after apply)
            }
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

google_bigquery_dataset.demo_dataset_de_kl: Creating...
google_storage_bucket.demo-bucket-dez-kl: Creating...
google_bigquery_dataset.demo_dataset_de_kl: Creation complete after 1s [id=projects/dtc-de-course-411420/datasets/demo_dataset_de_kl]
google_storage_bucket.demo-bucket-dez-kl: Creation complete after 1s [id=terraform-demo-terra-bucket_de_kl]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

When resources are not needed anymore then run ```terraform destroy```