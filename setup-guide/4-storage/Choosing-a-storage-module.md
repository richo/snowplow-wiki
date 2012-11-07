[[/images/4-storage.png]] 

## Available storage modules

| **Storage database**                           | **Description**                                     | **Status**       |
|:-----------------------------------------------|:----------------------------------------------------|:-----------------|
| [S3 / Apache Hive](s3-hive-storage-setup)      | Store your SnowPlow events data as flat files in Amazon S3. Use Apache Hive (developed at Facebook) to query that data directly. Scales horizontally. (To petabytes and beyond!) | Production-ready |
| [Infobright](infobright-storage-setup)         | Open source columnar database. Crunch through terabytes of data fast. | Production-ready |

## Databases on the SnowPlow roadmap

We plan to grow the number of storage modules available, to include:

1. [PostgreSQL](http://www.postgresql.org/) (for companies without enormous volumes / web and application traffic)
2. [SkyDB](http://skydb.io/) event database

## Selection considerations 

| **S3 / Apache Hive**                           | **Infobright**                                 | 
|:-----------------------------------------------|:-----------------------------------------------|
| Scales horizontally to petabytes and beyond    | Scales (vertically) to terabytes               |
| Costs scale with data volumes and query volumes| Costs scale with data volumes, but not query volumes |
| Easy to query data using SQL-like syntax via Apache Hive | Easy to query using SQL directly     |
| Integrates with some 3rd party tools e.g. Toad for Cloud databases | Integrates with every analytics tool that integrates with MySQL. |

In general, we recommend using Infobright for smaller volumes of data (up to Terabytes) and S3 / Hive for bigger volumes of data.

Note: if you start off with one storage solution e.g. Infobright, it is reasonably straightforward to migrate to another if need be.

