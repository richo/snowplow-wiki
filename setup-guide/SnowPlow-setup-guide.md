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


In general, the most common SnowPlow setup involves:

1. The [Javascript tracker](javascript-tracker-setup)
2. The [Cloudfront collector](setting-up-the-cloudfront-collector)
3. The [Hive ETL](hive-etl-setup)
4. [S3 / Hive storage](s3-hive-storage-setup)
5. Analytics using [Apache Hive](hive-analytics-setup)




[conceptual-architecture]: about-snowplow/images/conceptual-architecture.png