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