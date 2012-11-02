# SnowPlow Technical Documentation

The technical documentation is split into 5 sections, reflecting the five components of SnowPlow's loosely coupled architecture:

![architecture] [conceptual-architecture]

## 1. Trackers
[Trackers overview](trackers)  
[Javascript tracker (SnowPlow.js)](javascript-tracker)  
[iOS tracker](ios-tracker)  
[SnowPlow tracker protocol](tracker-protocol)  

## 2. Collectors
[Collectors overview](collectors)  
[Cloudfront collector](cloudfront)  
[SnowCannon (node.js)](snowcannon)  
[Clojure collector (Elastic Beanstalk)](clojure)  

## 3. ETL
[ETL overview](etl)  
[Hive ETL](hive)  
[Scalding / Cascading ETL](scalding)  

## 4. Storage
[Canonical SnowPlow data structure](canonical-data-structure) (platform independent)  
[S3 / hive](s3-apache-hive-storage)  
[Infobright](infobright-storage)  

## 5. Analytics
[Analytics overview](analytics documentation)



[conceptual-architecture]: about-snowplow/images/conceptual-architecture.png