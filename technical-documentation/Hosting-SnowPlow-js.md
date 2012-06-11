# Self-hosting snowplow.js

## Overview

In addition to self-hosting the tracking pixel, it also possible to self-host the SnowPlow tracking JavaScript, `snowplow.js`. Unlike the tracking pixel, this does not have an impact on where your SnowPlow data gets stored, but it does have some definite advantages over using a 3rd party-hosted JavaScript: 

1. Hosting your JavaScript allows you to use your own JavaScript minification and asset pipelining approach (e.g. bundling all JavaScripts into one minified JavaScript)
2. As [Douglas Crockford] [crockford] put it about third-party JavaScripts: _"it is extremely unwise to load code from servers you do not control."_
3. Perhaps most importantly, hosting `snowplow.js` on your own server means that the SnowPlow tracking cookie will be **first-party**, not **third-party**. This is good from a user-privacy perspective, and it also gives better accuracy in counting unique visitors (as first-party cookies are more often accepted and less often deleted by users) 

So if you want to self-host `snowplow.js`, please read on...

## Prerequisites

To self-host `snowplow.js` you will need the following:

* Access to a Unix-like command line with `sed` and the Java runtime installed
* Familiarity with and access to [Git] [git]
* The ability to install [YUI Compressor 2.4.2] [yuic]
* Some level of technical ability _ta2_, where `ta1 < ta2 < ninja`

Once you have those ready, please read on...

## Self-hosting instructions

### 1. Check out the source code

First please download the source code to your development machine:

    $ cd ~/development
    $ git clone git@github.com:snowplow/snowplow.git
	...
	$ cd snowplow/tracker/js/
	$ ls 
    snowplow.js   sp.js         snowpak.sh

In the listing above, `snowplow.js` is the original JavaScript; `sp.js` is the minified version and `snowpak.sh` is a Bash shell script for performing the minification.

### 2. Install YUI Compressor 2.4.2

To minify the JavaScript, we use a bash shell script which in turn uses YUI Compressor 2.4.2. YUI Compressor depends on the Java runtime - but we'll assume that you already have Java installed.

To install YUI Compressor 2.4.2:

    $ sudo mkdir /opt/yui
    $ cd /opt/yui
    $ sudo wget http://yui.zenfs.com/releases/yuicompressor/yuicompressor-2.4.2.zip
    $ sudo unzip yuicompressor-2.4.2.zip

Just check that the required jarfile has now been installed:

    $ file /opt/yui/yuicompressor-2.4.2/build/yuicompressor-2.4.2.jar
    /opt/yui/yuicompressor-2.4.2/build/yuicompressor-2.4.2.jar: Zip archive data, at least v1.0 to extract

Now you're ready to minify `snowplow.js`.

### 3. Minify the JavaScript

Minification is handled by the bash shell script `snowpak.sh`, in the same folder as `snowplow.js`. `snowpak.sh` takes one argument, which is the path to the folder in which you installed YUI Compressor.

To run `snowpak.sh` is easy:

    $ cd ~/development/snowplow/tracker/js/
    $ ./snowpak.sh /opt/yui/yuicompressor-2.4.2

This will overwrite your existing `sp.js`.

A note: in theory it should be possible to use any JavaScript minifier or pipelining tool to minify the JavaScript - however, you would need to read through and understand what `snowpak.sh` is doing and make sure to recreate that same behaviour in your minification process.

### 4. Upload the minified JavaScript

Use your standard asset pipelining strategy to upload the minified `sp.js` JavaScript to your servers. Note that to avoid "mixed content" warnings, SnowPlow expects the `sp.js` JavaScript to be available both via HTTP and via HTTPS.

### 5. Update your header script

Now you need to update the JavaScript code for SnowPlow in your website's `<head>` section to use your hosted copy of `snowplow.js`. For the purposes of this section, we're going to assume that you have a minified `sp.js` available at the URL:

    http(s)://eskimo-ice.com/js/sp.js

If you are using **asynchronous tracking**, then update the corresponding line in your header script to look like this:

```javascript
sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://eskimo-ice.com/js/sp.js';
```

Whereas if you are using **synchronous tracking**, then update the corresponding line in your header script to look like this:

```javascript       
var spSrc = ('https:' == document.location.protocol ? 'https' : 'http') + '://eskimo-ice.com/js/sp.js';
```

Simple as that really.

### 6. Test your hosted JavaScript

As a final step, you'll want to just check that your self-hosted JavaScript is working okay. To do this:

* Upload a test page (possibly based on `tracker/examples/async.html`) to your server
* 

**To write**

[aws]: http://aws.amazon.com/
[pixel]: /snowplow/snowplow/raw/master/tracker/static/ice.png
[yuic]: http://developer.yahoo.com/yui/compressor/
[crockford]: https://github.com/douglascrockford