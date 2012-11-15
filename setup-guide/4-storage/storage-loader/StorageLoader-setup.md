[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Storage**](Choosing-a-storage-module) > **StorageLoader Setup**

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
 3. [Running](#running)
4. [Scheduling](#scheduling)
 1. [Overview](#scheduling-overview)
 2. [Scheduling StorageLoader only](#storage-loader-cron)
 3. [Scheduling EmrEtlRunner and StorageLoader](#runner-and-loader-cron)
 4. [Alternatives to cron](#cron-alternatives)

<a name="intro"/>
## Introduction

Once you have your ETL process storing your SnowPlow events data as flat files in Amazon S3, you may also want to populate those same events into another storage option, such as [Infobright Community Edition] [ice].

To make it easier to feed your SnowPlow event data into databases such as Infobright, we have written the [SnowPlow::StorageLoader] [storage-loader] Ruby application.

StorageLoader is a command-line tool which downloads your SnowPlow event files and loads them into a local Infobright database.

This guide will take you through installing and configuring StorageLoader on your own server.

<a name="install"/>
## Installation

<a name="assumptions"/>
### Assumptions

This guide assumes that you have already:

1. [Set up EmrEtlRunner](Deploying-EmrEtlRunner) to process SnowPlow events and warehouse them on Amazon S3
2. [Set up Infobright Community Edition (ICE)](Infobright-storage-setup) ready to store those same SnowPlow events

If you have not completed these two steps yet, then please follow the linked setup guides.

Note that this guide assumes that you have configured EmrEtlRunner to output the **non-Hive** SnowPlow event format. The Hive SnowPlow event format will not work with Infobright.

This guide assumes that you have administrator access to the Unix-based server (e.g. Ubuntu, OS X, Fedora) on which you installed ICE, and will install StorageLoader on the same server.

Please note that ICE can be deployed onto a Windows-based server, and in theory StorageLoader could be installed on a Windows-based server too, using the Windows Task Scheduler instead of cron, but this has not been tested or documented.

Also, note that, in theory, StorageLoader should work with [Infobright Enterprise Edition (IEE)] [iee] as well as ICE; however we have not yet tested this.

<a name="dependencies"/>
### Dependencies

#### Software

To install StorageLoader, first make sure that your server has **all** of the following installed:

1. **Git** - see the [Git Installation Guide] [git-install]
2. **Ruby** - see the [Ruby Download Instructions] [ruby-install]
3. **RubyGems** - see the [RubyGems Installation Instructions] [rubygems-install]
5. **Bundler** - this is a one-liner: `gem install bundler`

<a name="s3-buckets"/>
#### S3 buckets

StorageLoader moves the SnowPlow event files through three distinct S3 buckets during
the load process. These buckets are as follows:

1. **In Bucket** - contains the SnowPlow event files to process
2. **Archive Bucket** - where StorageLower moves the SnowPlow
   event files after successful loading

The In Bucket for StorageLoader is the same as the Out Bucket for the EmrEtlRunner -
i.e. you will already have setup this bucket.

We recommend creating a new bucket for the Archive Bucket - i.e. do **not** re-use
EmrEtlRunner's own Archive Bucket. Create the required Archive Bucket in the same
AWS region as your In Bucket.

Done? Right, now we can install StorageLoader.

<a name="installation"/>
### Installation

First, checkout the SnowPlow repository and navigate to the StorageLoader root:

    $ git clone git://github.com/snowplow/snowplow.git
    $ cd snowplow/4-storage/storage-loader
    
Next install the application on your system:

    $ gem build snowplow-storage-loader.gemspec
    $ sudo gem install snowplow-storage-loader-0.0.1.gem

Check it worked okay:

    $ bundle exec snowplow-storage-loader --version
    snowplow-storage-loader 0.0.1

<a name="configuration"/>
### Configuration

StorageLoader requires a YAML format configuration file to run. There is a configuration
file template available in the SnowPlow GitHub repository at 
[`/4-storage/storage-loader/config/config.yml`] [config-yml]. The template looks like this:

```yaml
:aws:
  :access_key_id: ADD HERE
  :secret_access_key: ADD HERE
:s3:
  :region: ADD HERE
  :buckets:
    :in: ADD HERE
    :archive: ADD HERE
:download:
  :folder: ADD HERE # Where to store the downloaded files
# Currently assumes we are loading only one target
:storage:
  :type: infobright # No other storage types supported yet
  :database: ADD HERE # Name of database
  :table:    ADD HERE # Name of the table to populate
  :username: ADD HERE # Or leave blank to default to the user running the script
  :password: ADD HERE # Or leave blank if no password
```

To take each section in turn:

#### aws

The `aws` variables should be self-explanatory - enter your AWS access
key and secret here.

#### s3

The `region` variable should hold the AWS region in which your two data
buckets (In Bucket and Archive Bucket) are located, e.g. "us-east-1"
or "eu-west-1".

Within the `s3` section, the `buckets` variables are as follows:

* `in` is where you specify your In Bucket
* `archive` is where you specify your Archive Bucket

Each of the bucket variables must start with an S3 protocol - either
`s3://` or `s3n://`. Each variable can include a sub-folder within the
bucket as required, and a trailing slash is optional.

The following are examples of valid bucket settings:

    :buckets:
      :in: s3n://my-snowplow-data/events/
      :archive: s3n://my-archived-snowplow-data

Please note that all buckets must exist prior to running StorageLoader.

#### download

This is where we configure the StorageLoader download operation, which
downloads the SnowPlow event files from Amazon S3 to your local server, 
ready for loading into your database.

You will need to set the `folder` variable to a local directory path -
please make sure that this path exists, is writable by StorageLoader
and is empty.

#### storage

In this section we configure exactly what database StorageLoader should
load our SnowPlow events into. At the moment, StorageLoader supports
only one load target, and this load target must be an Infobright
Community Edition database.

To take each variable in turn:

1. `type`, what type of database are we loading into? Currently the
   only supported format is "infobright"
2. `database`, the name of the database to load
3. `table`, the name of the database table which will store your
   SnowPlow events. Must have been setup previously  
4. `username`, the database user to load your SnowPlow events with.
   You can leave this blank to default to the user running the script
5. `password`, the password for the database user. Leave blank if there
   is no password

Note that there is no way of specifying what server the target database
is on - StorageLoader assumes that the database is on the server it is
being run on.

<a name="usage"/>
## Usage

<a name="usage-overview"/>
### Overview

Running the StorageLoader is very straightforward - please review the
command-line options in the next section. 

<a name="cli-options"/>
### Command-line options

Invoke StorageLoader using Bundler's `bundle exec` syntax:

    $ bundle exec snowplow-storage-loader
    
Note that the `bundle exec` command will only work when you are inside the 
`storage-loader` folder.

The command-line options for StorageLoader look like this:

    Usage: snowplow-storage-loader [options]

    Specific options:
        -c, --config CONFIG              configuration file
        -s, --skip download|load         skip work step(s)

    Common options:
        -h, --help                       Show this message
        -v, --version                    Show version

A note on the `--skip` option: this skips the work steps **up to and
including** the specified step. To give an example: `--skip load` skips
downloading the logs from In Bucket **and** skips loading the files into
your Infobright database, i.e. it **only** performs the final archiving
step.

<a name="running"/>
### Running

As per the above, running StorageLoader is a matter of populating
your configuration file, let's call it `my-config.yml` for this
example, and then invoking StorageLoader like so: 

    $ bundle exec snowplow-storage-loader --config my-config.yml

### Troubleshooting

#### locate command missing

StorageLoader depends on SnowPlow's [Infobright Ruby Loader] [irl],
which in turn uses the `locate` shell command. If your shell complains
that this is missing, in which case you can install it separately.

To install and configure `locate` on Debian/Ubuntu:

    $ sudo apt-get install mlocate
    $ sudo updatedb

<a name="scheduling"/>
## Scheduling

<a name="scheduling-overview"/>
### Overview

Once you have the load process working smoothly, you can schedule a daily
(or more frequent) task to automate the storage process.

The standard way of scheduling the load process is as a daily cronjob. We
provide two alternative shell scripts for you to use in your scheduling:

1. [snowplow-storage-loader.sh] [loader-bash] - this script just runs the
   StorageLoader
2. [snowplow-runner-and-loader.sh] [combo-bash] - this script runs the
   EmrEtlRunner immediately followed by the StorageLoader

The second script is recommended assuming you want to run the StorageLoader
immediately after EmrEtlRunner has completed its work.

To consider each scheduling option in turn:

<a name="storage-loader-cron"/>
### Scheduling StorageLoader only

The shell script [`/4-storage/storage-loader/bin/snowplow-storage-loader.sh`] [loader-bash]
runs the StorageLoader app only.

You need to edit this script and update the two variables:

    LOADER_PATH=/path/to/snowplow/4-storage/snowplow-storage-loader
    LOADER_CONFIG=/path/to/your-loader-config.yml

Now, assuming you're using the excellent [cronic] [cronic] as a wrapper for 
your cronjobs, and that both cronic and Bundler are on your path, you can 
configure your cronjob like so:

    0 6   * * *   root    cronic /path/to/snowplow/4-storage/bin/snowplow-runner-and-loader.sh

This will run the ETL job daily at 6am, emailing any failures to you via cronic. Please make
sure that your SnowPlow events have been safely generated and stored in your In Bucket prior
to 6am. 

<a name="runner-and-loader-cron"/>
### Scheduling EmrEtlRunner and StorageLoader

The shell script [`/4-storage/storage-loader/bin/snowplow-storage-loader.sh`] [combo-bash]
runs EmrEtlRunner, immediately followed by StorageLoader - i.e. it chains them together. At
SnowPlow, this is the scheduling option we use.

If you use this script, you can delete any separate cronjob for the EmrEtlRunner alone.

You need to update this script and update the **four** variables at the top:

    RUNNER_PATH=/path/to/snowplow/3-etl/snowplow-emr-etl-runner
    LOADER_PATH=/path/to/snowplow/4-storage/snowplow-storage-loader

    RUNNER_CONFIG=/path/to/your-runner-config.yml
    LOADER_CONFIG=/path/to/your-loader-config.yml

Using [cronic] [cronic] as a wrapper, and with cronic and Bundler on your path, configure
your cronjob like so:

    0 4   * * *   root    cronic /path/to/snowplow/4-storage/bin/snowplow-runner-and-loader.sh

This will run the ETL job and then the database load daily at 4am, emailing any failures
to you via cronic.

<a name="cron-alternatives"/>
### Alternatives to cron

In place of cron, you could schedule StorageLoader using a continuous integration
server such as [Jenkins] [jenkins], or potentially use the
[Windows Task Scheduler] [windows-task-scheduler].

These options are explored in a little more detail in the [[Deploying EmrEtlRunner]] guide.

[storage-loader]: https://github.com/snowplow/snowplow/tree/master/4-storage/storage-loader

[ice]: http://www.infobright.org/
[iee]: http://www.infobright.com/Products/
[irl]: https://github.com/snowplow/infobright-ruby-loader

[hive-etl]: https://github.com/snowplow/snowplow/tree/master/3-etl/hive-etl
[trackers]: https://github.com/snowplow/snowplow/tree/master/1-trackers
[collectors]: https://github.com/snowplow/snowplow/tree/master/2-collectors
[getting-started]: http://snowplowanalytics.com/product/get-started.html

[git-install]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[ruby-install]: http://www.ruby-lang.org/en/downloads/
[nokogiri-install]: http://nokogiri.org/tutorials/installing_nokogiri.html
[rubygems-install]: http://docs.rubygems.org/read/chapter/3

[config-yml]: https://github.com/snowplow/snowplow/blob/master/4-storage/storage-loader/config/config.yml
[loader-bash]: https://github.com/snowplow/snowplow/blob/master/4-storage/storage-loader/bin/snowplow-storage-loader.sh
[combo-bash]: https://github.com/snowplow/snowplow/blob/master/4-storage/storage-loader/bin/snowplow-runner-and-loader.sh

[cronic]: http://habilis.net/cronic/
[jenkins]: http://jenkins-ci.org/
[jenkins-tutorial]: http://blog.lusis.org/blog/2012/01/23/lowtech-monitoring-with-jenkins/
[windows-task-scheduler]: http://en.wikipedia.org/wiki/Windows_Task_Scheduler#Task_Scheduler_2.0