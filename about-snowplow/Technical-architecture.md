SnowPlow has a very different architecture from conventional open-source web analytics packages such as [Piwik] [piwik] or [Open Web Analytics] [owa]. Where those packages are built on a tightly-coupled LAMP stack, SnowPlow has a polyglot, loosely-coupled architecture, involving:

* A JavaScript event tracker
* A CDN ([Amazon CloudFront] [cloudfront])
* An immutable flatfile-based data store ([Amazon S3] [s3])
* JVM-based "big data" storage and querying tools ([Apache Hadoop] [hadoop] and [Hive] [hive]).

In the rest of this page we explain our rationale for this architecture, map out the technical components and finally flag up the strengths and limitations of this architecture.

# Rationale for architecture

SnowPlow's distinctive architecture has been informed by a set of key design principles:

1. **Extreme scalability** - SnowPlow should be able to scale to tracking billions of customer events without affecting the performance of your client (e.g. website) or making it difficult to subsequently analyse those events
2. **Track everything** - conventional analytics packages encourage you to only track the "important" events - but how can you know what's important until you come to the analysis stage, maybe months later? With SnowPlow, we recommend you track as many events as you can, and figure out what they mean later
3. **Separation of concerns** - event tracking and event analysis in SnowPlow should be two separate systems, only loosely-coupled. In particular, business logic in your analysis system should not bleed into the client-side tracking setup
4. **Permanent event history** - SnowPlow events should be stored in a simple, non-relational, immutable data store: there should be no costly "updates in place", and no subsequent analysis should corrupt the raw event data
5. **Direct access to individual events** - you should have direct access to your raw SnowPlow event data at the atomic level, not intermediated by a third-party vendor, or a slow HTTP API (or worse, an API which offers aggregates only). In particular, it should be easy to join the SnowPlow event data to other data sources (e.g. demographic, CRM, e-commerce transactional or geographic)
6. **Make any analysis possible** - don't trap analysts in a restrictive reporting UI. SnowPlow should make it easy for business analysts, data scientists and engineers to answer any business question they want, in as wide a range of analytical tools (e.g. Hive, R, Pig, Sky EQL) as possible

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