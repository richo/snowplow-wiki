Instructions on how to setup SnowCannon

## Installing SnowCannon

## Configuring SnowCannon

## Setting up Fluentd

### Overview

[Fluentd] [fluentd] is a lightweight log collector from the team at [Treasure Data] [treasure-data]. Fluentd supports a variety of different "output plugins", allowing SnowCannon to send SnowPlow events to Amazon S3 as well as other data sinks.

### Installation options

As explained in the [Fluentd installation guide] [fluentd-install], there are four ways to install Fluentd:

1. Binary package (as `td-agent`, maintained by Treasure Data)
2. RubyGem
3. `.tar.gz` file
4. Git repository

If you are running one of the OSes supported by `td-agent`, then we strongly recommend installing the binary package, as it the most straightforward and also bundles jemalloc. If the binary package is not available, then we recommend the RubyGem installation using RVM.

To install the binary package, please follow the appropriate [`td-agent` installation instructions] [td-agent-install]; the other three installation options are detailed back in the [Fluentd installation guide] [fluentd-install].

Fluentd installed? Great, now it's time to configure it...

### Configuration

For the purpose of this section, we are going to assume that you want to log your SnowPlow event data to **Amazon S3** using Fluentd.

Happily, with the `td-agent` binary packages, the Amazon S3 output plugin fluent-plugin-s3 is installed by default, so we just need to configure `td-agent` correctly.

Rest of section to come

[fluentd]: http://fluentd.org
[treasure-data]: http://treasure-data.com
[fluentd-install]: http://fluentd.org/doc/install.html
[td-agent-install]: http://help.treasure-data.com/kb/installing-td-agent-daemon

