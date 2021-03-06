SnowPlow is a powerful web analytics platform. It does three things:

1. Identifies users on your website(s), and tracks the way they engage (page views and events)
2. It stores the granular customer-level, event-level data in a scalable "clickstream" datawarehouse (using Amazon S3)
3. It enables data scientists and business analysts to query that data using powerful, scalable big data tools including Apache Hive, Pig and Hadoop (via Amazon EMR)

## What problem does SnowPlow solve?

SnowPlow was developed out of frustration with traditional web analytics tools. Traditional tools have a number of limitations:

1. Too high level - it's not possible to drill down into granular customer-level data
2. Too many "vanity metrics", few of which are useful for making business decisions
3. Provide a very limited analytic toolset - hard to integrate with powerful tools e.g. R and Mahout
4. Siloed - hard to integrate with other customer data systems e.g. CRM and social
5. Too narrowly focused on marketing-related metrics (sources of traffic, clicks, conversions)
6. Too narrowly focused on conventional web publishers (page views by page) and retailers (conversion funnels)

## Overcoming those limitations: new possibilities

SnowPlow enables users to do much more with their web analytics data:

1. Detailed customer analytics, including customer lifetime value, cohort analysis, customer segmentation
2. Detailed product analytics, including diagnosing where websites are and are not working effectively, identifying how customers actually use the product and measuring the impact of product developments on user engagement
3. Integrate web analytics data with powerful analytics tools including R and Mahout to perform cluster analysis, principle component analysis etc.
4. Drill into individual customer data to support customer services, deliver personalised service (e.g. email marketing / product recommendations)
5. Develop highly specific analyses to help answer specific business questions, definitively
6. Integrate with other customer data sources including CRM and social so that data on how customers engage online can be incorporated in the _single customer view_.

For more details se the [[Features and benefits]].
To see how SnowPlow looks technically, view the [[technical architecture|Technical architecture]].

## Resources

* [Keplar LLP](http://www.keplarllp.com) provides consultancy services around SnowPlow, including setup and business consultancy to use SnowPlow data to drive customer understanding and growth in customer value and volume. Some SnowPlow documentation is availabe on the [website](http://www.keplarllp.com/resources/snowplow) and in a large number of [blog posts](http://www.keplarllp.com/blog/category/snowplow) on SnowPlow.
* This wiki is being built out to house all SnowPlow documentation, including documentation for [business folks](Product-overview) outlining the value of using SnowPlow and [data scientsts / analysts](Analysts-cookbook) including documentation of queries for performing specific analyses with SnowPlow.