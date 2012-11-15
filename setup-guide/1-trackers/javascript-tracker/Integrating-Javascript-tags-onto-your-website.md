[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Trackers**](choosing-a-tracker) > [**Javascript tracker**](Javascript-tracker-setup)

## Overview

This guide takes you through the process for integrating SnowPlow's JavaScript tracker into your website or web app.

## Before you get started...

### Setup your collector before setting up the Javascript tracker

Before you start integrating the Javascript tracking tags onto your website, we recommend that you complete the [collector setup](choosing-a-collector). This is important as it determines what information you provide to the `setAccount` Javascript function below. (If you are using the [Cloudfront collector](setting-up-the-cloudfront-collector), the {{ACCOUNT}} entered is the Cloudfront subdomain you setup to serve the tracking pixel `ice.png`.)

### Decide how you want to implement the Javascript tracker

The exact integration steps required vary depending on whether:

1. You integrate SnowPlow tracking tags directly into your website, or through a tag management solution (like [Google Tag Manager] [gtm]). Our recommended approach is to use a Tag Manager, and implement this as part of a SnowPlow integration, if you have not already done so.
2. Whether or not you use the Javascript tracker in a asyncronous or syncronous manner.

The following guide covers the process for integrating SnowPlow tracking tags directly onto your website. Since Google released [Google Tag Manager] [gtm], a free tag management tool, our recommendation is that companies who wish to integrate SnowPlow, and do not currently have a tag management solution installed, implement [Google Tag Manager] [gtm] at the same time as SnowPlow is implemented. A [separate guide](integrating-javascript-tags-with-gtm) is provided to walk you through the process of setting up Google Tag Manager and then setting up SnowPlow in it, and should be read as an alternative to the following instructions for anyone who uses, or wishes to use, Google Tag Manager.

## Table of Contents

