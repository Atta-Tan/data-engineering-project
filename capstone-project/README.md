# Capstone Project Construction

## UK Detached housing price paid

## problem
For anyone who want to purchase properties in UK or Real Estate Company which is looking for investing in properties in UK, The housing price paid information is one of the most important thing they should know before spending their money for it, especially the detached house which is has a very high price compared to another property type. Thus, in this project, I am interested to study about information of detached houseing price in UK and create data platform for building dashboard to present actual information about housing price paid or to find insight of them. 


## Source

Raw data that I chose for this project is the information of all registered property sales in United Kingdom from Kaggle website. It is available to download [here](https://www.kaggle.com/datasets/hm-land-registry/uk-housing-prices-paid).

![kaggle](pictures/data_from_kaggle.jpg)


## Data Modeling
The designed data modeling is non-relational data base that comprises four tables, stagging table, "count by type" table, "average price of detached house by district" table and "price of detached house" table. The last three tables are builded by tranforming selected data from stagging table and are collected in columnar type database with partisioning. By building data model like this, new created tables are more comfortable to be used for visualization than using all data in stagging table which may include unwanted data. Users can access and get only useful data that we need to provide for them and they can query data faster. The details of data modeling are shown in picture below.

![Data Modeling](pictures/data_modeling.jpg)


## Data Ingestion
The preovided data pipeline starts from collecting the data from Kaggle website then uploading raw data to AWS S3 using etl code. After preparing raw data in AWS S3 bucket, the next steps are creating tables, loading raw data from AWS S3 bucket to AWS Redshift and transfroming stagging data to new tables in AWS Redshift with using Airflow for creating scheduling tasks, for this project is set for weekly schedule. And the last step is connecting AWS Redshift to Tableau Desktop and building Dashboard for data visualization. The data pipeline's workflow is slown in picture below

![Workflow](pictures/workflow.jpg)


## Tools & Technologies
Computer language: Python, SQL, Spark

Tools: AWS S3, AWS Redshift, Apache Airflow, Tableau Desktop, Gitpod platform


## Getting Started

### create and activate virtual environment and install tools as provided in requirements file.

```sh
python -m venv ENV
source ENV/bin/activate
pip install -r requirements.txt
```

### make dags directory in Airflow

```sh
mkdir -p ./dags ./logs ./plugins
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

### Run docker-compose 

```sh
docker-compose up
```

### After running prepared docker-compose file, we can access to PySpark and Airflow by following port 8080 and port 8888 respectively.

## Create Data lake, AWS S3 Bucket

First of all, we need to create the AWS S3 bucket as our "data lake" to collect raw data. AWS S3 is great one of cloud storage that can collect data in the large scale as much as we need and charges the cost based on our usage. However, AWS S3 cost is still very cheap compared to another platform.

In this step, S3 bucket must be edited "block public access" and bucket policy to allow all access from public can connect to the bucket (note: permission for all public access is not recommended for practical use).

To get AWS credential keys for connecting to S3 bucket, we can use code below on the AWS interface as shown in picture to get "aws_access_key", "aws_secret_access_key" and "aws_session_token".

```sh
cat ~/.aws/credentials
```
![AWS credentials](pictures/AWS_S3_Credentials_edited.jpg)

And we can find AWS S3 URI in the S3 properties interface as shown in picture below.

![AWS S3 URI](pictures/AWS_S3_URI_edited.jpg)


## Loading raw data to Datalake, AWS S3 bucket
To connect to AWS S3 bucket, AWS credential keys and S3 URI as mentioned above are required in this step.

Change connection configurations in etl code following by AWS credential keys and S3 URI.

![ETL Code Configuration](pictures/load_s3_configuration.jpg)

![ETL Code Configuration](pictures/load_s3_bucket_configuration.jpg)

### run provided etl code to load raw data to AWS s3 Bucket.

```sh
python etl_load_data_to_s3.py
```

Now, our data was uploaded to S3 bucket already.

![AWS S3](pictures/AWS_S3.jpg)


## Create Data Warehouse, AWS Redshift
AWS Redshift will be used as data warehouse for tranfroming raw data from data lake, AWS S3, to another tables in AWS Redshift as designed in data modeling. Because of AWS Redshift is based on PostgreSQL that is friendly for new users. Furthermore, it is a columnar database which is suitable for data transformation and analytics.

Same as the connection of AWS S3, We can get specific endpoint of Redshift cluster, password, database name, username and port number AWS Redshift and set all of them in connection configurations to access AWS Redshift.

![AWS Redshift Endpoint](pictures/AWS_Redshift_edited.jpg)


## ETL PySpark-Notebook with S3
Before transforming data to AWS Redshift, we can run prepared python code that can connect to AWS S3 on PySpark-Notebook to explore, clean and transform our raw data, and also write cleaded data to AWS S3. This step help us to do our tasks more autonomous and clean raw data easier by just running python code.

Connect to PySpark-Notebook by following port 8888 and run all etl script in PySpark-Notebook platform.

![PySpark-Notebook](pictures/PySpark_Notebook.JPG)

## Creating and Scheduling Data Pipeline with Airflow

To improve data pipeline performance, Apache Airflow is one good choice platform for creating scheduling data pipeline. This platform is a medern one and has friendly user interface. Users can use this platform to monitor their pipeline easily. Moreover, we can use Python which is a friendly computer language to build workflow on Apache Airflow. That is why it becomes a popular platform for now.

We can start with connecting to Airflow by following port 8080

Prepare our etl code and adjust connection configurations to access to data lake (AWS S3) and data warehouse (AWS Redshift) and keet it in dags folder.

![S3 Connection Configuaration](pictures/etl_s3_configuration.jpg)

![Redshift Connection Configuaration](pictures/etl_redshift_configuration.jpg)

For this project,scheduling data pipeline by using Airflow consists of creating tables in AWS Redshift, loading data from AWS S3 to stagging data in AWS Redshift and then transforming data in stagging table to designed tables that will be used for Visualization process in the next step.

Using etl code makes our pipeline can run all tasks autonomously following by dags operation sequences as show in picture below.

![Airflow Graph](pictures/Airflow_Graph.jpg)

Activate and triggle dag on Airflow interface and now we can see that graph of operator process was created autonomously and all statuses of each process are turned to "success" (green color).

![Airflow Scheduling](pictures/Airflow_Trigger.jpg)

And we can see all tables that was created by scheduling data pipeline on Redshift interface as shown in picture below.

![Scheduling Data Tranformation](pictures/Transfrom_Data.jpg)


## Data Visualizaion with Tableau
To create data visualization, We chose Tableau desktop software because it is one of powerful software for data visualization and can connect to various database platform, including AWS Redshift.

we can connect tableau with AWS Redshift directly by usging AWS Redshift endpoint, username, password, port and database name.

![Tableau Redshift Connection Configuration](pictures/Tableau_Redshift_Connection_Configuration.jpg)

Tableau already connect to all tables in AWS Redshift. Now we can use these tables to build our Visualization.

![Tableau Redshift Connection](pictures/Tableau_Redshift_Connection.jpg)

The Dashboard for this project is shown in picture below.

![Dashboard](pictures/Dashboard.jpg)