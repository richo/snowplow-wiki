# SnowPlow setup guide

Setting up a new SnowPlow installation involves the following steps:

1. Deploying SnowPlow to CloudFront
2. Implementing SnowPlow JavaScript tracking
3. Setting up Amazon EMR and the Hive client
4. Optimizing the storage of SnowPlow events data for analysis in Hive

To take each of these steps in turn:

## 1. Deploying SnowPlow to CloudFront

Like most web analytics solutions, SnowPlow works through JavaScript tracking tags; typically this JavaScript is served from Amazon CloudFront. The SnowPlow JavaScript tags then transmit SnowPlow event data to an event "collector", which also runs on Amazon CloudFront.

Deploying SnowPlow to CloudFront is a two-step process:

1. [[Hosting-SnowPlow-js|Hosting SnowPlow.js]]
2. [[Setting up the CloudFront collector]]

Note: in some cases, [SnowPlow Analytics](http://snowplowanalytics.com) hosts SnowPlow on behalf of their clients, so these two steps are not always necessary.

## 2. Installing SnowPlow JavaScript tracking

Now you are ready to add SnowPlow's JavaScript tracking to your website. The guide for this follows below, along with an optional guide to setting up tracking of your marketing campaigns:

1. [[Integrating SnowPlow tracking tags on your website]]
2. [[Marketing campaign tracking]]

## 3. Setting up Amazon Elastic Mapreduce and the Hive client

Setting up the JavaScript tracking (above) ensures that you are collecting web analytics data and storing it to the SnowPlow data warehouse on Amazon S3.

To query the data, however, you need to setup Amazon's Elastic Mapreduce and install the Command Line tools on analysts computers, so that they can query the data using Apache Hive and Pig, for example. 

1. [Setting up Amazon Elastic Mapreduce](Setting-up-EMR)
2. [Querying the SnowPlow data using Hive](Querying-the-data-using-Hive) 

### 4. Optimizing the storage of SnowPlow events data for analysis in Hive

Although the raw SnowPlow data can be queried directly in the format it is collected in, it is generally more efficient if a lot of analytic work will be performed using Hive, to migrate the data into a new format optimised for analysis in Hive. Instructions on performing the migration are described below:

1. [[Migrating data into a format optimized for querying with Hive|Migrating-data-to-optimized-format-for-Hive|Migrating-data-to-optimized-format-for-Hive]]
