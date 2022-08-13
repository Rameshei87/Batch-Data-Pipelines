# Batch-Data-Pipelines


Design
Setup
Prerequisite
Local run
Deploy to AWS
Stop
Contributing
Design

We will be using Airflow to orchestrate

Classifying movie reviews with Apache Spark.
Loading the classified movie reviews into the data warehouse.
Extract user purchase data from an OLTP database and load it into the data warehouse.
Joining the classified movie review data and user purchase data to get user behavior metric data.


Data pipeline design

Setup
Prerequisite
Docker with at least 4GB of RAM and Docker Compose v1.27.0 or later
psql
AWS account
AWS CLI installed and configured
Clone and cd into the project directory.

git clone https://github.com/josephmachado/beginner_de_project.git
cd beginner_de_project
Local run
When running locally, you can use the make command to manage infrastructure. We use the following docker containers

![image](https://user-images.githubusercontent.com/110036451/184506899-c59f4298-bfc1-410b-9be3-48618ec8c7c4.png)


Airflow
Postgres DB (as Airflow metadata DB)
Metabase for data visualization
You can start the local containers as shown below.

make up # start all containers
make ci # runs format checks, type checks, static checks, and tests
make down # stops the containers

Since we cannot replicate AWS components locally, we have not set them up here.

We have a dag validity test defined here.

Deploy to AWS
To set up the AWS infrastructure we have a script called setup_infra.sh.

Note: We run all of our infrastructure on AWS us-east-1. If you want to change this, please change the corresponding variables in infra_variables.txt.

Setup can be run as shown below.

make down # since our AWS infra will be port forwarded to 8080 and 3000 which are used by local Airflow and Metabase respectively
./setup_infra.sh {your-bucket-name} # e.g ./setup_infra.sh my-test-bucket 
In the prompt enter yes to authenticate the ssh connection.

This sets up the following components

1 AWS EC2, running Airflow, Metabase
1 AWS EMR cluster
1 AWS Redshift cluster
1 AWS S3 bucket
The command will also open Airflow running on an EC2 instance. You can also checkout

Airflow www.localhost:8080 (username and password are both airflow)
Metabase www.localhost:3000
The first time you log in, create a user name and password. To establish a connection to your Redshift cluster, you will need the redshift host, which you can get using the command

aws redshift describe-clusters --cluster-identifier sde-batch-de-project --query 'Clusters[0].Endpoint.Address' --output text
The port, username, and password are in infra_vairables.txt and the database is dev.

You can create dashboards in Metabase, as seen below.

![image](https://user-images.githubusercontent.com/110036451/184506920-8d19fd4d-d036-4074-bc4d-802c15bc55b7.png)


Data pipeline design

Stop
When you are done, do not forget to turn off your AWS instances. In your terminal run

./tear_down_infra.sh {your-bucket-name} # e.g. ./tear_down_infra.sh my-test-bucket
This will stop all the AWS services. Please double-check this by going to the AWS UI S3, EC2, EMR, & Redshift consoles.