1. [Asynchronous integration](#async)
2. [Synchronous integration](#sync)
3. [Tracking more than Pageviews. (Event tracking, ecommerce tracking, social tracking...)](#events)
4. [Testing](#testing)

<a name="async"/>
## Asynchronous integration

### Installing the header script

To use `snowplow.js` in an 'async' manner, first add the following script into your website template's `<head>` section:

```html
<!-- SnowPlow starts plowing -->
<script type="text/javascript">
var _snaq = _snaq || [];

_snaq.push(['setAccount', '{{ACCOUNT}}']);
_snaq.push(['trackPageView']);
_snaq.push(['enableLinkTracking']);

(function() {
var sp = document.createElement('script'); sp.type = 'text/javascript'; sp.async = true; sp.defer = true;
sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://d1fc8wv8zag5ca.cloudfront.net/sp.js';
var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(sp, s);
})();
 </script>
<!-- SnowPlow stops plowing -->
```

**Setting the {{ACCOUNT}} value** 

You must update `{{ACCOUNT}}` with the Cloudfront subdomain details you created as part of the [collector setup](setting-up-cloudfront-collector). (If you are using a version of SnowPlow hosted by the SnowPlow team, we will provide you with an account ID to enter.)  It will look something like `d3rkrsqld9gmqf`

If your CloudFront distribution's URL is `http://d1x5tduoxffdr7.cloudfront.net`, then update the appropriate line in your header script to look like this:

```javascript
_snaq.push(['setAccount', 'd1x5tduoxffdr7']);
```

**Updating the reference to `sp.js`**

The reference to `://d1fc8wv8zag5ca.cloudfront.net/sp.js'` loads `sp.js`, the SnowPlow Javascript tracker. The version loaded is the version [hosted by the SnowPlow team from our own Cloudfront subdomain](hosted-assets) (and provided free to the community). 

If you are hosting your own SnowPlow JavaScript file (see the guide to [[Self-hosting snowplow.js|Self hosting snowplow js]]), then you need to update the tag above, swapping your own Cloudfront `{{SUBDOMAIN}}` (the one from which you serve `sp.js` in for ours:

```javascript
sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://{{SUBDOMAIN}}.cloudfront.net/sp.js';
```

**Understanding the tracking code**

To explain a few things about this async tracking code:

* This code works with both HTTPS (i.e. SSL-secured) and HTTP pages
* The `trackPageView` command logs the page load 
* The `enableLinkTracking` command ensures SnowPlow enables clicks and download tracking. Currently this has not been implemented

### Integrating event tracking

Having set up the asynchronous tracking above, you can now add SnowPlow's asynchronous event tracking into your website or webapp.

Here is an example of SnowPlow event tracking attached to a simple JavaScript action:

```html
<!-- Website event with SnowPlow tracking -->
<script type="text/javascript">
    function playVideo(){
        alert("Playing a video")
        _snaq.push(['trackEvent', 'Videos', 'Play', 'Fargo', 'French-subtitles','320x200'])
    }
</script>
```

The five arguments to the event tracking command may be broadly familiar to you from Google Analytics - however there are some small differences, so please refer to the section [tracking more than page views](#events) below for more information.

Any problems? Please consult the [Testing and troubleshooting](#tt) section at the bottom of this guide.

<a name="sync"/>
## Synchronous integration

### Installing the header script

To use `snowplow.js` in a 'sync' manner, first add the following script into your website template's `<head>` section:

```html
<!-- SnowPlow starts plowing -->
<script type="text/javascript">
var spSrc = ('https:' == document.location.protocol ? 'https' : 'http') + '://d1fc8wv8zag5ca.cloudfront.net/sp.js';
document.write(unescape("%3Cscript src='" + spSrc + "' type='text/javascript'%3E%3C/script%3E"));
</script>
<script type="text/javascript">
try {
var snowplowTracker = SnowPlow.getTracker('{{ACCOUNT}}');
snowplowTracker.trackPageView();
snowplowTracker.enableLinkTracking();
} catch ( err ) {}
</script>
<!-- SnowPlow stops plowing -->
```

**Setting the {{ACCOUNT}} value** 

You must update `{{ACCOUNT}}` with the Cloudfront distribution details you created as part of the [collector setup](setting-up-cloudfront-collector). (If you are using a version of SnowPlow hosted by the SnowPlow team, we will provide you wiht an account ID to enter.)  It will look something like `d3rkrsqld9gmqf`

If your CloudFront distribution's URL is `http://d1x5tduoxffdr7.cloudfront.net`, then update the appropriate line in your header script to look like this:

```javascript
var snowplowTracker = SnowPlow.getTracker('d1x5tduoxffdr7');
```

**Updating the reference to `sp.js`**

The reference to `://d1fc8wv8zag5ca.cloudfront.net/sp.js'` loads `sp.js`, the SnowPlow Javascript tracker. The version loaded is the version [hosted by the SnowPlow team](hosted-assets) (and provided free to the community). 

If you are hosting your own SnowPlow JavaScript file (see the guide to [[Self-hosting snowplow.js|Self hosting snowplow js]]), then you need to update the tag above, swapping your own Cloudfront `{{SUBDOMAIN}}` (the one from which you serve `sp.js` in for ours:

```javascript
var spSrc = ('https:' == document.location.protocol ? 'https' : 'http') + '://{{SUBDOMAIN}}.cloudfront.net/sp.js';
```

**Understanding the tracking code**

To explain a few things about this synchronous tracking code:

* This code works with both HTTPS (i.e. SSL-secured) and HTTP pages
* The `trackPageView` command logs the page load 
* The `enableLinkTracking` command ensures SnowPlow enables clicks and download tracking. If this is not required, it can be removed or commented out

### Integrating event tracking

Having set up the synchronous tracking above, you can now add SnowPlow's synchronous event tracking into your website or webapp.

Here is an example of SnowPlow event tracking attached to a simple JavaScript action:

```html
<!-- Website event with SnowPlow tracking -->
<script type="text/javascript">
    function playVideo(){
        alert("Playing a video")
        snowplowTracker.trackEvent('Videos', 'Play', 'Fargo', 'French-subtitles', '320x200')
    }
</script>
```
The five arguments to the event tracking command may be broadly familiar to you from Google Analytics - however there are some small differences, so please refer to the section [tracking more than page views](#events) below for more information.

Any problems? Please consult the [Testing and troubleshooting](#tt) section at the bottom of this guide.

<a name="events"/>
## Tracking more than Pageviews. (Event tracking, ecommerce tracking, social tracking, campaign tracking.)

The SnowPlow Javascript tracker supports a rich set of tracking functions including [event tracking](javascript-tracker#wiki-events), [ecommerce tracking](javascript-tracker#wiki-ecommerce) and [social tracking](javascript-tracker#wiki-social). For details of how to implement the above, refer to the [[Javascript tracker]] section of the [Technical Documentation](snowplow-technical-documentation).

<a name="testing"/>
## Testing

_This section is common to both the synchronous and asynchronous integration approaches._

### Checking snowplow.js is working

You can 'kick the tyres' of `snowplow.js` with the example HTML pages available in [https://github.com/snowplow/snowplow/tree/master/1-trackers/javascript-tracker/examples/web](https://github.com/snowplow/snowplow/tree/master/1-trackers/javascript-tracker/examples/web) 

    snowplow/tracker/examples

Before running these HTML pages, you will make sure to update the `{{ACCOUNT}}` to your SnowPlow-supplied account ID; if you are using a self-hosted `snowplow.js`, then update that in the HTML files too.

We recommend using Chrome's [Developer Tools] [chrome-dev-tools] or [Firebug] [firebug] for Firefox to check that SnowPlow's JavaScript tracking is working correctly. Here is what Chrome's Network panel looks like after loading the page and clicking each button once:

![network-pane] [network-pane]

Note the three successful (status code 200) `GET`s to `ice.png`. The first of these was triggered by the page load, and then there is one `GET` each for the two user actions (button clicks) that we're tracking. 
If you have any problems getting this to run, please [contact] [contact] the **SnowPlow Analytics** team.

### Confirming that SnowPlow is logging correctly

If you are using the SnowPlow-hosted version of SnowPlow, then please [contact] [contact] the **SnowPlow Analytics** team to confirm that your event data is being successfully logged.

If you are self-hosting SnowPlow, then please proceed to the next section [[Setting up EMR]] to start testing (and working with) the SnowPlow data.

### Marketing campaign tracking

If you want SnowPlow to track inbound marketing campaigns you are running then [[learn how to do campaign tracking using SnowPlow|Tracking your marketing campaigns]].  

[ga-event-guide]: http://code.google.com/apis/analytics/docs/tracking/eventTrackerGuide.html
[chrome-dev-tools]: http://code.google.com/chrome/devtools/docs/overview.html
[firebug]: http://getfirebug.com/
[network-pane]: setup-guide/images/01_network_pane.png
[contact]: mailto:services@snowplowanalytics.com
[gtm]: http://www.google.com/tagmanager/