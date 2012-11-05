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
| `e`           | `event`          | The type of event             | `pv` (pageview), `d` (download), `s` (social), `e` (ecomm), `c` (custom) |
| `aid`         | `app_id`         | Unique identifier for website / app | `1`, `company-site`, `angry-birds-androis` |
| `tstamp`      | `timestamp`      | Date / time when the event being logged took place. This is not useful for web tracking (when the events are `GET` requests are made by the Javascript tracker in realtime), in useful in mobile application tracking, where a batch of requests may be made after the events being tracked have already occured, to optimize connectivity |
| `tv`          | `tracker_version`| Tracker ID incl. version number. Should make it clear from which tracker the data was generated | `js-0.5.2`, `iOS-0.0.3` |

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

&e=e             // Ecomm event type.
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



[events-table]: https://github.com/snowplow/snowplow/wiki/canonical-data-structure