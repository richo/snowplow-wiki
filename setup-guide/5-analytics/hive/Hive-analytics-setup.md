[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Analytics**](analytics-setup) > [**Hive analytics setup**](hive-analytics-setup)

## Pre-requisites

This guide assumes you have setup:

1. A [Collector](choosing a collector) to capture events in S3
2. A [Tracker](choosing a tracker) to capture those events, and pass them to the collector
3. The [Hive ETL](hive etl setup) to transform the collector data into a format optimized for querying with Hive / Hadoop

You are now ready to start querying the data using Hive.

## An overview of Amazon Elastic Mapreduce

Elastic Mapreduce (EMR) is a service provided by Amazon that makes it relatively straight forward to fire up analysis clusters of EC2 machines to run Hadoop / Hive jobs. EMR takes care of setting up and running Hadoop, Hive etc. on the cluster so you can concentrate on the nuts and bolts of developing, testing and running your Hadoop / Hive jobs.

More details on EMR can be found on the [Amazon website](http://aws.amazon.com/elasticmapreduce/).

## Querying data in S3 using Apache Hive using Elastic Mapreduce Command Line Tools

The most straight forward way to query data stored in S3 using Apache Hive is to install the [EMR Ruby Client](setting up emr command line tools). We think this is a misleading name for the client: it is a command line tool that though written in Ruby, requires no knowledge of Ruby to run. It enables you fire up machines on EC2 to perform analysis, ssh in and run Hadoop and Hive, all at the command line.

This guide has two parts:

1. [Setting up EMR Ruby Client (command line tools)](setting-up-emr-command-line-tools)
2. [An introduction to using Hive with the command line tools](Running-Hive-using-the-command-line-tools)

