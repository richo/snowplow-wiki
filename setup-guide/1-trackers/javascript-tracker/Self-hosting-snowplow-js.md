# Self-hosting snowplow.js

## Overview

In addition to self-hosting the tracking pixel, we recommend self-hosting the SnowPlow tracking JavaScript, `snowplow.js`. Unlike the tracking pixel, this does not have an impact on where your SnowPlow data gets stored, but it does have some definite advantages over using a third-party-hosted JavaScript:

1. Hosting your own JavaScript allows you to use your own JavaScript minification and asset pipelining approach (e.g. bundling all JavaScripts into one minified JavaScript)
2. As [Douglas Crockford] [crockford] put it about third-party JavaScripts: _"it is extremely unwise to load code from servers you do not control."_

The alternative to self-hosting `snowplow.js` is to use the version hosted by **SnowPlow Analytics**, which is okay too. But if you want to self-host `snowplow.js`, please read on...

## Contents

1. [Pre-requisites](#prerequisites)
2. [Self-hosting instructions](#self-hosting-instructions)
3. [Advanced options](#advanced-options)

<a name="prerequisites" />
## Pre-requisites

For the purposes of this guide, we are going to assume that you want to serve the standard `snowplow.js` from CloudFront. (We discuss other approaches in the [Advanced options](#advanced-options) section below.). To accomplish this, you will need the following:

* An account with [Amazon Web Services] [aws]
* S3 and CloudFront enabled within your AWS account
* Some technical chops (not too many)

Once you have those ready, please read on...

<a name="self-hosting-instructions" />
## Self-hosting instructions

### 1. Create a bucket for the JavaScript

First create a new bucket within your Amazon S3 account to store the pixel. Call this bucket something like `snowplow-static-js`:

![js-bucket] [js-bucket]

A couple of notes on this:

* Don't enable logging on this bucket
* You won't be able to call this bucket exactly `snowplow-static-js`. This is because Amazon S3 bucket names have to be globally unique, and `snowplow-static-js` is unfortunately taken by us!

### 2. Upload the JavaScript

You want to upload the **minified** version of the SnowPlow JavaScript, which is called `sp.js`. You can obtain this by right-clicking [this JavaScript file] [js] and selecting **Save Link As...**, or if you prefer run:

    $ wget https://raw.github.com/snowplow/snowplow/master/1-trackers/javascript/js/sp.js

Now you're ready to upload the JavaScript file into S3. Within the S3 pane, hit **Upload** and browse to your file:

![js-select] [js-select]

Then hit **Open** and you will see the following screen:

![js-upload] [js-upload]

Hit **Set Details >**, then hit **Set Permissions >** to set permissions on this file allowing **Everyone** to **Open/Download** it:

![js-permissions] [js-permissions]

Now hit **Start Upload** to upload the JavaScript file into your bucket. When done, you should have something like this:

![js-ready] [js-ready]

The Properties pane at the bottom is viewable by pressing the **Properties** button and selecting `sp.js`.

### 4. Create your CloudFront distribution

Now you are ready to create the CloudFront distribution which will serve your JavaScript. In the CloudFront tab, hit the **Create Distribution** button:

![dist-create] [dist-create]

Select the **Download** option and hit **Continue**:

![dist-origin] [dist-origin]

For the **Origin Domain Name**, choose your S3 bucket (`snowplow-static-js`) from the dropdown, and accept the suggested **Origin ID**. Now hit **Continue**:

![dist-behaviour] [dist-behaviour]

The defaults are fine on this screen, hit **Continue** again:

![dist-details] [dist-details]

On this screen leave Logging as **Off** and hit **Continue** to review a summary of your new distribution:

![dist-review] [dist-review]

Hit **Create Distribution** and then you should see something like this:

![dist-enabled] [dist-enabled]

Write down your CloudFront distribution's **Domain Name** - e.g. `http://d1fc8wv8zag5ca.cloudfront.net`. You will need this later when you integrate SnowPlow into your website.

### 5. Testing your JavaScript file on CloudFront

Before testing, take a 10 minute coffee or brandy break (that's how long CloudFront takes to synchronize).

Done? Now just check that you can access your JavaScript file over both HTTP and HTTPS using a browser, `wget` or `curl`:

    http://{{SUBDOMAIN}}.cloudfront.net/sp.js
    https://{{SUBDOMAIN}}.cloudfront.net/sp.js

If you have any problems, then double-check your CloudFront distribution's URL, and check the permissions on your `sp.js` file: it must be Openable by Everyone.

All done! That's it - you now have a CloudFront distribution which will serve your SnowPlow JavaScript to anybody anywhere in the world, fast.

<a name="advanced-options" />
## Advanced options

The guide above assumed that you were happy to take the already-minified `sp.js` and host it on CloudFront. In fact, you may prefer to:

* Update the contents of `snowplow.js` and then minify it yourself, _and/or:_
* Add the un-minified `snowplow.js` into your own asset pipelining process and serve it using something other than CloudFront

The first option above is explored in more detail in the guide to [[Modifying snowplow.js|Modifying-snowplow-js]].

The second option is out of the scope of the SnowPlow documentation but you should get some ideas as to how the minification should be handled from that same guide, [[Modifying snowplow.js|Modifying-snowplow-js]].

## All done?

Now [[add the SnowPlow javascript tracking tags to your site|Integrating-SnowPlow-into-your-website]]

[aws]: http://aws.amazon.com/
[yuic]: http://developer.yahoo.com/yui/compressor/
[crockford]: https://github.com/douglascrockford
[js]: https://raw.github.com/snowplow/snowplow/master/1-trackers/javascript/js/sp.js
[js-bucket]: setup-guide/images/js_bucket.png
[js-select]: setup-guide/images/js_select.png
[js-upload]: setup-guide/images/js_upload.png
[js-permissions]: setup-guide/images/js_permissions.png
[js-ready]: setup-guide/images/js_ready.png
[dist-create]: setup-guide/images/js_dist_create.png
[dist-origin]: setup-guide/images/js_dist_origin.png
[dist-behaviour]: setup-guide/images/js_dist_behaviour.png
[dist-details]: setup-guide/images/js_dist_details.png
[dist-review]: setup-guide/images/js_dist_review.png
[dist-enabled]: setup-guide/images/js_dist_enabled.png