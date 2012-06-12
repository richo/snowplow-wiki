## Introduction

SnowPlow is a powerful web analytics platform. It does three things:

1. Identifies users on your website(s), and tracks the way they engage. (Page views and events)
2. It stores the granular, customer-level, event-level data in a scalable "clickstream" datawarehouse. (Using Amazon S3.)
3. It enables data scientists and business analysts to query that data using powerful, scalable big data tools including Apache Hive, Pig and Hadoop (via Amazon EMR).

## What makes SnowPlow different to other web analytics tools?

Traditional web analytics programmes have been built with specific analyses in mind, and too often, these analyses are focused narrowly on the needs of web marketers for online retailers and publishers.

SnowPlow has been built from the ground up to provide analysts with direct access to very granular data and a flexible toolset to perform a much broader set of analyses, including:

1. *Customer analytics:* e.g. what is the lifetime value of each of our customers? How does that vary with customer engagement? How should we segment our audience based on the way they engage with our online products / services?
2. *Product analytics:* e.g. what has been the impact of the most recent set of product developments on driving engagement? Which parts of our product are working well? Which are not?

In addition, by giving data scientists access to this underlying data, SnowPlow opens the way for data scientists to develop models of customer behaviour and develop machine learning alogorithms that predict customer value and form the basis of personalisation services.

Because data can be accessed at an atomic level, it is straightforward to link web analytics data with other data sources including 

* CRM and marketing data 
* In-store data
* Social data incl. Twitter and Facebook

Lastly, SnowPlow is open source. All _your_ web analytics data is stored on your own Amazon S3 account, so you have complete control and ownership of it.

For more detailed information on the benefits of running SnowPlow for your business, see the [[Product overview]].

## Resources

* [Keplar LLP](http://www.keplarllp.com) provides consultancy services around SnowPlow, including setup and business consultancy to use SnowPlow data to drive customer understanding and growth in customer value and volume. Some SnowPlow documentation is availabe on the [website](http://www.keplarllp.com/resources/snowplow) and in a large number of [blog posts](http://www.keplarllp.com/blog/category/snowplow) on SnowPlow.
* This wiki is being built out to house all SnowPlow documentation, including documentation for [business folks](SnowPlow-for-business-folks) outlining the value of using SnowPlow and [data scientsts / analysts](SnowPlow-for-analysts) including documentation of queries for performing specific analyses with SnowPlow.