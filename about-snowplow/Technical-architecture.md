SnowPlow has a very different architecture from conventional open-source web analytics packages such as [Piwik] [piwik] or [Open Web Analytics] [owa]. Where those packages are built on a tightly-coupled LAMP stack, SnowPlow has a polyglot, loosely-coupled architecture, involving:

* A JavaScript event tracker
* A CDN ([Amazon CloudFront] [cloudfront])
* An immutable flatfile-based data store ([Amazon S3] [s3])
* JVM-based "big data" storage and querying tools ([Apache Hadoop] [hadoop] and [Hive] [hive]).

In the rest of this page we explain our rationale for this architecture, map out the technical components and finally flag up the strengths and limitations of this architecture.

# Rationale for architecture

SnowPlow's distinctive architecture has been informed by a set of key design principles:

1. **Extreme scalability** - SnowPlow should be able to scale to tracking billions of customer events without affecting the performance of your client (e.g. website) or making it difficult to subsequently analyse all of those events
2. **Separation of concerns** - event tracking and event analysis in SnowPlow should be two separate systems, only loosely-coupled
3. **Permanent event history** - SnowPlow events should be stored in a simple, non-relational, immutable data store
4. **Direct access to individual events** - you should have direct access to your raw SnowPlow event data at the atomic level
5. **Support any analysis** - SnowPlow should make it easy for business analysts, data scientists and engineers to answer any business question they want, in as wide a range of analytical tools as possible

## Architectural diagram

The current technical architecture for SnowPlow looks like this:

![SnowPlow Technical Architecture] [tech-architecture]

[tech-architecture]: /snowplow/snowplow/wiki/about-snowplow/images/snowplow-tech-architecture.jpg

# Technical strengths

This section to come.

# Technical limitations

This section to come.

[piwik]: http://piwik.org/
[owa]: http://www.openwebanalytics.com/
[cloudfront]: http://aws.amazon.com/cloudfront/
[s3]: http://aws.amazon.com/s3/
[hadoop]: http://hadoop.apache.org/
[hive]: http://hive.apache.org/