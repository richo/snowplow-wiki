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

    $ wget https://github.com/snowplow/snowplow/master/1-collectors/javascript/js/sp.js 

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

Choose `snowplow-static-js` as your Amazon S3 Origin bucket and hit **Continue**:

![dist-details] [dist-details]

On this screen you want to leave Logging as **Off**. Then hit `Continue` to review a summary of your new distribution:

![dist-review] [dist-review]

Hit **Create Distribution** and then you should see something like this:

![dist-enabled] [dist-enabled]

Write down your CloudFront distribution's **Domain Name** (highlighted above) - e.g. `http://d1x5tduoxffdr7.cloudfront.net`. You will need this later when you integrate SnowPlow into your website.

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

[aws]: http://aws.amazon.com/
[yuic]: http://developer.yahoo.com/yui/compressor/
[crockford]: https://github.com/douglascrockford
[js-]: /snowplow/snowplow/raw/master/2-collectors/cloudfront/static/ice.png
[js-bucket]: technical-documentation/images/02_log_bucket.png
[js-select]: technical-documentation/images/02_pixel_select.png
[js-upload]: technical-documentation/images/02_pixel_upload.png
[js-permissions]: technical-documentation/images/02_pixel_permissions.png
[js-ready]: technical-documentation/images/02_pixel_ready.png
[dist-create]: technical-documentation/images/02_js_dist_create.png
[dist-details]: technical-documentation/images/02_js_dist_details.png
[dist-review]: technical-documentation/images/02_js_dist_review.png
[dist-enabled]: technical-documentation/images/02_js_dist_enabled.png