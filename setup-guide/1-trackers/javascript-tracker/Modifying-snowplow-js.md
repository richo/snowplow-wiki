[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Trackers**](choosing-a-tracker) > [**Javascript tracker**](Javascript-tracker-setup)

# Modifying snowplow.js

## Overview

It may be necessary for you to make changes to `snowplow.js`. If you do, then you will also need to minify your updated `snowplow.js` prior to deploying it. This guide covers how to do this.

To continue with this guide, you will need the following

* Access to a Unix-like command line with `sed` and the Java runtime installed
* Familiarity with and access to [Git] [git]
* The ability to install [YUI Compressor 2.4.2] [yuic]
* Some level of technical ability _ta2_, where `ta1 < ta2 < ninja`

Once you have those ready, please read on...

## 1. Check out the source code

First please download the source code to your development machine:

    $ cd ~/development
    $ git clone http://github.com/snowplow/snowplow.git
	...
	$ cd snowplow/1-trackers/javascript/js/
	$ ls
    snowplow.js   sp.js         snowpak.sh

In the listing above, `snowplow.js` is the original JavaScript; `sp.js` is the minified version and `snowpak.sh` is a Bash shell script for performing the minification.

## 2. Update `snowplow.js`

Making changes to `snowplow.js` (and testing them) is out of scope of this document.

Once you have made your changes and tested them, you are ready to **minify** your new `snowplow.js`.

### 3. Install YUI Compressor 2.4.2

To minify the JavaScript, we use a bash shell script which in turn uses YUI Compressor 2.4.2. YUI Compressor depends on the Java runtime - we'll assume that you already have Java installed.

To install YUI Compressor 2.4.2:

    $ sudo mkdir /opt/yui
    $ cd /opt/yui
    $ sudo wget http://yui.zenfs.com/releases/yuicompressor/yuicompressor-2.4.2.zip
    $ sudo unzip yuicompressor-2.4.2.zip
    $ sudo rm yuicompressor-2.4.2.zip

Just check that the required jarfile has now been installed:

    $ file /opt/yui/yuicompressor-2.4.2/build/yuicompressor-2.4.2.jar
    /opt/yui/yuicompressor-2.4.2/build/yuicompressor-2.4.2.jar: Zip archive data, at least v1.0 to extract

Now you're ready to minify `snowplow.js`.

## 4. Minify the JavaScript

Minification is handled by the bash shell script `snowpak.sh`, in the same folder as `snowplow.js`. `snowpak.sh` takes one argument, which is the path to the folder in which you installed YUI Compressor.

To run `snowpak.sh` is easy:

    $ cd ~/development/snowplow/1-trackers/javascript/js/
    $ ./snowpak.sh /opt/yui/yuicompressor-2.4.2

This will overwrite your existing `sp.js`.

A note: in theory it should be possible to use any JavaScript minifier or pipelining tool to minify the JavaScript - however, you would need to read through and understand what `snowpak.sh` is doing and make sure to recreate that same behaviour in your minification process.

## 5. Upload the minified JavaScript

Use your standard asset pipelining strategy to upload the minified `sp.js` JavaScript to your servers. Note that to avoid "mixed content" warnings, SnowPlow expects the `sp.js` JavaScript to be available both via HTTP and via HTTPS.

## 6. Test your updated JavaScript

As a final step, you will want to just check that your customised `sp.js` JavaScript is working okay.

To do this, please follow the testing instructions at the end of the guide to [[Integrating SnowPlow into your website]].

[aws]: http://aws.amazon.com/
[yuic]: http://developer.yahoo.com/yui/compressor/