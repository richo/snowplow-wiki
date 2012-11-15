<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Trackers**](choosing-a-tracker) > [**Javascript tracker**](Javascript-tracker-setup)

This is divided into two sections:

1. [Setting up Google Tag Manager](#setup-gtm)
2. [Integrating SnowPlow Javascript tracking tags with Google Tag Manager](#snowplow-setup)

If you have already setup Google Tag Manager on your website, you can proceed directly to [section 2](#snowplow-setup). However, we recommend at least skimming the section on [setting up Google Tag Manager](#setup-gtm), as we've seen a number of companies implement this badly, with the end result that they cannot pass all the data they would like to SnowPlow tags for analysis later.

<a name="setup-gtm" />
## 1. Setting up Google Tag Manager

### Overview

There are six steps to setting up GTM to the point you can integrate SnowPlow (or any other web analytics tool) with GTM:

1. [Create a Google Tag Manager account](#1.1)
2. [Integrate the container tag on your website](#1.2)
3. [Work out what data to pass to Google Tag Manager from your website, using the `dataLayer`](#1.3)
4. [Work out how to structure the data passed into `dataLayer`](#1.4)
5. [Integrate the `dataLayer` onto your website](#1.5)
6. [Create Macros for the variables stored in the `dataLayer` in in the GTM UI](#1.6)

Typically, the steps people get wrong (or miss out alltogether) are steps 3-5. Getting them right is critical, however, because if you do not setup a robust mechanism for passing **all** the relevant data you want to report on in SnowPlow (or any other web analytics program) into GTM, then GTM will not be able to pass that data into SnowPlow, in turn. Designing and managing the data pipeline between your website and GTM is therefore critical.

<a name="1.1" />
### 1.1 Create a Google Tag Manager account

Creating a Google Tag Manager account is very simple. Log on to [[http://www.google.com/tagmanager/]] and select the **sign up now** button. Once you have signed up, elect to create a **New Account**. Give the account a name and a timezone. Then within it, create a **Container**. 

[[/setup-guide/images/gtm/1-create-account.png]]

In general, you'll only need to setup a single account, and most likely a single container for each domain you manage. However, that is not necessarily the case:

1. A container is a single GTM tag that you will install on your web pages instead of the individual tags from different web analytics and other vendors. If you use a common tag setup across many domains, then you can use the same container across multiple domains, rather than create a container for each domain. This might save administrative time in GTM, because you can then add and manage tags across all the domains in one go. (It is straightforward to add tags for a single domain within a multi-domain container setup, and to distinguish data collected from each domain, so there really is no disadvantage in using a single container across multiple domains.)
2. An account is a grouping of containers for administrative purposes. In general, one account is all you would need. If you work with different teams to manage different containers, then it makes sense to group the containers used by each team into a single account, with each team member being granted access to the account.

### 1.2 Integrating the container tag on your website

Once you have created a container, Google will provide you with the actual container tag. You will need to integrate this on every web page on your website(s), in place of any tags that are currently directly integrated on those web pages. (These will need to be migrated into GTM.)

[[/setup-guide/images/gtm/2-grab-embed-code.png]]

The embed code needs to be inserted on your web pages immediately after the opening `<body>` tag.

<a name="1.3" />
### 1.3 Work out what data to pass to Google Tag Manager from your website, using the `dataLayer`

The [`dataLayer`] [datalayer] is a JSON that contains name value pairs of data points you wish to pass from your website into GTM. (And GTM can then, in turn, pass on to any tags that are managed in GTM, including SnowPlow tags.)

Working out what data should be passed into the `dataLayer` is critical to ensuring that your GTM installation lasts. Getting it right, however, is not trivial. On the one hand, you need to be as comprehensive as possible: identifying every data point you might want to interrogate in your web analytics, and passing it in to avoid having to pester your web master to add it later, if you failed to include it in the initial GTM implementation.

On the other hand, we don't want to swamp GTM with lots of data that is never used: either becauese it is not passed onto any of the tags GTM manages, or because those points are never used as part of SnowPlow analyses.

In general, we recommend erring on the side of comprehensiveness: the cost of passing data into the `dataLayer` is small and SnowPlow is built to house as much data as you can throw at it. As a process, we recommend:

1. Start off with the different objects that make up your product or service _universe_. If you're a video site, like Youtube, than videos will be the main object that make up your universe. But users, comments, likes and channels are all other objects that make up the Youtube experience. (Each one of these will be represented in the CMS behind Youtube.)
2. For each different object, think about what the key data points are that are interesting. For example, a video on Youtube will have an `id`, a `name`, an `author` / `producer` etc.
3. Now consider what actions / events happen on a user journey. Each will typically involve the user interacting with one or more objects e.g. a video on the site. If the user likes a video, comments on a video or uploads a video, it will involve creating a new object.
4. We'd want each of those objects, with each of the associated data points, pushed to the `dataLayer`. It may not be necessary to push **all** the data points for each object to the `dataLayer`, because it may be possible to infer some from another. (For example, if we know the `id` of a video, we might be able to lookup the `author` from the CMS at a later stage.) But we want to pass enough data points in with each object to support any analysis, that we might want to perform, down the line, on that object.
5. The list of objects, data points, and events on the user journeys, should be documented. Those documents will be used in the next step, working out how to [structure the data in the `dataLayer`](#1.4)

<a name="1.4" />
### 1.4 Work out how to structure the data passed into `dataLayer`

Broadly speaking, there are two occasions we can pass data points into the `dataLayer`:

1. When the page loads
2. When an event occurs. (Which might not correspond to a page load because it is AJAX.) This might include _watching a video_, _adding an item to the shopping basket_, _submitting a purchase_, _logging in_.

In general, if the data point is directly related to the webpage being loaded, it makes sense to push that data point into the data layer at page load time. For example, if on the product pages on an online shop, it would make sense to push key product details (`name`, `sku`, `price`, `category`) with the page load, by inserting the following code in the page **before** the container tags:

```html
<body>
	<script type="text/javascript">
		dataLayer = [{
      		'productSku': 'pbz00123',
      		'productName': 'The Original Rider Waite Tarot cards',
      		'productPrice': '9.99'
    	}];
	</script>
	 <!-- Google Tag Manager -->
  	...
  	<!-- End Google Tag Manager -->
</body>
```

GTM might then pass these data points into SnowPlow as page level custom variables, for example.

We might equally want to record these data points on catalogue pages, where multiple products are listed. In this case, we could pass the three data points (`productSku`, `productName` and `productPrice`) in for each product as follows:

```html
<body>
	<script type="text/javascript">
		dataLayer = [{
      		'products': [
              {'productSku': 'pbz00123', 'productName': 'The Original Rider Waite Tarot cards', 'productPrice': '9.99' },
              {'productSku': 'pbz00124', 'productName': 'Aleicester Crowley Thoth Tarot', 'productPrice': '12.99' },
            ]
    	}];
	</script>
	 <!-- Google Tag Manager -->
  	...
  	<!-- End Google Tag Manager -->
</body>
```

We might use this data to perform an analysis of which product on catalogue pages are most likely to be clicked on, and whether that varies by user segment, for example. Analogous data points related to videos and listing the videos displayed on a selection page could be used by a Youtube-like site that wanted to analyse what drove users to select particular videos from a selection.

In many cases, however, we want to capture data related to specific events that occur on a user's journey, like playing a video, for example. In these cases, we can use `dataLayer.push` function to add new data points to the layer when an AJAX event like watching a video occurs. For example, we might trigger the following with a video play:

```javascript
dataLayer.push(
	'event': 'playVideo',
	'videoId': 'vid-000-123',
	'videoName': 'Skateboarding dog',
	'videoAuthor': 'user-00121'
	'videoFormat': 'hd'
);
```



[Back to top](#top)

<a name="snowplow-setup" />
## 2. Integrating SnowPlow Javascript tracking tags with Google Tag Manager

[Back to top](#top)

[datalayer]: https://developers.google.com/tag-manager/reference