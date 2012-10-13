# Deploying the SnowPlow ETL runner for EMR

## Table of Contents

1. [Introduction](#intro)
2. [Installation](#install)

<a name="intro"/>
## Introduction

To make it easier to run SnowPlow's Hive-based ETL (extract, transform, load) process on Amazon Elastic MapReduce, we have written the [SnowPlow::EmrEtlRunner] [emr-etl-runner] Ruby gem.

EmrEtlRunner is a command-line tool which runs and monitors our [Hive-based ETL  process] [hive-etl] on EMR, as well as performing necessary housekeeping tasks (such as archiving the raw event files on S3).

This guide will take you through installing and configuring EmrEtlRunner on your own server.

<a name="install"/>
## Installation

### Assumptions

This guide assumes that you have already integrated one or more [SnowPlow trackers] [trackers] into your website or app, and have deployed a [SnowPlow collector] [collectors] which is logging raw SnowPlow events to Amazon S3.

If you have not completed these steps yet, then please consult the [Getting started] [getting-started] page on the SnowPlow Analytics website.

This guide also assumes that you have administrator access to a Unix-based server (e.g. Ubuntu, OS X, Fedora) on which you can install EmrEtlRunner and schedule a regular cronjob.

In theory EmrEtlRunner can be deployed onto a Windows-based server, using the Windows Task Scheduler instead of cron, but this has not been tested or documented.

### Dependencies

To install EmrEtlRunner, first make sure that your server has **all** of the following installed:

1. Ruby installed. Please see the [Ruby Download Instructions] [ruby-install] as needed
2. RubyGems installed. Please see the [RubyGems Installation Instructions] [rubygems-install]
   as needed
3. Nokogiri installed. Please see the [Installing Nokogiri Guide] [nokogiri-install] as needed

Done? Right, now we can install EmrEtlRunner.

### Installation

**TODO: these instructions will change! These are for developing.**

First checkout the repository (TODO: remove the third line when we merge this 
branch to master):

    $ git clone git://github.com/snowplow/snowplow.git
    $ cd snowplow
    $ git checkout etl-scripts
    
Now install the SnowPlow::ETL gem on your system:

    $ cd hive/snowplow-etl
    $ gem build snowplow-etl.gemspec 
    $ sudo gem install snowplow-etl-0.0.1.gem
    $ bundle install

If you have any problems installing, it may be because of a missing dependency on the Nokogiri library. See the [Installing Nokogiri] [nokogiri-install] guide for help installing Nokogiri in your system.

Now test that the gem was installed successfully:

    $ bundle exec snowplow-etl --version
    snowplow-etl 0.0.1

Note that the `bundle exec` command will only work when you are inside the 
`snowplow-etl` folder.

### Configuration

SnowPlow::Etl requires a YAML format configuration file to run. There
is a configuration file template available in the repository at 
`hive/snowplow-etl/config/config.yml`. The contents should be as
follows:

    :aws:
      :access_key_id: ADD HERE
      :secret_access_key: ADD HERE
    :buckets:
      :query: ADD HERE
      :serde: ADD HERE
      :in: ADD HERE
      :out: ADD HERE
      :archive: ADD HERE

The `aws` variables should be self-explanatory. The `buckets` variables
are as follows:

* `query` is the bucket to install the ETL job's HiveQL scripts
* `serde` is the bucket to install the ETL job's Hive deserializer
* `in` is the bucket containing the CloudFront access logs to process
* `out` is the bucket to store the SnowPlow-format event files in
* `archive` is the bucket to move the processed CloudFront logs to

All `buckets` variables can include a sub-folder within the bucket as 
required. A trailing slash is optional. For example, the following are
all valid configuration settings:

    :buckets:
      :query: my-snowplow-static/hiveql/
      :serde: my-snowplow-static/jars
      :in: my-snowplow-log-bucket

Please note that all buckets must exist prior to running the EmrEtlRunner.

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
[cronic]: http://habilis.net/cronic/