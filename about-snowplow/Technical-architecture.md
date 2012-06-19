SnowPlow has a very different architecture from conventional open-source web analytics packages such as [Piwik] [piwik] or [Open Web Analytics] [owa]. Where those packages are built on a tightly-coupled LAMP stack, SnowPlow has a polyglot, loosely-coupled architecture, involving:

* A JavaScript event tracker
* A CDN ([Amazon CloudFront] [cloudfront])
* An immutable flatfile-based data store ([Amazon S3] [s3])
* JVM-based "big data" storage and querying tools ([Apache Hadoop] [hadoop] and [Hive] [hive]).

In the rest of this page we explain our rationale for this architecture, map out the technical components and finally flag up the benefits and limitations of this architecture.

# Rationale for architecture

SnowPlow's distinctive architecture has been informed by a set of key design principles:

1. SnowPlow should be able to scale to tracking billions of customer events without crippling either a) the in-client tracking or b) the subsequent analysis of those events
2. Events should be stored in a simple, immutable data store - there should be no costly "updates in place" and it should be easy to join the SnowPlow event data to other data sources (e.g. demographic, CRM, e-commerce transactional or geographic)
3. SnowPlow should make it easy for business analysts, data scientists and engineers to query and analyse its event data in as wide a range of analytical tools (e.g. Hive, R, Cascalog, Sky EQL) as possible

## Architectural diagram

The current technical architecture for SnowPlow looks like this:

![SnowPlow Technical Architecture] [tech-architecture]

[tech-architecture]: /snowplow/snowplow/wiki/about-snowplow/images/snowplow-tech-architecture.jpg

# Technical benefits

This section to come.

# Technical limitations

This section to come.

[piwik]: http://piwik.org/
[owa]: http://www.openwebanalytics.com/
[cloudfront]: http://aws.amazon.com/cloudfront/
[s3]: http://aws.amazon.com/s3/
[hadoop]: http://hadoop.apache.org/
[hive]: http://hive.apache.org/