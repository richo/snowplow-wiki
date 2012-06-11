## Introduction

SnowPlow is a powerful web analytics platform. It does three things:

1. Identifies users on your website(s), and tracks the way they engage. (Page views and events)
2. It stores the granular, customer-level, event-level data in a scalable "clickstream" datawarehouse. (Using Amazon S3.)
3. It enables data scientists and business analysts to query that data using powerful, scalable big data tools including Apache Hive, Pig and Hadoop (via Amazon EMR).

## Contents

1. [What makes SnowPlow different to other web analytics tools?](#what-makes-snowplow-different)
2. [Resources](#resources)
3. [Roadmap](#roadmap)
4. [Contributors](#contributors)
5. [Open source license](#license)


<a name="what-makes-snowplow-different" />
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

<a name="resources" />
## Resources

* [Keplar LLP](http://www.keplarllp.com) provides consultancy services around SnowPlow, including setup and business consultancy to use SnowPlow data to drive customer understanding and growth in customer value and volume. Some SnowPlow documentation is availabe on the [website](http://www.keplarllp.com/resources/snowplow) and in a large number of [blog posts](http://www.keplarllp.com/blog/category/snowplow) on SnowPlow.
* This wiki is being built out to house all SnowPlow documentation, including documentation for [business folks](SnowPlow-for-business-folks) and [data scientsts / analysts](SnowPlow-for-analysts).

<a name="roadmap" />
## Roadmap

* Build out the list of recipe analyses using Apache Hive
* Build a set of connectors to manage ongoing data migration (ETL) from S3 into analytics databases for improved train-of-thought and repeatable analyses
* Make `snowplow.js` available over SSL (currently not working)

<a name="contributors" />
## Contributors

* [Alex Dean](https://github.com/alexanderdean)
* [Yali Sassoon](https://github.com/yalisassoon)
* [Anthon Pang](https://github.com/robocoder)

Original concept for SnowPlow inspired by [Radek Maciaszek](https://github.com/rathko).

## Copyright and license

SnowPlow is copyright 2012 Orderly Ltd. Significant portions of `snowplow.js`
are copyright 2010 Anthon Pang.

Licensed under the [Apache License, Version 2.0] [license] (the "License");
you may not use this software except in compliance with the License.

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.