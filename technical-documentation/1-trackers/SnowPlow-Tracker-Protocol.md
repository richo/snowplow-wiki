<a name="top" />

1. [Overview](#overview)
2. [Sending common hit types](#common)
3. [Complete list of field names and parameters](#allparams)


<a name="overview" />
## 1. Overview

SnowPlow trackers fire _events_, which are `GET` or `POST` requests of a [SnowPlow collector](collectors), whenever an event on a website or application takes place. By appending parameters and values to the end of those `GET` or `POST` requests, trackers can pass data into the collectors, for processing by SnowPlow. 

The SnowPlow Tracker Protocol is the list of all the parameters that SnowPlow trackers use when firing events to push data into the [SnowPlow collectors] (collectors). Each parameter maps onto one or more fields in the [SnowPlow events table] (canonical-data-structure) employed in storage. Here we document which field in the [SnowPlow events table] (canonical-data-structure) each parameter added to the query string maps onto. 

SnowPlow has been architected to be as easy as possible for developers to create their own alternative subsystems. This documentation should be used by anyone who would like to build their own tracker: by utilising the parameters documented here, the author of a new tracker can be confident that his / her tracker will work with the rest of the SnowPlow stack, and be confident where the values associated with each parameter on every call will be available to query in SnowPlow, whether that's in Hive or Infobright or another database.

[Back to top](#top)

<a name="common" />
## 2. Sending common hit types

1. [Pageview tracking](#pageview)
2. [Event tracking](#event)
3. [Ecomm tracking](#ecomm)
4. [Social tracking](#social)
5. [Campaign tracking](#campaign)
6. [Error tracking](#error)

<a name="pageview" />
### 1. Pageview tracking


	uid=aeb1691c5a0ee5a6        // User ID
	vid=2                       // Visit ID (i.e. session 
	tid=508780					// Transaction ID
	page=Tarot%20cards%20-%20Psychic%20Bazaar       // Name of the page (URL encoded)
	url=http://www.psychicbazaar.com/2-tarot-cards  // Page URL
	number for this `user_id`)
	refr=http://www.psychicbazaar.com               // Referrer URL


Back to [common hit types](#common)

<a name="event" />
### 2. Event tracking

TO WRITE

Back to [common hit types](#common)

<a name="ecomm" />
### 3. Ecommerce tracking 

TO WRITE

Back to [common hit types](#common)

<a name="social" />
TO WRITE

Back to [common hit types](#common)

<a name="campaign" />
TO WRITE

Back to [common hit types](#common)

<a name="error" />
TO WRITE

Back to [common hit types](#common)

[Back to top](#top)

<a name="allparams" />
## 3. Complete list of field names and parameters



[events-table]: https://github.com/snowplow/snowplow/wiki/canonical-data-structure