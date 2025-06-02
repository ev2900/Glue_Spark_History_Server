# AWS Glue Spark UI Log via. History Server

<img width="85" alt="map-user" src="https://img.shields.io/badge/views-328-green"> <img width="125" alt="map-user" src="https://img.shields.io/badge/unique visits-084-green">

AWS Glue provides a serverless Spark execution environment. The Spark UI via. a Spark history server can help with performance tuning jobs.

Glue does not provide a Spark history server. However, Glue jobs can produce Spark UI logs in an S3 bucket. You can subsequently host your own Spark history server that visualizes the Spark UI logs directly from the S3 bucket Glue logs in.

The instructions below are for Windows. It uses a docker container to run the Spark history server and directly consumes Spark UI logs from an S3 bucket in real time.

# Spark History Server Install

1. [Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)

2. Enable Spark UI logs for a Glue job if it is not already enabled. Instructions for how to [enable the Spark web UI for glue jobs](https://docs.aws.amazon.com/glue/latest/dg/monitor-spark-ui-jobs.html) are provided in the AWS documentation

3. Download the required docker files that correspond to the version of Glue

* [Glue 1.0 and 2.0](https://github.com/ev2900/Glue_Spark_History_Server/tree/main/Docker/Glue_1.0_and_2.0)
* [Glue 3.0](https://github.com/ev2900/Glue_Spark_History_Server/tree/main/Docker/Glue_3.0)
* [Glue 4.0](https://github.com/ev2900/Glue_Spark_History_Server/tree/main/Docker/Glue_4.0)

4. Build the Docker image by running the following from the command line in the folder you downloaded the docker files in

``` docker build -t glue/sparkui:latest . ```

5. Set the following environment variables from the command line

* Replace the *<S3_BUCKET_PATH_TO_SPARK_UI_LOGS>* with the name of the S3 bucket and path to the folder that contains the Spark UI logs from the Glue job(s)

``` set LOG_DIR="s3a://<S3_BUCKET_PATH_TO_SPARK_UI_LOGS>" ```

* Replace *<AWS_ACCESS_KEY_ID>* and *<AWS_SECRET_ACCESS_KEY>* with the access key id and secret access key for a user that has access to read the Spark UI files from the S3 bucket

``` set AWS_ACCESS_KEY_ID="<AWS_ACCESS_KEY_ID>" ```

``` set AWS_SECRET_ACCESS_KEY="<AWS_SECRET_ACCESS_KEY>" ```

6. Create the docker container running the following from the command line

``` docker run -itd -e SPARK_HISTORY_OPTS="-Dspark.history.fs.logDirectory=%LOG_DIR% -Dspark.hadoop.fs.s3a.access.key=%AWS_ACCESS_KEY_ID% -Dspark.hadoop.fs.s3a.secret.key=%AWS_SECRET_ACCESS_KEY%" -p 18080:18080 --name sparkui glue/spark:latest "/opt/spark/bin/spark-class org.apache.spark.deploy.history.HistoryServer" ```

7. The Spark UI will be avaiable at [http://localhost:18080/](http://localhost:18080/)
