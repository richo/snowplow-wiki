## Overview

This guide assumes that you will be hosting your the tracking pixel used by your SnowPlow implementation. (Self hosting the tracking pixel on your own Amazon S3 / Cloudfront account means that all your SnowPlow data will be stored on your own Amazon account.)

If you prefer the tracking pixel and data to be hosted by a 3rd party, then get in touch with the team at [Keplar LLP](#http://www.keplarllp.com) for this and other services related to SnowPlow implementation.

Self hosting the tracking pixel is straightforward. Implementation steps are described below.

## Contents

1. [Pre-requisites](#prerequisites)
2. [Self-hosting instructions](#self-hosting-instructions)
3. [Privacy considerations](#privacy): issues that impact your choice of where to host your tracking pixel

<a name="prerequisites" />
## Pre-requisites

If you want to self-host the tracking pixel, you will need the following:

* An account with [Amazon Web Services] [aws]
* S3 and CloudFront enabled within your AWS account
* Some level of technical ability _ta1_, where `noob < ta1 < ninja`

Once you have those ready, please read on...

<a name="self-hosting-instructions" />
## Self-hosting instructions

### 1. Create a bucket for the pixel

First create a new bucket within your Amazon S3 account to store the pixel. Call this bucket something like `snowplow-static`:

![pixelbucket] [pixelbucket]

A couple of notes on this:

* Don't enable logging on this bucket
* You won't be able to call this bucket exactly `snowplow-static`. This is because Amazon S3 bucket names have to be globally unique, and `snowplow-static` is unfortunately taken!
* Because we will be using CloudFront, it doesn't particularly matter which data center you choose (although see [A note on privacy](#privacy) below)

### 2. Create a bucket for the CloudFront logging

Now let's create a second bucket to store our CloudFront logs - i.e. our actual SnowPlow data. Call this bucket something like `snowplow-logs`:

![logbucket] [logbucket]

The notes above also hold true for this bucket: don't enable logging, and expect to have to change the bucket's name slightly.

### 3. Upload a tracking pixel

You can obtain a 1x1 transparent tracking pixel by right-clicking [this image file] [pixel] and selecting **Save Link As...**, or if you prefer run:

    $ wget https://github.com/snowplow/snowplow/raw/master/tracker/static/ice.png 	

Now you're ready to upload the pixel into S3. Within the S3 pane, hit **Upload** and browse to your tracking pixel:

![pixelselect] [pixelselect]

Then hit **Open** and you will see the following screen:

![pixelupload] [pixelupload]

Hit **Set Details >**, then hit **Set Permissions >** to set permissions on this file allowing **Everyone** to **Open/Download** it:

![pixelpermissions] [pixelpermissions]

Now hit **Start Upload** to upload the pixel into your bucket. When done, you should have something like this:

![pixelready] [pixelready]

The Properties pane at the bottom is viewable by pressing the **Properties** button and selecting `ice.png`.

### 4. Create your CloudFront distribution

Now you are ready to create the CloudFront distribution which will serve your tracking pixel. In the CloudFront tab, hit the **Create Distribution** button:

![distcreate] [distcreate]

Choose `snowplow-static` as your Amazon S3 Origin bucket and hit **Continue**:

![distdetails] [distdetails]

On this screen you just want to switch Logging to **On** and select `snowplow-logs` as your Log Bucket. Then hit `Continue` to review a summary of your new distribution:

![distreview] [distreview]

Hit **Create Distribution** and then you should see something like this:

![distenabled] [distenabled]

Write down your CloudFront distribution's **Domain Name** (highlighted above) - e.g. `http://d1x5tduoxffdr7.cloudfront.net`. You will need this in the next step.

That's it - you now have a CloudFront distribution which will serve your tracking pixel fast to anybody anywhere in the world and log the request to Amazon S3 in your `snowplow-logs` bucket. 

### 5. Testing your tracking pixel on CloudFront

Before testing, take a 10 minute coffee or brandy break (that's how long CloudFront takes to synchronize).

Done? Now just check that you can access your pixel over both HTTP and HTTPS using a browser, `wget` or `curl`:

    http://{{SUBDOMAIN}}.cloudfront.net/ice.png
    https://{{SUBDOMAIN}}.cloudfront.net/ice.png

If you have any problems, then double-check your CloudFront distribution's URL, and check the permissions on your pixel: it must be Openable by Everyone.

### 6. Update your header script

Now you need to update the JavaScript code for SnowPlow in your website's `<head>` section to work with your custom tracking pixel. We're assuming here that you have already followed the steps in [Integrating `snowplow.js` into your site] [integrating].

The secret is to realise that SnowPlow's `setAccount()` method in fact takes a CloudFront subdomain as its argument - so using your own CloudFront distribution is super-simple.

If you are using **asynchronous tracking** and your CloudFront distribution's URL is `http://d1x5tduoxffdr7.cloudfront.net`, then update the appropriate line in your header script to look like this:

```javascript
_snaq.push(['setAccount', 'd1x5tduoxffdr7']);
```

Whereas if you are using **synchronous tracking**, then update your header script to look like this:

```javascript
var snowplowTracker = SnowPlow.getTracker('d1x5tduoxffdr7');
```

### 7. Test snowplow.js with your tracking pixel

**To write**

### 8. Inspect the CloudFront access logs

**To write**

<a name="privacy"/>
# A note on privacy

Above we mentioned that, from a performance perspective, it is not important which Amazon data center you choose to self-host your pixel, or indeed your JavaScript:

![chooseregion] [chooseregion]

However, data center choice, particularly for your access logs, does matter from a data privacy perspective. For example, at the time of writing Amazon Web Services [recommends] [dataprivacy] storing data in the EU (Ireland) region if you wish to comply with EU data privacy regulations.

It is your responsibility to ensure that you comply with the privacy laws governing your web property and users.

[aws]: http://aws.amazon.com/
[pixel]: /snowplow/snowplow/raw/master/tracker/static/ice.png
[pixelbucket]: technical-documentation/images/02_log_bucket.png
[pixelselect]: technical-documentation/images/02_pixel_select.png
[pixelupload]: technical-documentation/images/02_pixel_upload.png
[pixelpermissions]: technical-documentation/images/02_pixel_permissions.png
[pixelready]: technical-documentation/images/02_pixel_ready.png
[distcreate]: technical-documentation/images/02_dist_create.png
[distdetails]: technical-documentation/images/02_dist_details.png
[distreview]: technical-documentation/images/02_dist_review.png
[distenabled]: technical-documentation/images/02_dist_enabled.png
[integrating]: technical-documentation/images/02_integrating_snowplowjs.md
[chooseregion]: technical-documentation/images/02_choose_region.png
[dataprivacy]: http://aws.amazon.com/s3/faqs/#Can_I_comply_with_EU_data_privacy_regulations_using_Amazon_S3
[git]: http://git-scm.com/
[yuic]: http://developer.yahoo.com/yui/compressor/