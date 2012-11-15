[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](SnowPlow technical documentation) > [**Collectors**](collectors)

## Overview

Different SnowPlow collectors write SnowPlow data to logs of different formats.

Here we document the different formats, and show which collectors generate what. This document should be used by anyone:

1. Building a new collector, who would like to ensure it logs to a SnowPlow supported format.
2. Building an ETL module, to ensure that the ETL module can successfully read the raw SnowPlow logs generated by each collector, and write them to the [data structures](canonical-data-structure) used by the storage modules.

## Logging formats

| **Logging formats**                   | **Description**                                 | **Status** | **Collector** |
|:--------------------------------------|:------------------------------------------------|:-----------|:--------------|
| [Cloudfront logs](#cloudfront)        | Amazon's Cloudfront log formats with Amazon Cloudfront filename naming convention | Supported (both pre and post Sept 2012 formats) | [[Cloudfront collector]] |
| [Tomcat access logs](#tomcat)         | Tomcat access logs with Amazon Elastic Beanstalk filename naming convention | Not currently supported | [Clojure collector](clojure) |
| [Fluentd logs](#fluentd)              | Fluentd logs with the fluent-s3 plugin filename conventions | Not currently supported | [[SnowCannon]] |

<a name="cloudfront" />
## The Cloudfront logging format (with Cloudfront naming convention)

TO WRITE

<a name="tomcat" />
## The Tomcat access log format (with Amazon Elastic Beanstalk filename naming convention)

TO WRITE

<a name="fluentd" />
## The Fluentd log format (with s3 plugin filename convention)

TO WRITE