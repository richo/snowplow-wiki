[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Analytics**](analytics-setup)

[[/images/5-analytics.png]] 

## Analytics modules

| **Tracker**                                            | **Description**                                                          |
|:-------------------------------------------------------|:-------------------------------------------------------------------------|
| [Hive / Hadoop analytics on EMR](hive-analytics-setup) | Setup command line tools to start writing Hive / Hadoop jobs and running them on SnowPlow data stored in S3 using EMR |
| [Analytics in Infobright](infobright-analytics-setup)  | Perform analytics on data stored in Infobright, either by directly executing SQL statements in Infobright, or by plugging in one of the many tools that work with data stored in MySQL. (On which Infobright is based) 

## SnowPlow approach to analytics

Enabling analysts to perform the widest set of analysis on their web and application data is the principle reason we created SnowPlow in the first place.

SnowPlow is architected to enable analysts to use a wide range of tools and technologies to analyse their data, regardless of where [that data is stored](choosing a storage module).

Whether your data is stored in S3 or in Infobright, the structure and granularity of the data is exactly the same. Although there are differences in setup required to query the data in S3 using Hive vs in Infobright, the types of queries that you can run, and the range of tools that you can connect to is equally large - the steps required to enable those connections are different depending on your storage solution.

Exploring and documenting all those possibilities is beyond the scope of this setup guide. Instead, over here we've outlined how to get started with analysis in both the storage options outlined above. For further ideas, we recommend looking at the SnowPlow [Analytics Cookbook](http://snowplowanalytics.com/analytics/index.html), on the [SnowPlow website](http://snowplowanalytics.com/). 
