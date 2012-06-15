SnowPlow was built to meet the following 2 requirements:

1. Make it easy for website owners to capture granular, event-level and customer-level data capturing how their audience / customers engage on their websites and digital platforms and store that data securely
2. Make it possible to run a wide range of analytics across that entire data set including highly bespoke queries: thereby enabling website owners to answer very specific business questions using their data

Utilising [Apache Hive](http://hive.apache.org/) has been critical in our delivering on our 2nd objective: giving website owners a powerful, flexible tool to query data that is straightforward to use.

### Hive is powerful

Because it is built on top of Hadoop, Hive queries can be run against vast data sets. 

### Hive is flexible

Hive is a very flexible tool to use to query data.

1. It includes a large (and growing) number of [inbuilt functions](https://cwiki.apache.org/Hive/languagemanual-udf.html)
2. Where Hive functions do not already exist to perform required operations, it is possible to create [new user-defined functions](https://cwiki.apache.org/Hive/genericudafcasestudy.html)

### Hive is straightforward, especially as implemented for SnowPlow

Hive makes it easy for anyone with a knowledge of SQL to run queries against SnowPlow web analytics data. No knowledge of Java (or any other languages e.g. Python) is required.

SnowPlow data is stored in Hive in a single events table where each line of the table represents a single "event" or "user action" e.g. opening a web page, adding an item to a shopping basket, filling in an entry in a web form,  playing a video.

SnowPlow's SQL-like querying language combined with SnowPlow's simple data structure means that writing queries is very easy. 

To get started using Hive, we recommend trying the [[basic recipes|Basic recipes part 1]] to begin with.

[serdes]: https://github.com/snowplow/snowplow-log-deserializers
[hive]: http://hive.apache.org/

[serdereadme]: https://github.com/snowplow/snowplow-log-deserializers/blob/master/README.md
[recipereadme]: https://github.com/snowplow/snowplow/blob/master/hive/recipes/README.md
[hiveinteractive]: https://github.com/snowplow/snowplow/blob/master/docs/06_running_hive_interactive_session.md
[hivetables]: https://github.com/snowplow/snowplow/blob/master/docs/07_snowplow_hive_tables_introduction.md

[cohort1]: http://www.keplarllp.com/blog/2012/04/cohort-analyses-for-digital-businesses-an-overview
[cohort2]: http://www.keplarllp.com/blog/2012/05/performing-cohort-analysis-on-web-analytics-data-using-snowplow
[cohort3]: http://www.keplarllp.com/blog/2012/05/performing-the-cohort-analysis-described-in-eric-riess-lean-startup-using-snowplow-and-hive
[cohort4]: http://www.keplarllp.com/blog/2012/05/on-the-wide-variety-of-different-cohort-analyses-possible-with-snowplow
[cohort5]: http://www.keplarllp.com/blog/2012/05/different-approaches-to-measuring-user-engagement-with-snowplow

[contact]: snowplow@keplarllp.com
[license]: http://www.apache.org/licenses/LICENSE-2.0