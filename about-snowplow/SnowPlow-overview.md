## Introduction

SnowPlow is a powerful web analytics platform. It does three things:

1. Identifies users on your website(s), and tracks the way they engage (page views and events)
2. It stores the granular customer-level, event-level data in a scalable "clickstream" datawarehouse (using Amazon S3)
3. It enables data scientists and business analysts to query that data using powerful, scalable big data tools including Apache Hive, Pig and Hadoop (via Amazon EMR)

## What problem does SnowPlow solve?

SnowPlow was developed out of frustration with traditional web analytics tools. Traditional tools were found to have a number of limitations:

1. Too narrowly focussed on marketing related metrics (sources of traffic, clicks, conversions)
2. Too narrowly focussed on content sites (page views by page) and retail sites (conversion funnels)
3. Provide a very limited analytic toolset. (Hard to integrate with powerful tools e.g. R and Mahout)
4. Too high level. Cannot drill into granular customer data
5. Drown users in a sea of metrics, few of which are useful for making business decisions
6. Siloed: hard to integrate with other customer data systems e.g. CRM and social

## Overcoming those limitations: new possibilities

SnowPlow enables users to do much more with their web analytics data:

1. Detailed customer analytics, including customer lifetime value, cohort analysis, customer segmentation
2. Detailed product analytics, including diagnosing where websites are and are not working effectively, identifying how customers actually use the product and measuring the impact of product developments on user engagement
3. Integrate web analytics data with powerful analytics tools including R and Mahout to perform cluster analysis, principle component analysis etc.
4. Drill into individual customer data to support customer services, deliver personalised service (e.g. email marketing / product recommendations)
5. Develop highly specific analyses to help answer specific business questions, definitively
6. Integrate with other customer data sources including CRM and social so that data on how customers engage online can be incorporated in the _single customer view_.

For more details se the [[Features and benefits]].
To see how SnowPlow looks technicall, view the [technical architecture|technical architecture].

## Resources

* [Keplar LLP](http://www.keplarllp.com) provides consultancy services around SnowPlow, including setup and business consultancy to use SnowPlow data to drive customer understanding and growth in customer value and volume. Some SnowPlow documentation is availabe on the [website](http://www.keplarllp.com/resources/snowplow) and in a large number of [blog posts](http://www.keplarllp.com/blog/category/snowplow) on SnowPlow.
* This wiki is being built out to house all SnowPlow documentation, including documentation for [business folks](SnowPlow-for-business-folks) outlining the value of using SnowPlow and [data scientsts / analysts](SnowPlow-for-analysts) including documentation of queries for performing specific analyses with SnowPlow.

