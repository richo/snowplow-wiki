<a name="top" />

1. [Overview](#overview)
2. [Sending common event types](#common)
3. [Complete list of field names and parameters](#allparams)


<a name="overview" />
## 1. Overview

SnowPlow trackers fire _events_, which are `GET` or `POST` requests of a [SnowPlow collector](collectors), whenever an event on a website or application takes place. By appending parameters and values to the end of those `GET` or `POST` requests, trackers can pass data into the collectors, for processing by SnowPlow. 

The SnowPlow Tracker Protocol is the list of all the parameters that SnowPlow trackers use when firing events to push data into the [SnowPlow collectors] (collectors). Each parameter maps onto one or more fields in the [SnowPlow events table] (canonical-data-structure) employed in storage. Here we document which field in the [SnowPlow events table] (canonical-data-structure) each parameter added to the query string maps onto. 

SnowPlow has been architected to be as easy as possible for developers to create their own alternative subsystems. This documentation should be used by anyone who would like to build their own tracker: by utilising the parameters documented here, the author of a new tracker can be confident that his / her tracker will work with the rest of the SnowPlow stack, and be confident where the values associated with each parameter on every call will be available to query in SnowPlow, whether that's in Hive or Infobright or another database.

[Back to top](#top)

<a name="common" />
## 2. Sending common event types

1. [Common parameters used in (nearly) all tracking](#common-params)
2. [Pageview tracking](#pageview)
3. [Custom event tracking](#event)
4. [Ecomm tracking](#ecomm)
5. [Social tracking](#social)
6. [Error tracking](#error)

<a name="common-params" />
### 2.1. Common parameters

| **Parameter** | **Name**         | **Description**               | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:--------------------------|
| `uid`         | `user_id`        | Unique identifier for user    | `aeb1691c5a0ee5a6`        |
| `vid`         | `visit_id`       | Visit / session identifier for this user e.g. `1` is first visit | `1`, `2`...|
| `tid`         | `txn_id`         | Transaction ID: Unique identifier for this specific event | `508780` |
| `e`           | `event`          | The type of event             | `pv`, `d`, `s`,  `e`,`c` (see [reference](#event2)) |
| `aid`         | `app_id`         | Unique identifier for website / app | `1`, `company-site`, `angry-birds-android` |
| `tstamp`      | `timestamp`      | Date / time when the event being logged took place. This is not useful for web tracking (when the events are `GET` requests are made by the Javascript tracker in realtime), in useful in mobile application tracking, where a batch of requests may be made after the events being tracked have already occured, to optimize connectivity |
| `tv`          | `tracker_version`| Tracker ID incl. version number. Should make it clear from which tracker the data was generated | `js-0.5.2`,  `iOS-0.0.3` |

Back to [common event types](#common)

<a name="pageview" />
### 2.2. Pageview tracking

```javascript
uid=aeb1691c5a0ee5a6    // User ID
&vid=2                  // Visit ID (i.e. session number for this user_id)
&tid=508780             // Transaction ID
&aid=1                  // App ID
&tv=js-0.5.2            // Tracker version

&e=pv                   // event = page view
&url=http%3A%2F%2Ftest.psybazaar.com%2F2-tarot-cards    // Page URL
&page=Tarot%20cards                                     // Page title
&refr=http%3A%2F%2Ftest.psybazaar.com%2F                // Referrer URL
```

Back to [common event types](#common)

<a name="event" />
### 2.3. Custom event tracking

_Add-to-basket_ example:

```javascript
uid=aeb1691c5a0ee5a6    // User ID  
&vid=2                  // Visit ID (i.e. session number for this user_id)  
&tid=508780             // Transaction ID  
&aid=1                  // App ID
&tv=js-0.5.2            // Tracker version

&e=c                    // event = custom  
&ev_ca=ecomm            // event_category = ecomm  
&ev_ac=add-to-basket    // event_action = add-to-basket  
&ev_la=178              // event_label = 178 (product_id of item added to basket)  
&ev_pr=1                // event_property = 1 (quantity of item added to basket)  
&ev_va=14.99            // event_value = 14.99 (price of item added to basket)  

```

_Watch-video-clip_ example:

```javascript
uid=aeb1691c5a0ee5a6    // User ID  
&vid=2                  // Visit ID (i.e. session number for this user_id)  
&tid=508780             // Transaction ID  
&aid=1                  // App ID
&tv=js-0.5.2            // Tracker version

&e=c                    // event = custom  
&ev_ca=video            // event_category = video  
&ev_ac=play             // event_action = play  
&ev_la=291              // event_label = 291 (video_id of video played)  
&ev_pr=13.2             // event_property = 13.2 (number of seconds into video that clip starts playing)  
&ev_va=0.0025           // event_value = 0.0025 (ad revenue associated with view)  

```


Back to [common event types](#common)

<a name="ecomm" />
### 2.4. Ecommerce tracking 

To track an ecommerce transaction, fire a `transaction` event to register the transaction, and then fire `item` events to log specific data about the items that were part of that transaction. the `order_id`, (captured using the `ti` parameter) is used to link the transaction-level and item-level data at analysis time.

Transaction event example:

```javascript
uid=aeb1691c5a0ee5a6    // User ID  
&vid=2                  // Visit ID (i.e. session number for this user_id)  
&tid=508780             // Transaction ID  
&aid=1                  // App ID
&tv=js-0.5.2            // Tracker version

&e=ec            // Ecomm event type.
&ti=12345        // Order ID
&ta=westernWear  // Transaction affiliation
&tr=50.00        // Transaction revenue
&ts=32.00        // Transaction shipping
&tt=12.00        // Transaction tax
```

Item hit example:

```javascript
uid=aeb1691c5a0ee5a6    // User ID  
&vid=2                  // Visit ID (i.e. session number for this user_id)  
&tid=508780             // Transaction ID  
&aid=1                  // App ID
&tv=js-0.5.2            // Tracker version

&e=ei            // Ecomm item event type
&ti=12345        // Order ID
&ip=300          // Item price
&iq=2            // Item quantity
&ic=u3eqds43     // Item code / SKU
&ip=300          // Item price
&in=sofa         // Item name
&ic=furniture    // Item category
```

Back to [common event types](#common)

<a name="social" />
### 2.5. Social tracking

**Note!** This has not been implemented yet. (On the ETL and storage side.)

```javascript
uid=aeb1691c5a0ee5a6    // User ID  
&vid=2                  // Visit ID (i.e. session number for this user_id)  
&tid=508780             // Transaction ID  
&aid=1                  // App ID
&tv=js-0.5.2            // Tracker version

&e=s            // Social event type
&sa=like        // Social Action
&sn=facebook    // Social Network
&st=/home       // Social Target
```

TO WRITE

Back to [common event types](#common)

<a name="error" />
### 2.6 Error tracking

TO WRITE

Back to [common event types](#common)

[Back to top](#top)

<a name="allparams" />
## 3. Complete list of field names and parameters

1. [Application parameters](#appid)  
2. [Date / time parameter](#timestamp)  
3. [Event / transaction parameters](#event2)  
4. [SnowPlow tracker version](#version)  
5. [User related parameters](#user)  
6. [Page level parameters](#page)  
7. [Custom event tracking parameters](#events3)
8. [Social parameters](#social2)
9. [Ecommerce parameters](#ecomm3)

<a name="appid" />
### 3.1 Application parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `aid`         | `app_id`         | Unique identifier for website / application    | Yes | `angry-birds-android` |
| `pl`          | `platform`       | The platform the app runs on  | No               | `ios`, `web`, `win-8`     |

The application ID parameter is used to distinguish data from different website and applications.

As a SnowPlow user, you can define application IDs for each of your different ditial products and track behaviour of your users across all of them using the same SnowPlow instance by setting the `app_id` in your tracker of choice.

**Potential platform values**: (to finalise and complete...)

| **Platform**                 | **`pl` value** |
|:-----------------------------|:---------------|
| Web                          | `w`            | 
| iOS                          | `iOS`          |
| Android                      | `a`            |
| Windows                      | `win`          |
| Blackberry                   | `b`            |
| ...                          |                |

Back to [complete list of parameters](#allparams).

<a name="timestamp" />
### 3.2 Date / time parameter

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `tstamp`      | `dt` and `tm`    | Timestamp when event occurred | Yes              |                           |

For some trackers e.g. the [Javascript tracker](javascript-tracker), tracking happens in real-time: so when an event occurs on a website being tracked, a SnowPlow event is fired almost simultaneously to record the event. For this type of setup, there is no need to explicitly track the timestamp for the event, as this can be inferred from the timestamp recorded on the collector log. (I.e. when the event fired was received by the collector.)

For some trackers e.g. those that work on mobile platforms, it may not be optimal to fire tracking events as the events occur in real time: it may make more sense to store them locally and fire them the there is bandwidth available, for example. In these cases we cannot rely on the collector for the timestamp data because the collector may only receive the data some time after the event occured. In these situations we need to store the timestamp for each event when it occurs, and communicate that data to the collector using the `tstamp` parameter.

Back to [complete list of parameters](#allparams).

<a name="event2" />
### 3.3 Event / transaction parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `e`           | `event`          | Event type                    | No               | (See table below)         |
| `tid`         | `txn_id`         | Unique transaction / event ID | Yes              | 508780                    |

**Potential `event` values**

| **Event type**            | **`e` value** |
|:--------------------------|:--------------|
| Pageview                  | `pv`          |
| Download                  | `d`           |
| Social                    | `s`           |
| Ecommerce                 | `ec`          |
| Ecommerce item            | `eci`         |
| Error                     | `er`          |


Back to [complete list of parameters](#allparams).

<a name="version" />
### 3.4 SnowPlow Tracker Version

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `tv`          | `v_tracker`      | Identifier for SnowPlow tracker | No             | `js-0.5.1`                |

For deployments where multiple trackers are used (e.g. to track user behaviour across many platforms), it is useful to be able to distinguish data generated from each tracker. It can also be useful when tracker versions are updated, so that it is easier to see if an update in tracker accounts for a feature of the data at analysis time.

Back to [complete list of parameters](#allparams).


<a name="user" />
### 3.5 User related parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `uid`         | `user_id`        | Unique identifier for user    | Yes              | `aeb1691c5a0ee5a6`        |
| `vid`         | `visit_id`       | Visit / session identifier for this user e.g. `1` is first visit | Yes       | `1`, `2`...|
| `ctype`       | `connection_type`| Type of connection            | No               |                           |

We recommend **always** setting the `uid` / `user_id` parameter: as this is the cornerstone of all customer-centric analytics.

In contrast, setting `vid` / `visit_id` is optional. It is possible to define when sessions begin and end client-side, in the tracker. But it is equally possible to define session start and stop times at the ETL or analytics phase, in which case it need not be set in the tracker at all. Note: Google Analytics defines sessions server side.

Back to [complete list of parameters](#allparams).


<a name="page" />
### 3.6 Page-level parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `url`         | `page_url`       | Page URL                      | Yes              | `http%3A%2F%2Ftest.psybazaar.com%2F2-tarot-cards` |
| `page`        | `page_title`     | Page title                    | Yes              | `Tarot%20cards  `         |
| `refr`        | `page_referrer`  | Unique identifier for user    | Yes              | `http%3A%2F%2Ftest.psybazaar.com%2F` |

For website tracking, page-level parameters are essential for pageview events. However, they may also be set on other events (e.g. add-to-baskets, or social interactions) so that an analyst can explore to what extent those actions are more prevalant on some pages than others.

These fields do not make sense in a mobile application or other non-web environment.

Back to [complete list of parameters](#allparams).

<a name="events3" />
### 3.7 Custom event tracking parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `ev_ca`       | `ev_category`    | Category of event             | Yes              | `ecomm`, `media`          |
| `ev_ac`       | `ev_action`      | The action performed          | Yes              | `add-to-basket`, `play-video` |
| `ev_la`       | `ev_label`       | A label associated with the event. Often the object (e.g. `product_id`, `video_id` acted on | Yes | `pbz00123` |
| `ev_pr`       | `ev_property`    | A property value associated with the action / event e.g. quantity of an item added to basket | `1`, `large` |
| `ev_va`       | `ev_value`       | A value associatd with the action / event. This may be the monetary value associated with it e.g. the value of the item added to basket | '12.99' |

Custom event tracking is at the heart of the SnowPlow approach to 'track everything'. We recommend tracking all events that are not tracked as part of pageviews as custom events. For people using SnowPlow to track web behaviour, that means all AJAX events. 

Our hope is that the above fields are enough in most cases to capture all the relevant data points associated with a specific event. In the event that they are not, we plan to extend SnowPlow shortly to include 10 custom variables that can be associated with specific events, and an 11th that can be stuffed with an JSON, if there is a need to pass more structured data into SnowPlow for the events that the 5 fields above and the 10 custom variables can hold.

Back to [complete list of parameters](#allparams).

<a name="social2" />
### 3.8 Social parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `sa`          | `social_action`  | Social action performed       | No               | `like`, `tweet`           |
| `sn`          | `social_network` | Social network involved       | No               | `facebook`, `twitter`     |
| `st`          | `social_target`  | Social action target e.g. object _liked_, article _tweeted_ | No | `like`, `tweet` |
| `sp`          | `social_pagepath`| Page path action was performed on | No           |         |

Note: these have not been implemented yet. However, the planned implementation is closely modelled on the Google Analytics Measurement Protocol for tracking social interactions.



<a name="ecomm2" />
### 3.9 Ecommerce parameters

| **Parameter** | **Maps to**      | **Description**               | **Implemented?** | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:-----------------|:--------------------------|
| `ti`          | `tr_orderid`     | Unique ID for transaction     | Yes              | `0015313`                 |
| `ta`          | `tr_affiliation` | Store name or affiliation     | Yes              | `Member`                  |
| `tr`          | `tr_total`       | Total value (revenue) of transaction | Yes       | `24.99`                   |
| `ts`          | `tr_shipping`    | The shipping cost             | Yes              | `3.00`                    |
| `tt`          | `tr_tax`         | The tax (VAT) total for the transaction | Yes    | `2.75`                    |
| `ip`          | `ti_price`       | The price of the item         | Yes              | `12.99`                   |
| `iq`          | `ti_quantity`    | The quantity of the item bought | Yes            | `1`                       |
| `ic`          | `ti_sku`         | Item SKU or product code      | Yes              | `pbz00123`                |
| `in`          | `ti_name`        | Item name                     | Yes              | `The Hezicos Tarot`       |
| `ic`          | `ti_category`    | Item category                 | Yes              | `Tarot decks`             |

Ecommerce tracking has been implemented in a way that closely models Ecommerce tracking in Google Analytics.

Back to [complete list of parameters](#allparams).
