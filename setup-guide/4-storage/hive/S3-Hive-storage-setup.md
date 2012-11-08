[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Storage**](choosing-a-storage-module) > [**S3 / Hive storage setup**](s3-hive-storage-setup)

## Overview

Storing your SnowPlow data in S3 for querying in Hadoop / Hive has a number of advantages:

1. You can run complicated queries across your entire data set using the growing array of Hadoop-powered tools and libraries through Elastic Mapreduce. This includes Apache Hive, Apache Mahout and Apache HBase.
2. Your storage and analytics setup scales horizontally, so you should not have any scalability difficulties as your data volumes grow.

However, there are some disadvantages:

1. Although tools like Hive make writing Mapreduce jobs in Hadoop easier, they will never be _as_ easy as querying data in a SQL database, especially with all the analytics tools and UIs that plug directly into databases to enable that analysis 
2. Query times can be slow relative to queries run in specialised databases, especially where the data volumes are smaller

## Setting up S3 / Hive storage

Currently there is no setup required to store your data on S3 / Hive - as this is handled automatically for you by the [EMR ETL Runner] (deploying-emretlrunner) used to perform the [etl] (hive-etl-setup). As a result, if you are in the process of setting up SnowPlow, and you have already set up your [collector](choosing-a-collector) and [tracker](choosing a tracker), we recommend proceeding to the [ETL setup](choosing an etl module) stage of the setup process.