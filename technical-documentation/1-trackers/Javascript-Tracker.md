<a name="top" />
## Contents

1. [Overview](#overview)  
2. [Pageview tracking](#page)
3. [Event tracking](#events)  
4. [Ecommerce tracking](#ecommerce)  
5. [Social tracking](#social)
6. [Campaign tracking](#campaign)  

<a name="overview" />
### Overview

The [SnowPlow Javascript tracker](https://github.com/snowplow/snowplow/tree/master/1-trackers/javascript-tracker/) works in much the same way as Javascript trackers for other major web analytics solutions including Google Analytics and Omniture.

Tracking is done by inserting Javascript tags onto pages. These tags run functions defined in [snowplow.js](https://github.com/snowplow/snowplow/blob/master/1-trackers/javascript-tracker/js/snowplow.js), that trigger GET requests of the SnowPlow pixel. The Javascript functions append data points to be passed into SnowPlow onto the query string for the GET requests. These then get logged by the SnowPlow [collector](collectors). For a full list of data points that can be passed into SnowPlow in this way, please refer to the [SnowPlow tracker protocol](snowplow-tracker-protocol) documentation.

The Javascript tracker supports both syncronous and asyncronous tags. We recommend the asyncronous tags in nearly all instances, as these do not slow down page load times. Both methods are documented below.

<a name="page" />
### Pageview tracking

Pageview tracking is executed using the `trackPageView` function call. This is included in part of the core SnowPlow tag, added to the <head> section of the website(s) on which SnowPlow is implemented.

#### Asyncronous implementation

By inserting the following code into the <head> section of the each web page, views of those pages will be tracked in an 'async' manner:

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


Note: you will need to update the {{ACCOUNT}} field with an account ID provided by the SnowPlow team (if the SnowPlow team is hosting SnowPlow for you), or the self-generated account ID if you are [hosting the SnowPlow Cloudfront collector yourself](https://github.com/snowplow/snowplow/wiki/Integrating-SnowPlow-into-your-website#wiki-self-hosting).

#### Syndcronous implementation

By inserting the following code into the <head> section of each web page, views of those pages will be tracked in a 'sync' manner:

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

Note: you will need to update the {{ACCOUNT}} field with an account ID provided by the SnowPlow team (if the SnowPlow team is hosting SnowPlow for you), or the self-generated account ID if you are [hosting the SnowPlow Cloudfront collector yourself](https://github.com/snowplow/snowplow/wiki/Integrating-SnowPlow-into-your-website#wiki-self-hosting).

[Back to top](#top)

<a name="events" />
### Event tracking

#### Philosophy

The concept of event tracking is at the heart of SnowPlow. In the 'classical' model of web analytics, sensible analyses are agreed in advance, then formalised by being integrated into the site (e.g. by tracking goals and conversion funnels) and finally analysed. SnowPlow views this approach as 'premature analysis', and encourages logging plenty of intent-agnostic events and then figuring out what they mean later. In general, we believe that any on-page event that would not be captured by trackPageView (e.g. all AJAX events) are captured using the event tracking.

#### Examples of events that you might want to track

* Video / music plays
* Downloads
* Add-to-baskets
* Favoriting content

#### Anatomy of event tracking

5 key attributes can be associated with each event that is fired in SnowPlow. Of them, only the first two are required:

| **Name**    | **Required?** | **Description**                                                                          |
|------------:|:--------------|:-----------------------------------------------------------------------------------------|
|  `Category` | Yes           | The name you supply for the group of objects you want to track e.g. 'media', 'ecomm'     |
|    `Action` | Yes           | A string which defines the type of user interaction for the web object e.g. 'play-video', 'add-to-basket' |
|    `Object` | No            | An optional string which identifies the specific object being actioned e.g. ID of the video being played, or the SKU or the product added-to-basket |
|  `Property` | No            | An optional string describing the object or the action performed on it. This might be the quantity of an item added to basket |
|     `Value` | No            | An optional float to quantify or further describe the user action. This might be the price of an item added-to-basket, or the starting time of the video where play was just pressed |

#### Calling the trackEvent method: asyncronous implementation

The async specification for the `trackEvent` method is:

	_snaq.push(['trackEvent', 'category','action','object','property','value'])

An example of tracking a user listening to a music mix:

	_snaq.push(['trackEvent', 'Mixes', 'Play', 'MrC/fabric-0503-mix', , '0.0'])

Note that in the above example no value is set for the `event property`.

#### Calling the trackEvent method: syncronous implementation:

The sync specification for the `trackEvent` method is:

	trackEvent('category','action','object','property','value')

An example of tracking a user adding an item to basket:

	trackEvent('Checkout', 'Add', 'ASO01043', 'blue:xxl', '2.0');

