<a name="top" />
## Contents

1. [Overview](#overview)  
2. [Pageview tracking](#page)
3. [Event tracking](#events)  
4. [Ecommerce tracking](#ecommerce)  
5. [Social tracking](#social)
6. [Campaign tracking](#campaign) 
7. [Error tracking](#error)
8. [Setting the application ID](#appid) 

<a name="overview" />
## 1. Overview

The [SnowPlow Javascript tracker](https://github.com/snowplow/snowplow/tree/master/1-trackers/javascript-tracker/) works in much the same way as Javascript trackers for other major web analytics solutions including Google Analytics and Omniture. We have tried, as far as possible, to keep the API very close to that used by Google Analytics, so that users who have implemented Google Analytics Javascript tags have no difficulty also implementing the SnowPlow Javascript tags.

Tracking is done by inserting Javascript tags onto pages. These tags run functions defined in [snowplow.js](https://github.com/snowplow/snowplow/blob/master/1-trackers/javascript-tracker/js/snowplow.js), that trigger GET requests of the SnowPlow pixel. The Javascript functions append data points to be passed into SnowPlow onto the query string for the GET requests. These then get logged by the SnowPlow [collector](collectors). For a full list of data points that can be passed into SnowPlow in this way, please refer to the [SnowPlow tracker protocol](snowplow-tracker-protocol) documentation.

The Javascript tracker supports both syncronous and asyncronous tags. We recommend the asyncronous tags in nearly all instances, as these do not slow down page load times. Both methods are documented below.

<a name="page" />
## 2. Pageview tracking

Pageview tracking is executed using the `trackPageView` function call. This is included in part of the core SnowPlow tag, added to the <head> section of the website(s) on which SnowPlow is implemented.

#### Asyncronous implementation

By inserting the following code into the `<head>` section of the each web page, views of those pages will be tracked in an 'async' manner:

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

Note: you will need to update the {{ACCOUNT}} field with an account ID provided by the SnowPlow team (if the SnowPlow team is hosting SnowPlow for you), or the self-generated account ID if you are hosting the SnowPlow Cloudfront collector yourself. Please refer to the [setup guide](https://github.com/snowplow/snowplow/wiki/Integrating-SnowPlow-into-your-website#wiki-self-hosting) for more details.

In the event that you want to manually trigger a Pageview (e.g. for an AJAX event that does not actually trigger a new page view), you can do so by calling the `trackPageView` function directly i.e.

```javascript
_snaq.push['trackPageView']
```

and bind the to the event you want to trigger it.

#### Syncronous implementation

By inserting the following code into the `<head>` section of each web page, views of those pages will be tracked in a 'sync' manner:

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

Note: you will need to update the {{ACCOUNT}} field with an account ID provided by the SnowPlow team (if the SnowPlow team is hosting SnowPlow for you), or the self-generated account ID if you are hosting the SnowPlow Cloudfront collector yourself. Please refer to the [setup guide](https://github.com/snowplow/snowplow/wiki/Integrating-SnowPlow-into-your-website#wiki-self-hosting) for more details.

[Back to top](#top)

<a name="events" />
## 3. Event tracking

#### Philosophy

The concept of event tracking is at the heart of SnowPlow. In the 'classical' model of web analytics, sensible analyses are agreed in advance, then formalised by being integrated into the site (e.g. by tracking goals and conversion funnels) and finally analysed. SnowPlow views this approach as out-dated: in the current era of cheap storage and growing computer power, we believe companies should log **all** identificable user events, and then figure out afterwards how to use that data to drive insight. We recommend that any on-page event that would not be captured by trackPageView (i.e. all AJAX events) are captured using Event tracking.

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

```javascript
_snaq.push(['trackEvent', 'category','action','object','property','value'])
```

An example of tracking a user listening to a music mix:

```javascript
_snaq.push(['trackEvent', 'Mixes', 'Play', 'MrC/fabric-0503-mix', , '0.0'])
```

Note that in the above example no value is set for the `event property`.

#### Calling the trackEvent method: syncronous implementation:

The sync specification for the `trackEvent` method is:

```javascript
trackEvent('category','action','object','property','value')
```

An example of tracking a user adding an item to basket:

```javascript
trackEvent('Checkout', 'Add', 'ASO01043', 'blue:xxl', '2.0');
```

<a name="ecommerce" />
## 4. Ecommerce tracking

Whilst it is possible to track all ecommerce events using SnowPlow's generic [event tracking](#events) (described [above](#events), it is also possible to track online transactions using dedicated Ecommerce Tracking, described here. Tracking purchase events using the Ecommerce Tracking is a 3 step process:

1. **Create a transaction object**. Use `addTrans()` method to initialize a transaction object. This will be the object that is loaded with all the data relevant to the specific transaction that is being tracked including all the items in the order, the prices of the items, the price of shipping and the `order_id`.
2. **Add items to the transaction.** Use the `addItem()` method to add data about each individual item to the transaction object.
3. **Submit the transaction to SnowPlow** using the trackTrans() method, once all the relevant data has been loaded into the object.

#### Anatomy of ecommerce tracking

**The addTrans() method**

| **Parameter**                  | **Required?** | **Example value** | 
|:-------------------------------|:--------------|:------------------|
| `order ID`                     | Yes           | '1234'            |
| `affiliation or store name`    | No            | 'Womens Apparel'  |
| `total spend`                  | Yes           | '19.99'           |
| `shipping cost`                | No            | '2.99'            |
| `city`                         | No            | 'San Jose'        | 
| `state or province`            | No            | 'California'      |
| `country`                      | No            | 'USA'             |

**The addItem() method**

| **Parameter**                  | **Required?**                                     | **Example value** |
|:-------------------------------|:--------------------------------------------------|:------------------|
| `order ID`                     | Yes (in order to associate item with transaction) | '1234'            |
| `SKU / product code`           | Yes                                               | 'pbz0001234'      |
| `product name`                 | No, but advisable (to make interpreting SKU easier) | 'Black Tarot'   |
| `category or variation`        | No                                                | 'Large'           |
| `unit price`                   | Yes                                               | '9.99'            |
| `quantity`                     | Yes                                               | '1'               |

**The trackTrans() method**

This method is called without any parameters. It pings the SnowPlow Collector with the data added to the transaction object created using the `addTrans()` and `addItem()` method calls above.

### Example ecommerce tracking code

#### Async example

Note how similar the SnowPlow Ecommerce tracking is to the [Google equivalent](https://developers.google.com/analytics/devguides/collection/gajs/gaTrackingEcommerce). This should make adding SnowPlow tracking alongside Google Analytics tracking very straightforward.

```html
<html>
<head>
<title>Receipt for your clothing purchase from Acme Clothing</title>
<script type="text/javascript">

  var _snaq = _snaq || [];
  _snaq.push(['setAccount', 'UA-XXXXX-X']);
  _snaq.push(['trackPageview']);
  _snaq.push(['addTrans',
    '1234',           // order ID - required
    'Acme Clothing',  // affiliation or store name
    '11.99',          // total - required
    '1.29',           // tax
    '5',              // shipping
    'San Jose',       // city
    'California',     // state or province
    'USA'             // country
  ]);

   // add item might be called for every item in the shopping cart
   // where your ecommerce engine loops through each item in the cart and
   // prints out _addItem for each
  _snaq.push(['addItem',
    '1234',           // order ID - required
    'DD44',           // SKU/code - required
    'T-Shirt',        // product name
    'Green Medium',   // category or variation
    '11.99',          // unit price - required
    '1'               // quantity - required
  ]);

  // trackTrans sends the transaction to SnowPlow tracking servers.
  // Must be called last to commit the transaction.
  _snaq.push(['trackTrans']); //submits transaction to the Analytics servers

  (function() {
    var sp = document.createElement('script'); sp.type = 'text/javascript'; sp.async = true; sp.defer = true;
    sp.src = ('https:' == document.location.protocol ? 'https' : 'http') + '://d1fc8wv8zag5ca.cloudfront.net/sp.js';
    var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(sp, s);
  })();

</script>
</head>
<body>

  Thank you for your order.  You will receive an email containing all your order details.

</body>
</html>
```


#### Sync example

Again, note how similar the SnowPlow Ecommerce tracking is to the [Google equivalent](https://developers.google.com/analytics/devguides/collection/gajs/gaTrackingEcommerce). This should make adding SnowPlow tracking alongside Google Analytics tracking very straightforward.

```html
<html>
<head>
<title>Receipt for your clothing purchase from Acme Clothing</title>
</head>

<body>

  Thank you for your order.  You will receive an email containing all your order details.


<script type="text/javascript">
  var spSrc = ('https:' == document.location.protocol ? 'https' : 'http') + '://d1fc8wv8zag5ca.cloudfront.net/sp.js';
  document.write(unescape("%3Cscript src='" + spSrc + "' type='text/javascript'%3E%3C/script%3E"));
</script>
<script type="text/javascript">
try{
  var snowplowTracker = SnowPlow.getTracker('{{ACCOUNT}}');
  snowplowTracker.trackPageView();
  snowplowTracker.addTrans(
      "1234",            // order ID - required
      "Womens Apparel",  // affiliation or store name
      "11.99",           // total - required
      "1.29",            // tax
      "15.00",           // shipping
      "San Jose",        // city
      "California",      // state or province
      "USA"              // country
    );


   // add item might be called for every item in the shopping cart
   // where your ecommerce engine loops through each item in the cart and
   // prints out _addItem for each 
   snowplowTracker.addItem(
      "1234",           // order ID - necessary to associate item with transaction
      "DD44",           // SKU/code - required
      "T-Shirt",        // product name
      "Olive Medium",   // category or variation
      "11.99",          // unit price - required
      "1"               // quantity - required
   );

   snowplowTracker.trackTrans(); //submits transaction to the Analytics servers
} catch(err) {}
</script>
</body>
</html>
```

<a name="social" />
## 5. Social tracking

**THIS FUNCTIONALITY IS STILL IN DEVELOPMENT**

Social tracking can be used to track users interacting with Facebook, Twitter and Google+ buttons and widgets.

#### Anatomy of the `trackSocial` method

Social tracking uses the `trackSocial` method, which takes four parameters: 

| **Parameter** | **Description** | **Required?** | **Example value**     | 
|:--------------|:----------------|:--------------|:----------------------|
| `network`     | Social network  | Yes           | 'facebook', 'twitter' |
| `socialAction`| Social action performed | Yes           | 'like', 'retweet'     |
| `opt_target`  | Object social action is performed on e.g. page ID, product ID | No            | '19.99'               |
| `opt_pagePath`| Page path of URL on which social action was carried out | No            | '2.99'                |

#### `trackSocial` syntax

The method is executed in an async manner as:

```javascript
_snaq.push('trackSocial', network, socialAction, opt_target, opt_pagePath)
```

For example:

```javascript
_snaq.push('trackSocial', 'facebook', 'like', 'pbz00123', '/products/tarot/123-original-rider-waite')
```

Or if the optional parameters were left off:

```javascript
_snaq.push('trackSocial', 'facebook', 'like', '', '')
```

And in a sync manner as:

```javascript
trackSocial(network, socialAction, opt_target, opt_pagePath)
```

For example:

```javascript
trackSocial('facebook', 'like', 'pbz00123', '/products/tarot/123-original-rider-waite')
```

Or if the optional parameters were left off:

```javascript
trackSocial('facebook', 'like', '', '')
```

#### Calling the `trackSocial` method

The widgets provided by each social network (Google+, Facebook, Twitter) each have their own set of Javascript events associated with different user actions. Therefore, the details of how to implement Social tracking vary for each of them. In all cases, however, the key is to trigger the call to `trackSocial` when the specific social action (e.g. 'like' or 'retweet') has been completed.

#### Tracking Facebook actions

Social actions in Facebook can be tracked by subscribing to Facebook's `edge.create` events, that fire when a social action (e.g. a _like_ or a _share_) have been completed.

**Recording _likes_**

```javascript
FB.Event.subscribe('edge.create', function(targetUrl) {
  _snaq.push(['_trackSocial', 'facebook', 'like', targetUrl]);
});
```

**Recording _unlikes_**

```javascript
FB.Event.subscribe('edge.remove', function(targetUrl) {
  _snaq.push(['_trackSocial', 'facebook', 'unlike', targetUrl]);
});
```

**Recording _shares_**

```javascript
FB.Event.subscribe('message.send', function(targetUrl) {
  _snaq.push(['_trackSocial', 'facebook', 'send', targetUrl]);
});
```

#### Tracking Twitter tweets

When you insert a Twitter button onto your website, the Twitter button code creates a `twttr` object:

```html
<a href="https://twitter.com/share" class="twitter-share-button" data-lang="en">Tweet</a>
  <script type="text/javascript" charset="utf-8">
    window.twttr = (function (d,s,id) {
      var t, js, fjs = d.getElementsByTagName(s)[0];
      if (d.getElementById(id)) return; js=d.createElement(s); js.id=id;
      js.src="//platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs);
      return window.twttr || (t = { _e: [], ready: function(f){ t._e.push(f) } });
    }(document, "script", "twitter-wjs"));
  </script>
```

To track Tweet Button events, you need to create a function that calls the `trackSocial` method with the relevant parameters, and bind it to the `twttr` object as an event binding:

```javascript
function trackTwitter(intent_event) {
    if (intent_event) {
      var opt_pagePath;
      if (intent_event.target && intent_event.target.nodeName == 'IFRAME') {
            opt_target = extractParamFromUri(intent_event.target.src, 'url');
      }
      _snaq.push(['_trackSocial', 'twitter', 'tweet', opt_pagePath]);
    }
  }

  //Wrap event bindings - Wait for async js to load
  twttr.ready(function (twttr) {
    //event bindings
    twttr.events.bind('tweet', trackTwitter);
  });
```

#### Gracking Google +1s

TO WRITE


<a name="campaign" />
## 6. Campaign tracking

#### Overview of the process by which traffic sources are ascribed to visits

Campaign tracking is used to identify the source of traffic coming to a website.

At the highest level, we can distinguish **paid** traffic (that derives from ad spend) with **non paid** traffic: visitors who come to the website by entering the URL directly, clicking on a link from a referrer site or clicking on an organic link returned in a search results, for example.

In order to identify **paid** traffic, SnowPlow users need to set five query parameters on the links used in ads. SnowPlow checks for the presence of these query parameters on the web pages that users load: if it finds them, it knows that that user came from a paid source, and stores the values of those parameters so that it is possible to identify the paid source of traffic exactly.

If the query parameters are not present, SnowPlow reasons that the user is from a **non paid** source of traffic. It then checks the page referrer (the url of the web page the user was on before visiting our website), and uses that to deduce the source of traffic:

1. If the URL is identified as a search engine, the traffic medium is set to "organic" and SnowPlow tries to derive the search engine name from the referrer URL domain and the keywords from the  query string.
2. If the URL is a non-search 3rd party website, the medium is set to "referrer". SnowPlow derives the source from the referrer URL domain.

#### Identifying paid sources

Your different ad campaigns (PPC campaigns, display ads, email marketing messages, Facebook campaigns etc.) will include one or more links to your website e.g.:

'''html
<a href="http://mysite.com/myproduct.html">Visit website</a>
'''

We want to be able to identify people who've clicked on ads e.g. in a marketing email as having come to the site having clicked on a link in that particular marketing email. To do that, we modify the link in the marketing email with query parameters, like so:

```html
<a href="http://mysite.com/myproduct.html?utm_source=newsletter-october&utm_medium=email&utm_campaign=cn0201">Visit website</a>
```

For the prospective customer clicking on the link, adding the query parameters does not change the user experience. (The user is still directed to the webpage at http://mysite.com/myproduct.html.) But SnowPlow then has access to the fields given in the query string, and uses them to identify this user as originating from the October Newsletter, an email marketing campaign with campaign id = cn0201.

#### Anatomy of the query parameters

SnowPlow uses the same query parameters used by Google Analytics. Because of this, SnowPlow users who are also using GA do not need to do any additional work to make their campaigns trackable in SnowPlow as well as GA. Those parameters are:

| **Parameter**        | **Name**              | **Description**                                     |
|:--------------------:|:---------------------:|:---------------------------------------------------:|
| `utm_source`         | Campaign source       | Identify the advertiser driving traffic to your site e.g. Google, Facebook, autumn-newsletter etc.  |
| `utm_medium`         | Campaign medium       | The advertising / marketing medium e.g. cpc, banner, email newsletter, in-app ad, cpa |
| `utm_campaign`       | Campaign id           | A unique campaign id. This can be a descriptive name or a number / string that is then looked up against a campaign table as part of the analysis |
| `utm_term  `         | Campaign term(s)      | Used for search marketing in particular, this field is used to identify the search terms that triggered the ad being displayed in the search results. |
| `utm_content`        | Campaign content      | Used either to differentiate similar content or two links in the same ad. (So that it is possible to identify which is generating more traffic.) |

The parameters are descibed in the [Google Analytics help page] [gahelppage]. Google also provides a [urlbuilder] [gaurlbuilder] which can be used to construct the URL incl. query parameters to use in your campaigns.

<a name="error" />
## 7. Error tracking 

COMING SOON

## 8. Setting the application ID

The application ID field (aka `app_id`) is useful for SnowPlow uses distinguishing between data from different digital applications, be they websites, mobile apps or other types of software application with SnowPlow tracking installed.

To set the `app_id`, use the `setSiteId` function. This takes only one argument: the application ID you want to set. The async implementation is:

```javascript
_snaq.push(['setSiteId', 'my_application_id_here']);
```

The sync implementation is simply:

```javascript
setSiteId('my_application_id')
```