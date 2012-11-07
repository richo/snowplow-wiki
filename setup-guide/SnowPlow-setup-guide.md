Setting up SnowPlow involve/s setting up one of each of the five modules that make up SnowPlow's architecture:

![architecture] [conceptual-architecture]

## 1. Trackers
[Tracker setup: choosing a tracker](choosing-a-tracker)  
[Javascript tracker setup](javascript-tracker-setup)  
[iOS tracker setup](ios-tracker-setup)  

## 2. Collectors
[Collectors setup: choosing a collector](choosing-a-collector)  
[Cloudfront collector setup](setting-up-the-cloudfront-collector)  
[Clojure collector setup](setting-up-the-clojure-collector)  
[SnowCannon (node.js) setup](snowcannon-setup-guide)  


## 3. ETL
[ETL setup: choosing an ETL module](choosing-an-etl-module)  
[Hive ETL](hive-etl-setup)  
[Scalding / Cascading ETL](scalding-etl-setup)  

## 4. Storage
[Storage setup: choosing a storage option](choosing-a-storage-module)  
[S3 / Hive setup](s3-hive-storage-setup)  
[Infobright setup](infobright-storage-setup)  

## 5. Analytics
[Analytics setup](analytics-setup)
[Hive based analytics](hive-analytics-setup)
[Infobright based analytics](infobright-analytics-setup)

## Common elements  
[[Setting up EMR]]  
[[Hosted assets]]  

## Approach to choosing which SnowPlow modules you want to use

SnowPlow's loosely coupled architecture means there are a lot of different options when implementing SnowPlow.

When making your choice about which module to use, we recommend:

1. Starting with your choice of [collector](choosing-a-collector). This is the module that will receive data from your tracker(s) (it's possible to run many trackers for each collector e.g. to track behaviour across multiple platforms) and log it to files. With this and your trackers setup, you have complete flexibility to switch in and out ETL, storage and analytics modules, to direct the data collected into different databases and analytics engines to support different types of uses later.
2. Your choice of [tracker](choosing-a-tracker) will generally be determined by the platform(s) you want to track behaviour on. If you want to track behaviour on your website(s) for example, the Javascript tracker will most likely be the right choice for you.
3. The next most important decision will be whether you want to store your SnowPlow events data in S3 for processing in Hive / Hadoop, or in Infobright where it can be queried using straight SQL. There are pros and cons to both approaches - for more details on the relevant considerations, refer to the [[choosing a storage option]] page.
4. Your choice of [ETL module](choosing-an-etl-module) should be straightforward based on your choice of [collector](choosing-a-collector) and [storage](choosing-a-storage-module) module.
5. Your can add as many [analytics](analytics-setup) modules in as you like as you go / we develop them / other people develop them.


In general, the most common SnowPlow setup involves:

1. The [Javascript tracker](javascript-tracker-setup)
2. The [Cloudfront collector](setting-up-the-cloudfront-collector)
3. The [Hive ETL](hive-etl-setup)
4. [S3 / Hive storage](s3-hive-storage-setup)
5. Analytics using [Apache Hive](hive-analytics-setup)

[conceptual-architecture]: about-snowplow/images/conceptual-architecture.png