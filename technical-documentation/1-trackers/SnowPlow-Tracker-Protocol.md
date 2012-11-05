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
6. [Campaign tracking](#campaign)
7. [Error tracking](#error)

<a name="common-params" />
### 2.1. Common parameters

| **Parameter** | **Name**         | **Description**               | **Example values**        | 
|:--------------|:-----------------|:------------------------------|:--------------------------|
| `uid`         | `user_id`        | Unique identifier for user    | `aeb1691c5a0ee5a6`        |
| `vid`         | `visit_id`       | Visit / session identifier for this user e.g. `1` is first visit | `1`, `2`...|
| `tid`         | `txn_id` (transaction ID) | Unique identifier for this specific event | `508780` |


```javascript
uid=aeb1691c5a0ee5a6   // User ID
&vid=1                 // Visit ID (session number for this user_id)
```

<a name="pageview" />
### 2.2. Pageview tracking

```javascript
uid=aeb1691c5a0ee5a6                             // User ID
&vid=2                                           // Visit ID (i.e. session number for this user_id)
&tid=508780					                     // Transaction ID
&page=Tarot%20cards%20                           // Page title (URL encoded&url=http://www.psychicbazaar.com/2-tarot-cards  // Page URL
&refr=http://www.psychicbazaar.com               // Referrer URL
```

Back to [common event types](#common)

<a name="event" />
### 2.3. Custom event tracking

TO WRITE

Back to [common event types](#common)

<a name="ecomm" />
### 2.4. Ecommerce tracking 

TO WRITE

Back to [common event types](#common)

<a name="social" />
### 2.5. Social tracking
TO WRITE

Back to [common event types](#common)

<a name="campaign" />
### 2.6. Campaign tracking
TO WRITE

Back to [common event types](#common)

<a name="error" />
### 2.7 Error tracking

TO WRITE

Back to [common event types](#common)

[Back to top](#top)

<a name="allparams" />
## 3. Complete list of field names and parameters



[events-table]: https://github.com/snowplow/snowplow/wiki/canonical-data-structure