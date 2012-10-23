To simplify setting up and running SnowPlow, the SnowPlow Analytics team provide public hosting for some of the open-source components. These hosted assets are publically available through Amazon Web Services (CloudFront and S3), and using them is free for SnowPlow community members.

**While SnowPlow Analytics Ltd will make every reasonable effort to maintain this hosted assets service, we will not be liable for any failure to provide this service. All of these hosted assets are freely available via [our GitHub repository] [snowplow-repo] and you are encouraged to host them yourselves.** 

The hosted assets are as follows:

## 1. Trackers

### 1.1 JavaScript tracker resources

The minified JavaScript tracker is hosted on CloudFront:

    xxx

## 3. ETL

### 3.1 Hive ETL resources

The Hive ETL process uses a HiveQL file and a Hive deserializer. These are both made available in a public Amazon S3 bucket, for SnowPlowers who are running their Hive ETL process on Amazon EMR:

    xxx

## See also

As well as these hosted assets for running SnowPlow, the SnowPlow Analytics team also make code components and libraries available through Ruby and Java artifact repositories. Please see the [[Artifact repositories]] wiki page for more information.