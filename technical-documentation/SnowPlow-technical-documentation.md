# SnowPlow Technical Documentation

The technical documentation reflects the SnowPlow architecture, with five loosely-coupled sub-systems connected by four standardised data protocols/formats:

![architecture] [technical-architecture]

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


[technical-architecture]: technical-documentation/images/technical-architecture.png