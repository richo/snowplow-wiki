## Before You Begin

### Assumptions

For the purpose of this guide, we are going to assume:

1. That you want to log the SnowPlow event data collected by SnowCannon to **Amazon S3**
2. That you want to use **Fluentd** to handle the actual file upload to S3 (versus SnowCannon's built-in S3 sink)
3. That you will use **Forever** as your service daemon
4. That you will use **Monit** as your process monitor

If any of these assumptions are not true, then you may need to additionally consult the SnowCannon README file, the Fluentd user manual and/or the manual for your preferred service daemon or process monitor.

### Hardware and OS

If you can choose your hardware, we recommend running SnowCannon on a **multi-core machine** (preferably quad-core or better), to take advantage of SnowCannon's built-in clustering support.

If you can choose your OS, we recommend deploying SnowCannon on **Ubuntu 12.04 LTS / Precise**. This is the most modern Debian-based OS supported by Fluentd (which we use alongside SnowCannon).

## Installing SnowCannon

## Configuring SnowCannon

## Setting up Fluentd

### Overview

[Fluentd] [fluentd] is a lightweight log collector from the team at [Treasure Data] [treasure-data]. SnowCannon supports Fluentd as an event sink - and Fluentd in turn supports a variety of different "output plugins", meaning that SnowCannon to use Fluentd to send SnowPlow events to Amazon S3 as well as other data stores.

### Installation options

As explained in the [Fluentd installation guide] [fluentd-install], there are four ways to install Fluentd:

1. Binary package (as `td-agent`, maintained by Treasure Data)
2. RubyGem
3. `.tar.gz` file
4. Git repository

If you are running one of the OSes supported by `td-agent`, then we strongly recommend installing the binary package, as it the most straightforward and also bundles jemalloc. If the binary package is not available, then we recommend the RubyGem installation using RVM.

### Installation

#### Binary package

To install the binary package, please follow the appropriate [td-agent installation instructions] [td-agent-install].

Happily, with the `td-agent` binary packages, the Amazon S3 output plugin fluent-plugin-s3 is installed by default.

#### Manual installation

For any of the three manual installation options, please consult the [Fluentd installation guide] [fluentd-install].

With the manual options, you will need to install jemalloc and the Amazon S3 output plugin fluent-plugin-s3 separately. Installing jemalloc is out of scope of this guide; happily installing the Amazon S3 plugin is easy:

    $ fluent-gem install fluent-plugin-s3

### Testing

To confirm that Fluentd was installed successfully, run the following commands:

    $ cd ~
    $ fluentd --setup ./fluent
    $ fluentd -c ./fluent/fluent.conf -vv &
    $ echo '{"json":"message"}' | fluent-cat debug.test

The last command sends Fluentd a message ‘{“json”:”message”}’ with a “debug.test” tag. If the installation was successful, Fluentd will output the following message:

    2011-07-10 16:49:50 +0900 debug.test: {"json":"message"}

### Configuration

#### Setting up log folders

To come

#### Updating fluent.conf

To come

Rest of section to come

[fluentd]: http://fluentd.org
[treasure-data]: http://treasure-data.com
[fluentd-install]: http://fluentd.org/doc/install.html
[td-agent-install]: http://help.treasure-data.com/kb/installing-td-agent-daemon