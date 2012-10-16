# Deploying the SnowPlow ETL runner for EMR

## Table of Contents

1. [Introduction](#intro)
2. [Installation](#install)
 1. [Assumptions](#assumptions)
 2. [Dependencies](#dependencies)
 3. [Installation](#installation)
 4. [Configuration](#configuration)
3. [Usage](#usage)
 1. [Overview](#usage-overview)
 2. [Command-line options](#cli-options)
 3. [Running in each mode](#running)
4. [Scheduling](#scheduling)
 1. [Overview](#scheduling-overview)
 2. [cron](#cron)
 3. [Jenkins](#jenkins)
 4. [Windows Task Scheduler](#windows)

<a name="intro"/>
## Introduction

To make it easier to run SnowPlow's Hive-based ETL (extract, transform, load) process on Amazon Elastic MapReduce, we have written the [SnowPlow::EmrEtlRunner] [emr-etl-runner] Ruby gem.

EmrEtlRunner is a command-line tool which runs and monitors our [Hive-based ETL process] [hive-etl] on EMR, as well as performing the required housekeeping tasks (such as archiving the raw event logs).

This guide will take you through installing and configuring EmrEtlRunner on your own server.

<a name="install"/>
## Installation

<a name="assumptions"/>
### Assumptions

This guide assumes that you have already integrated one or more
[SnowPlow trackers][trackers] into your website or app, and have deployed a
[SnowPlow collector] [collectors] which is logging raw SnowPlow events to Amazon S3.

If you have not completed these steps yet, then please consult the [Getting started] [getting-started] page on the SnowPlow Analytics website.

This guide also assumes that you have administrator access to a Unix-based server (e.g. Ubuntu, OS X, Fedora) on which you can install EmrEtlRunner and schedule a regular cronjob.

_In theory EmrEtlRunner can be deployed onto a Windows-based server, using the Windows Task Scheduler instead of cron, but this has not been tested or documented._

<a name="dependencies"/>
### Dependencies

#### Software

To install EmrEtlRunner, first make sure that your server has **all** of the following installed:

1. **Ruby** - please see the [Ruby Download Instructions] [ruby-install]
2. **RubyGems** - please see the [RubyGems Installation Instructions] [rubygems-install]
3. **Nokogiri** - please see the [Installing Nokogiri Guide] [nokogiri-install]

#### EC2 key

You will also need a EC2 key setup in your Amazon EMR account.

For details on how to do this, please see the section **Configuring the client** in the
[[Setting up EMR]] wiki page. Make sure that you setup the EC2 key pair inside the region
in which you will be running your ETL jobs.

<a name="s3-buckets"/>
#### S3 buckets

EmrEtlRunner moves the SnowPlow event data through four distinct buckets during
the ETL process. These buckets are as follows:

1. The **in** bucket contains the raw SnowPlow event logs to process
2. The **processing** bucket is where EmrEtlRunner moves the raw event
   logs for processing
3. The **out** bucket is where EmrEtlRunner stores the processed
   SnowPlow-format event files
4. The **archive** bucket is where EmrEtlRunner moves the raw SnowPlow
   event logs after successful processing

You will have already setup the **in** bucket when you were configuring your SnowPlow
collector - but the other three buckets do not exist yet.

So, create the other three buckets in the same AWS region as your in bucket. Take
a note of their names as you will need to use these buckets shortly.

Done? Right, now we can install EmrEtlRunner.

<a name="installation"/>
### Installation

INSTRUCTIONS TO COME

Check it worked okay:

    $ snowplow-emr-etl-runner --version
    snowplow-emr-etl-runner 0.0.2

If you have any problems installing, it may be because of a missing dependency on the 
Nokogiri library. See the [Installing Nokogiri] [nokogiri-install] guide for help 
installing Nokogiri in your system.

<a name="configuration"/>
### Configuration

EmrEtlRunner requires a YAML format configuration file to run. There is a configuration
file template available in the SnowPlow GitHub repository at 
[`/3-etl/emr-etl-runner/config/config.yml`] [config-yml]. The template looks like this:

```yaml
:aws:
  :access_key_id: ADD HERE
  :secret_access_key: ADD HERE
:s3:
  :region: ADD HERE
  :buckets:
    # Update assets if you want to host the serde and HiveQL yourself
    :assets: s3://snowplow-emr-assets
    :log: ADD HERE
    :in: ADD HERE
    :processing: ADD HERE
    :out: ADD HERE
    :archive: ADD HERE
:emr:
  # Can bump the below as EMR upgrades Hadoop
  :hadoop_version: 1.0.3
  :placement: ADD HERE
  :ec2_key_name: ADD HERE
  # Adjust your Hive cluster below
  :jobflow:
    :instance_count: 2
    :master_instance_type: m1.small
    :slave_instance_type: m1.small
# Can bump the below as SnowPlow releases new versions
:snowplow:
  :serde_version: 0.4.9
  :hiveql_version: 0.4.10
```

To take each section in turn:

#### aws

The `aws` variables should be self-explanatory - enter your AWS access
key and secret here.

#### s3

The `region` variable should hold the AWS region in which your data buckets
(`in`, `processing`, `out`, `archive`) reside, e.g. "us-east-1" or
"eu-west-1".

Within the `s3` section, the `buckets` variables are as follows:

* `assets` holds the ETL job's static assets (HiveQL script plus Hive
  deserializer). You can leave this as-is (pointing to SnowPlow
  Analytics' own public bucket containing these assets) or replace this
  with your own private bucket containing the assets
* `log` is the bucket in which Amazon EMR will record processing
  information for this job run, including logging any errors  
* `in` is where you specify your in bucket _(see the
  [S3 Buckets](#s3-buckets) section above for details)_
* `processing` is where you specify your processing bucket _(see the
  [S3 Buckets](#s3-buckets) section above for details)_
* `out` is where you specify your out bucket _(see the
  [S3 Buckets](#s3-buckets) section above for details)_
* `archive` is where you specify your archive bucket _(see the
  [S3 Buckets](#s3-buckets) section above for details)_

Each of the bucket variables must start with an S3 protocol - either
`s3://` or `s3n://`. Each variable can include a sub-folder within the
bucket as required, and a trailing slash is optional.

**Important:** there is a bug in Hive on Amazon EMR where Hive dies if 
you attempt to write data to the root of an S3 bucket. **Therefore
always specify a sub-folder (e.g. `/events/`) for the `out` bucket
variable.**

The following are all valid bucket settings:

    :buckets:
      :assets: s3://my-public-snowplow-assets
      :in: s3n://my-cloudfront-logs/
      :processing: s3n://my-cloudfront-logs/processing
      :out: s3n://my-snowplow-data/events

Please note that all buckets must exist prior to running EmrEtlRunner.

#### emr

This is where we configure the operation of EMR. The variables with
defaults can typically be left as-is, but you will need to set:

1. `placement`, which is the Amazon EC2 region and availability zone
   in which the job should run, e.g. "us-east-1a" or "eu-west-1b"
2. `ec2_key_name`, which is the name of the Amazon EC2 key that you
   set up in the [Dependencies](#dependencies) above

Make sure that placement and the EC2 key you specify both belong to the
same region, or else EMR won't be able to find the key.

#### snowplow

This section allows you to update the versions of the Hive deserializer
(`serde`) and HiveQL script (`hiveql`) run by EmrEtlRunner. These
variables let you upgrade the ETL process without having to update
the EmrEtlRunner gem itself.

<a name="usage"/>
## Usage

<a name="usage-overview"/>
### Overview

There are two usage modes for EmrEtlRunner:

1. **Daily mode** where EmrEtlRunner is run daily to process the last 24
   hours worth of raw SnowPlow event logs ready
2. **Catchup mode** where EmrEtlRunner is run across a 'datespan' of
   multiple days to bring processing of the raw SnowPlow event logs
   up-to-date 

In particular, catchup mode is useful when running SnowPlow::Etl for the 
first time, or when something has gone wrong with daily mode and a day's
processing needs to be re-run.

<a name="cli-options"/>
### Command-line options

Invoke EmrEtlRunner using Bundler's `bundle exec` syntax:

    $ bundle exec snowplow-emr-etl-runner
    
Note that the `bundle exec` command will only work when you are inside the 
`snowplow-etl` folder.

The command-line options for EmrEtlRunner look like this:

    Usage: snowplow-etl [options]

    Specific options:
        -c, --config CONFIG              configuration file
        -s, --start YYYY-MM-DD           start date (defaults to yesterday)
        -e, --end YYYY-MM-DD             end date (defaults to yesterday)

    Common options:
        -h, --help                       Show this message
        -v, --version                    Show version

<a name="running"/>
### Running in each mode

#### Daily mode

Invoking EmrEtlRunner with just the `--config` option puts it into daily
mode, processing raw SnowPlow event logs from **yesterday** only:

    $ bundle exec bundle exec snowplow-emr-etl-runner --config my-config.yml

#### Catchup mode

To run EmrEtlRunner in catchup mode, you need to specify the start and end
dates as well as the `--config` option, like so:

    $ bundle exec bundle exec snowplow-emr-etl-runner \
      --config my-config.yml \
      --start 2012-06-20 \
      --end 2012-06-24 

This will run EmrEtlRunner for the period 20 June 2012 to 24 June 2012
inclusive.

<a name="scheduling"/>
## Scheduling

<a name="scheduling-overview"/>
### Overview

Once you have the ETL process working smoothly, you can schedule a daily task
to automate the daily ETL process. We recommend running the job in the early
morning  (say, 4am) when the full set of raw SnowPlow event logs for the day
before have definitely been finalised.

To consider the different scheduling options in turn:

<a name="cron"/>
### cron

The recommended way of scheduling the ETL process is as a daily cronjob using the 
shell script available in the SnowPlow GitHub repository at 
[`/3-etl/emr-etl-runner/bin/snowplow-emr-etl.sh`] [bash-script].

You need to edit this script and update the two variables:

    BUNDLE_GEMFILE=/path/to/snowplow/hive/snowplow-etl
    ETL_CONFIGFILE=/path/to/your-etl-config.yml

Now, assuming you're using the excellent [cronic] [cronic] as a wrapper for 
your cronjobs, and that both cronic and Bundler are on your path, you can 
configure your cronjob like so:

    0 4   * * *   root    cronic /path/to/your/gem/bin/snowplow-emr-etl.sh

This will run the ETL job daily at 4am, emailing any failures to you via cronic.

<a name="jenkins"/>
### Jenkins

Some developers use the [Jenkins] [jenkins] continuous integration server (or
[Hudson] [hudson], which is very similar) to schedule their Hadoop and Hive jobs.

Describing how to do this is out of scope for this guide, but the blog post
[Lowtech Monitoring with Jenkins] [jenkins-tutorial] is a great tutorial on using
Jenkins for non-CI-related tasks, and could be easily adapted to schedule
EmrEtlRunner.

<a name="windows"/>
### Windows Task Scheduler

For Windows servers, in theory it should be possible to use a Windows PowerShell
script plus [Windows Task Scheduler] [windows-task-scheduler] instead of bash and cron. However, this has not been tested or documented.

If you get this working, please let us know!

[emr-etl-runner]: https://github.com/snowplow/snowplow/tree/master/3-etl/emr-etl-runner
[hive-etl]: https://github.com/snowplow/snowplow/tree/master/3-etl/hive-etl
[trackers]: https://github.com/snowplow/snowplow/tree/master/1-trackers
[collectors]: https://github.com/snowplow/snowplow/tree/master/2-collectors
[getting-started]: http://snowplowanalytics.com/product/get-started.html

[ruby-install]: http://www.ruby-lang.org/en/downloads/
[nokogiri-install]: http://nokogiri.org/tutorials/installing_nokogiri.html
[rubygems-install]: http://docs.rubygems.org/read/chapter/3

[config-yml]: https://github.com/snowplow/snowplow/blob/master/3-etl/emr-etl-runner/config/config.yml
[bash-script]: https://github.com/snowplow/snowplow/blob/master/3-etl/emr-etl-runner/bin/snowplow-emr-etl.sh

[cronic]: http://habilis.net/cronic/
[jenkins]: http://jenkins-ci.org/
[hudson]: http://hudson-ci.org/
[jenkins-tutorial]: http://blog.lusis.org/blog/2012/01/23/lowtech-monitoring-with-jenkins/
[windows-task-scheduler]: http://en.wikipedia.org/wiki/Windows_Task_Scheduler#Task_Scheduler_2.0