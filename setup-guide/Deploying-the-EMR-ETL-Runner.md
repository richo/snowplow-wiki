# Deploying the SnowPlow ETL runner for EMR

## Table of Contents

1. [Introduction](#intro)
1.1 Blah
2. [Installation](#install)
3. [Usage](#usage)

<a name="intro"/>
## Introduction

To make it easier to run SnowPlow's Hive-based ETL (extract, transform, load) process on Amazon Elastic MapReduce, we have written the [SnowPlow::EmrEtlRunner] [emr-etl-runner] Ruby gem.

EmrEtlRunner is a command-line tool which runs and monitors our [Hive-based ETL process] [hive-etl] on EMR, as well as performing necessary housekeeping tasks (such as archiving the raw event logs).

This guide will take you through installing and configuring EmrEtlRunner on your own server.

<a name="install"/>
## Installation

### Assumptions

This guide assumes that you have already integrated one or more [SnowPlow trackers] [trackers] into your website or app, and have deployed a [SnowPlow collector] [collectors] which is logging raw SnowPlow events to Amazon S3.

If you have not completed these steps yet, then please consult the [Getting started] [getting-started] page on the SnowPlow Analytics website.

This guide also assumes that you have administrator access to a Unix-based server (e.g. Ubuntu, OS X, Fedora) on which you can install EmrEtlRunner and schedule a regular cronjob.

_In theory EmrEtlRunner can be deployed onto a Windows-based server, using the Windows Task Scheduler instead of cron, but this has not been tested or documented._

### Dependencies

To install EmrEtlRunner, first make sure that your server has **all** of the following installed:

1. **Ruby**. Please see the [Ruby Download Instructions] [ruby-install] as needed
2. **RubyGems**. Please see the [RubyGems Installation Instructions] [rubygems-install]
   as needed
3. **Nokogiri**. Please see the [Installing Nokogiri Guide] [nokogiri-install] as needed

You will also need a EC2 key setup in your Amazon EMR account.

Done? Right, now we can install EmrEtlRunner.

### Installation

EmrEtlRunner is [published on RubyGems.org] [rubygems-emretlrunner], so installing is easy:

    $ gem install snowplow-emr-etl-runner

Check it worked okay:

    $ snowplow-emr-etl-runner --version
    snowplow-emr-etl-runner 0.0.2

If you have any problems installing, it may be because of a missing dependency on the Nokogiri library. See the [Installing Nokogiri] [nokogiri-install] guide for help installing Nokogiri in your system.

### Configuration

EmrEtlRunner requires a YAML format configuration file to run. There is a configuration file template available in the SnowPlow GitHub repository at 
[`/3-etl/emr-etl-runner/config/config.yml`] [config-yml]. The template looks like this:

```yaml
:aws:
  :access_key_id: 'ADD HERE'
  :secret_access_key: 'ADD HERE'
:s3:
  :buckets:
    # Update assets if you want to host the serde and HiveQL yourself
    :assets: 's3n://snowplow-emr-assets'
    :in: 'ADD HERE'
    :processing: 'ADD HERE'
    :out: 'ADD HERE'
    :archive: 'ADD HERE'
:emr:
  # Can bump the below as EMR upgrades Hadoop
  :hadoop_version: '1.0.3'
  :placement: 'ADD HERE'
  :ec2_key_name: 'ADD HERE'
  # Adjust your Hive cluster below
  :jobflow:
    :instance_count: 2
    :master_instance_type: 'm1.small'
    :slave_instance_type: 'm1.small'
# Can bump the below as SnowPlow releases new versions
:snowplow:
  :serde_version: '0.4.9'
  :hiveql_version: '0.4.10'
```

To take each section in turn:

#### aws

The `aws` variables should be self-explanatory - supply your AWS access
key and secret here.

#### s3

Within the `s3` section, the `buckets` variables are as follows:

* `assets` is where the ETL job's static assets (HiveQL script plus Hive
  deserializer) are stored. You can leave this as is (pointing to
  SnowPlow Analytics' own public bucket containing these assets) or
  replace this with your own private bucket containing the assets
* `in` is the bucket containing the raw SnowPlow event logs to process
* `processing` is the bucket where the raw event logs will be moved to
  for processing
* `out` is the bucket where the processed SnowPlow-format event files
  will be stored
* `archive` is the bucket to move the raw SnowPlow event logs to after
  successful processing

All `buckets` variables should start with an S3 protocol - either
`s3://` or `s3n://`. Each variable can include a sub-folder within the
bucket as required, and a trailing slash is optional.

The following are all valid bucket settings:

    :buckets:
      :assets: s3://my-public-snowplow-assets
      :serde: s3n://my-snowplow-logs/
      :processing: s3n://my-snowplow-etl/processing

Please note that all buckets must exist prior to running EmrEtlRunner.

#### emr

This is where we configure the operation of EMR. The variables with
defaults can typically left as-is, but you will need to set:

1. `placement` is the Amazon EC2 region and availability zone
   in which the job should run, e.g. 'us-east-1a' or 'eu-west-1b'
2. `ec2_key_name` 

Section to come.

#### snowplow

This section allows you to update the versions of the Hive deserializer
(`serde`) and HiveQL script (`hiveql`) run by EmrEtlRunner. Having this
versioning in the `config.yml` file means that you can upgrade the ETL
process without having to update EmrEtlRunner itself.

<a name="usage"/>
## Usage

### Overview

There are two usage modes for the SnowPlow::Etl gem:

1. **Daily mode** where the gem is run daily to process the last 24 hours
   worth of CloudFront access logs ready for SnowPlow
2. **Catchup mode** where the gem is run across a "datespan" of multiple 
   days to bring processing of the CloudFront access logs up-to-date 

In particular, catchup mode is useful when running SnowPlow::Etl for the 
first time, or when something has gone wrong with daily mode and a day's
processing needs to be rerun.

### Command-line options

Invoke the SnowPlow::Etl gem using Bundler's `bundle exec` syntax:

    $ bundle exec snowplow-etl
    
Note that the `bundle exec` command will only work when you are inside the 
`snowplow-etl` folder.

The command-line options for SnowPlow::Etl look like this:

    Usage: snowplow-etl [options]

    Specific options:
        -c, --config CONFIG              configuration file
        -s, --start YYYY-MM-DD           start date (defaults to yesterday)
        -e, --end YYYY-MM-DD             end date (defaults to yesterday)

    Common options:
        -h, --help                       Show this message
        -v, --version                    Show version
   
Invoking SnowPlow::Etl with just a `--config` option puts it into daily
mode, processing CloudFront log files from **yesterday** only:

    $ bundle exec snowplow-etl --config my-config.yml
 
To run SnowPlow::Etl in catchup mode, you need to specify the start and end
dates as well as the `--config` option, like so:

    $ bundle exec snowplow-etl --config my-config.yml --start 2012-06-20 --end 2012-06-24 

This will run SnowPlow::Etl for the period 20 June 2012 to 24 June 2012
inclusive.

### Scheduling

Once you have the ETL process working smoothly, you can set up a daily cronjob
to automate the daily ETL process. The job should run in the early morning 
(say, 4am) when the full set of CloudFront log files for yesterday have been 
finalised.

The recommended way of running the ETL process as a daily cronjob is by using 
the shell script `bin/snowplow-etl-cron`. You need to edit this script and 
update the two variables:

    BUNDLE_GEMFILE=/path/to/snowplow/hive/snowplow-etl
    ETL_CONFIGFILE=/path/to/your-etl-config.yml

Now, assuming you're using the excellent [cronic] [cronic] as a wrapper for 
your cronjobs, and that both cronic and Bundler are on your path, you can 
configure your cronjob like so:

    0 4   * * *   root    cronic /path/to/snowplow/hive/snowplow-etl/bin/etl-cron

This will run the ETL job daily at 4am, emailing any failures to you via cronic.

[emr-etl-runner]: https://github.com/snowplow/snowplow/tree/master/3-etl/emr-etl-runner
[hive-etl]: https://github.com/snowplow/snowplow/tree/master/3-etl/hive-etl
[trackers]: https://github.com/snowplow/snowplow/tree/master/1-trackers
[collectors]: https://github.com/snowplow/snowplow/tree/master/2-collectors
[getting-started]: http://snowplowanalytics.com/product/get-started.html

[ruby-install]: http://www.ruby-lang.org/en/downloads/
[nokogiri-install]: http://nokogiri.org/tutorials/installing_nokogiri.html
[rubygems-install]: http://docs.rubygems.org/read/chapter/3
[rubygems-emretlrunner]: http://rubygems.org/gems/snowplow-emr-etl-runner

[config-yml]: https://github.com/snowplow/snowplow/blob/master/3-etl/emr-etl-runner/config/config.yml

[cronic]: http://habilis.net/cronic/