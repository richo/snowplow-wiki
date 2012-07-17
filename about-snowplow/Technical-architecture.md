SnowPlow has a very different architecture from conventional open-source web analytics packages such as [Piwik] [piwik] or [Open Web Analytics] [owa]. Where those packages are built on a tightly-coupled LAMP stack, SnowPlow has a polyglot, loosely-coupled architecture, involving:

* A JavaScript event tracker
* A CDN ([Amazon CloudFront] [cloudfront])
* An immutable flatfile-based data store ([Amazon S3] [s3])
* JVM-based "big data" storage and querying tools ([Apache Hadoop] [hadoop] and [Hive] [hive]).

In the rest of this page we explain our rationale for this architecture, map out the technical components and finally flag up the strengths and limitations of this architecture.

# Rationale for architecture

SnowPlow's distinctive architecture has been informed by a set of key design principles:

1. **Extreme scalability** - SnowPlow should be able to scale to tracking billions of customer events without affecting the performance of your client (e.g. website) or making it difficult to subsequently analyse all of those events
2. **Permanent event history** - SnowPlow events should be stored in a simple, non-relational, immutable data store
3. **Direct access to individual events** - you should have direct access to your raw SnowPlow event data at the atomic level
4. **Separation of concerns** - event tracking and event analysis should be two separate systems, only loosely-coupled
5. **Support any analysis** - SnowPlow should make it easy for business analysts, data scientists and engineers to answer any business question they want, using as wide a range of analytical tools as possible

## Architectural diagram

The current technical architecture for SnowPlow looks like this:

![SnowPlow Technical Architecture] [tech-architecture]

This architecture diagram will be updated shortly with the new daily
ETL job, written in Hive.

# Technical strengths

The SnowPlow approach has several technical advantages over more
conventional web analytics approaches. In no particular order, these
advantages are:

* **Scalable, fast tracking** - using CloudFront for event tracking
    reduces complexity and minimizes client slowdown worldwide
* **Never lose your raw data** - your raw event data is never
    compacted, overwritten or otherwise corrupted by SnowPlow
* **Direct access to events** - not intermediated by a third-party
    vendor, or a slow API, or an interface offering aggregates only
* **Analysis tool agnostic** - SnowPlow can be used to feed whatever
    analytics process you want (e.g. Hive, R, Pig, Sky EQL)  
* **Integrable with other data sources** - join SnowPlow data into
    your other data sources (e.g. ecommerce, CRM) at the event level
* **Clean separation of tracking and analysis** - new analyses will not
    require re-tagging of your site or app

For a wider discussion of the strengths of SnowPlow from a business or
product perspective, please see the [[Features and benefits]] page.

# Technical limitations

The current SnowPlow architecture, tightly coupled as it is to Amazon
CloudFront and S3, has some specific limitations to consider:

* **Not real-time** - CloudFront takes 20-60 minutes to collate logs
    from its edge nodes, so real-time analytics are not feasible
* **Cannot track users across multiple domains** - the user tracking
    cookie is a first-party cookie set by SnowPlow.js, so the user
    will be assigned a different ID on each domain
* **Data payload limited by querystring length** - SnowPlow data is
    logged via a GET querystring - which of course could potentially
    hit the de facto [2000 character] [2000char] URL length limit

For more information on these limitations, please see the [[Technical FAQ]].

[tech-architecture]: /snowplow/snowplow/wiki/about-snowplow/images/snowplow-tech-architecture.jpg
[piwik]: http://piwik.org/
[owa]: http://www.openwebanalytics.com/
[cloudfront]: http://aws.amazon.com/cloudfront/
[s3]: http://aws.amazon.com/s3/
[hadoop]: http://hadoop.apache.org/
[hive]: http://hive.apache.org/
[2000char]: http://stackoverflow.com/questions/417142/what-is-the-maximum-length-of-a-url
