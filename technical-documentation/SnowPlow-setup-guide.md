There are several steps to take in order to setup SnowPlow:

1. [Installing the Javascript tracker](#installing-javascript-tracker)
2. [Setting up Amazon EMR and the Hive client](#emr)
3. [Optimizing the storage of SnowPlow events data for analysis in Hive](#optimize)

<a name="installing-javascript-tracker" />
### 1. Installing the Javascript tracking.

Like most web analytics solutions, SnowPlow works through Javascript tracking tags, that need to be embedded on web pages to collect data. Setting up Javascript tracking is a three step process:

1. [Integrating SnowPlow tracking tags on your website](Integrating-SnowPlow-tracking-tags-on-your-website)
2. [Hosting the tracking pixel](Hosting-the-tracking-pixel)
3. [Hosting SnowPlow.js](Hosting-SnowPlow-js)

Note: in some cases, [Keplar LLP](http://www.keplarllp.com) hosts the tracking pixel and `SnowPlow.js` on behalf of their clients, so that these two steps are not always necessary.

<a name="emr" />
### 2. Setting up Amazon Elastic Mapreduce and the Hive client

Setting up the Javascript tracking (above) ensures that you are collecting web analytics data and storing it to the SnowPlow data warehouse on Amazon S3.

To query the data, however, you need to setup Amazon's Elastic Mapreduce and install the Command Line tools on analysts computers, so that they can query the data using Apache Hive and Pig, for example. 

4. [Setting up Amazon Elastic Mapreduce](Setting-up-EMR)
5. [Querying the SnowPlow data using Hive](Querying-the-data-using-Hive) 

<a name="optimise" />
### 3. Optimizing the storage of SnowPlow events data for analysis in Hive

Although the raw SnowPlow data can be queried directly in the format it is collected in, it is generally more efficient if a lot of analytic work will be performed using Hive, to migrate the data into a new format optimised for analysis in Hive. Instructions on performing the migration are described below:

7. Migrating data into a format optimized for querying with Hive
