# Canonical data structure  

* **Single table** SnowPlow data is stored in a single, "fat" (many columns) table. We call this the *SnowPlow events table*
* **Each line represents one event**. Each line in the table represents a single *event*, be that a page view, add to basket, play video etc, facebook-like etc.
* **Immutable log**. The SnowPlow data table is designed to be immutable: the data in each line should not change over time. Data points that we would expect to change over time (e.g. what cohort a particular user belongs to, how we classify a particular visitor) can be derived from SnowPlow data. However, our recommendation is that these derived fields should be defined and calculated at analysis time, stored in a separate table and joined to the *SnowPlow events table* when performing any analysis

### Contents

1. [Current SnowPlow data structure](#current)
2. [Future SnowPlow data structure](#future)
3. [S3 / Hive storage](s3-hive-storage)
4. [Infobright storage](infobright-storage)

<a name="current" />
## Current SnowPlow data structure

The fields recorded in the *SnowPlow events table* today:

| **FIELD**            | **DATATYPE**   | **CAN BE EMPTY?** | **DESCRIPTION**            |
|:---------------------|:---------------|:------------------|:---------------------------|
| **Date/time**        |                |                   | _The date and time of this page view or event_ |
| `dt`                 | STRING         | No                | Date                       |
| `tm`                 | STRING         | No                | Time                       |
| **Transaction**      |                |                   | _Identifying this logging event_ |
| `txn_id`             | STRING         | No                | A unique event ID. If two or more records have the same txn_id, one is a duplicate record |
| **User and visit**   |                |                   | _Identifying the web user and this specific visit_ |
| `user_id`            | STRING         | No                | A unique ID assigned to each browser and stored on the SnowPlow cookie. |
| `user_ipaddress`     | STRING         | No                | Visitor IP Address         |
| `visit_id`           | INT            | No                | A counter that indicates what visit this is for this particular user_id i.e. 1 if this is a user's first visit, 2 if it is his / her 2nd visit |
| **Page**             |                |                   | _Identifying the web page being visited_ |
| `page_url`           | STRING         | No                | The web page URL           |
| `page_title`         | STRING         | Yes               | The web page title         |
| `page_referrer`      | STRING         | No                | The referrer URL. If this is the first page view of a session, it points at the referrering website / search engine if applicable |
| **Marketing**        |                |                   | _Marketing campaign attribution_ |
| `mkt_medium`         | STRING         | Yes               | The type of ad used e.g. cpc, banner, email, affiliate... |
| `mkt_source`         | STRING         | Yes               | The source of the ad: used e.g. Google, MSN, Facebook, TradeDoubler |
| `mkt_term`           | STRING         | Yes               | Any keywords associated with the ad. This is relevant for search ads |
| `mkt_content`        | STRING         | Yes               | The content of the ad, or a reference to the creative ID. Used e.g. to compare the results within a campaign between different creatives. |
| `mkt_campaign`       | STRING         | Yes               | The campaign name. A single campaign may involve ads on multiple sources / mediums, so `mkt_campaign` is often a way of grouping them together into a single marketing initiative |
| **Event**            |                |                   | _If this is an event being logged, its details_ |
| `ev_category`        | STRING         | Yes               | The category of event e.g. 'ecomm', 'media' |
| `ev_action`          | STRING         | Yes               | The action performed e.g. 'play-video', 'add-to-basket' |
| `ev_label`           | STRING         | Yes               | A label associated with the event / action. This is often set to the *object* and action is performed *on* e.g. the product_id of the item added-to-basket, or the ID of the video played |
| `ev_property`        | STRING         | Yes               | A property associated with the event / action. This might be the number of seconds into a video play starts, or the quantity of an item added to basket |
| `ev_value`           | STRING         | Yes               | A value associated with with the action e.g. the value of the items added to basket |
| **Browser**          |                |                   | _Information about the web browser_ |
| `br_name`            | STRING         | Yes               | Browser name e.g. Internet Explorer |
| `br_family`          | STRING         | Yes               | Browser family e.g. Chrome          |
| `br_version`         | STRING         | Yes               | Browser version            |
| `br_type`            | STRING         | Yes               | Type of client e.g. Browser, robot|
| `br_renderengine`    | STRING         | Yes               | Browser rendering engine e.g. GECKO, WEBKIT |
| `br_lang`            | STRING         | Yes               | Language that the browser is set to  |
| `br_features`        | ARRAY\[STRING\]| Yes               | Contains a set of all features supported by this browser, e.g. `fla` for Flash, `pdf` for PDF support |
| `br_cookies`         | BOOLEAN        | Yes               | Flag set to 'true' if browser permits cookies |
| **Operating System** |                |                   | _Information about the host operating system_ |
| `os_name`            | STRING         | Yes               | Operating system name e.g. Windows            |
| `os_family`          | STRING         | Yes               | Operating system family e.g. Android          |
| `os_manufacturer`    | STRING         | Yes               | Operating system manufacturer e.g. Apple Inc., Google Inc.  |
| **Device/Hardware**  |                |                   | _Information about the host device/hardware_ |
| `dvce_type`          | STRING         | Yes               | Device type e.g. computer, mobile...         |
| `dvce_ismobile`      | BOOLEAN        | Yes               | Flag set if user is browsing on a mobile device  |
| `dvce_screenwidth`   | INT            | Yes               | Screenwidth in pixels      |
| `dvce_screenheight`  | INT            | Yes               | Screenheight in pixels     |

<a name="future" />
## Future SnowPlow data structure

We are building out the **SnowPlow events table** to incorporate new fields in the near future. A complete list of all the fields (current and intended) is given below:

| **FIELD**            | **DATATYPE**   | **CAN BE EMPTY?** | Implemented?  | **DESCRIPTION**            |
|:---------------------|:---------------|:------------------|:--------------|:---------------------------|
| **Date/time**        |                |                   |               | _The date and time of this page view or event_ |
| `dt`                 | STRING         | No                | Yes           | Date                       |
| `tm`                 | STRING         | No                | Yes           | Time                       |
| **Transaction**      |                |                   |               | _Identifying this logging event_ |
| `txn_id`             | STRING         | No                | Yes           | A unique event ID. If two or more records have the same txn_id, one is a duplicate record |
| **User and visit**   |                |                   | Yes           | _Identifying the web user and this specific visit_ |
| `user_id`            | STRING         | No                | Yes           | A unique ID assigned to each browser and stored on the SnowPlow cookie. |
| `user_ipaddress`     | STRING         | No                | Yes           | Visitor IP Address         |
| `user_domain`        | STRING         | Yes               | No            | User domain (based on IP address) |
| `visit_id`           | INT            | No                | Yes           | A counter that indicates what visit this is for this particular user_id i.e. 1 if this is a user's first visit, 2 if it is his / her 2nd visit |
| `connection_type`    | STRING         | Yes               | No            | Type of connection |        
| **Page**             |                |                   |               | _Identifying the web page being visited_ |
| `page_url`           | STRING         | No                | Yes           | The web page URL           |
| `page_title`         | STRING         | Yes               | Yes           | The web page title         |
| `page_referrer`      | STRING         | No                | Yes           | The referrer URL. If this is the first page view of a session, it points at the referrering website / search engine if applicable |
| **Marketing**        |                |                   |               | _Marketing campaign attribution_ |
| `mkt_medium`         | STRING         | Yes               | Yes           | The type of ad used e.g. cpc, banner, email, affiliate... |
| `mkt_source`         | STRING         | Yes               | Yes           | The source of the ad: used e.g. Google, MSN, Facebook, TradeDoubler |
| `mkt_term`           | STRING         | Yes               | Yes           | Any keywords associated with the ad. This is relevant for search ads |
| `mkt_content`        | STRING         | Yes               | Yes           | The content of the ad, or a reference to the creative ID. Used e.g. to compare the results within a campaign between different creatives. |
| `mkt_campaign`       | STRING         | Yes               | Yes           | The campaign name. A single campaign may involve ads on multiple sources / mediums, so `mkt_campaign` is often a way of grouping them together into a single marketing initiative |
| `mkt_referrerurl     | STRING         | Yes               | No            | URL of referrer. Same as `page_referrer` but only set where `page_referrer` is not the same domain as the website |
| **Event**            |                |                   |               | _If this is an event being logged, its details_ |
| `ev_category`        | STRING         | Yes               | Yes           | The category of event e.g. 'ecomm', 'media' |
| `ev_action`          | STRING         | Yes               | Yes           | The action performed e.g. 'play-video', 'add-to-basket' |
| `ev_label`           | STRING         | Yes               | Yes           | A label associated with the event / action. This is often set to the *object* and action is performed *on* e.g. the product_id of the item added-to-basket, or the ID of the video played |
| `ev_property`        | STRING         | Yes               | Yes           | A property associated with the event / action. This might be the number of seconds into a video play starts, or the quantity of an item added to basket |
| `ev_value`           | STRING         | Yes               | Yes           | A value associated with with the action e.g. the value of the items added to basket |
| **Social event**     |                |                   |               | Fields for social event tracking    |
| `social_network`     | STRING         | Yes               | No            | Social network that action relates to e.g. `Facebook`, `Twitter` |
| `social_action`      | STRING         | Yes               | No            | Action that user performed e.g. `like` |
| 'social_target'      | STRING         | Yes               | No            | The social object that the action was performed on e.g. the video that was 'liked' |
| `social_pagepath`    | STRING         | Yes               | No            | The page URL the action was committed on |
| **Browser**          |                |                   |               | _Information about the web browser_ |
| `br_name`            | STRING         | Yes               | Yes           | Browser name e.g. Internet Explorer 9 |
| `br_family`          | STRING         | Yes               | Yes           | Browser family e.g. Chrome          |
| `br_version`         | STRING         | Yes               | Yes           | Browser version            |
| `br_type`            | STRING         | Yes               | Yes           | Type of client e.g. Browser, robot|
| `br_renderengine`    | STRING         | Yes               | Yes           | Browser rendering engine e.g. GECKO, WEBKIT |
| `br_lang`            | STRING         | Yes               | Yes           | Language that the browser is set to  |
| `br_features`        | ARRAY\[STRING\]| Yes               | Yes           | Contains a set of all features supported by this browser, e.g. `fla` for Flash, `pdf` for PDF support |
| `br_cookies`         | BOOLEAN        | Yes               | Yes           | Flag set to 'true' if browser permits cookies |
| `br_jsversion        | STRING         | Yes               | No            | Version of Javascript that browser is running |
| `br_windowheight`    | INT            | Yes               | No            | Height of browser window in pixels |
| `br_windowwidth`     | INT            | Yes               | No            | Width of browser window in pixels  |
| `br_colordepth`      | STRING         | Yes               | No            | Browser color depth |
| **Operating System** |                |                   |               | _Information about the host operating system_ |
| `os_name`            | STRING         | Yes               | Yes           | Operating system name e.g. Windows            |
| `os_family`          | STRING         | Yes               | Yes           | Operating system family e.g. Android          |
| `os_version`         | STRING         | Yes               | No            | Operating system version |
| `os_manufacturer`    | STRING         | Yes               | Yes           | Operating system manufacturer e.g. Apple Inc., Google Inc.  |
| **Device/Hardware**  |                |                   |               | _Information about the host device/hardware_ |
| `dvce_name`          | STRING         | Yes               | No            | Name of device e.g. 'iPhone' |
| `dvce_type`          | STRING         | Yes               | Yes           | Device type e.g. computer, mobile...         |
| `dvce_ismobile`      | BOOLEAN        | Yes               | Yes           | Flag set if user is browsing on a mobile device  |
| `dvce_isspider`      | BOOLEAN        | Yes               | No            | Flag set if 'visitor' is a web crawler |
| `dvce_screenwidth`   | INT            | Yes               | Yes           | Screenwidth in pixels      |
| `dvce_screenheight`  | INT            | Yes               | Yes           | Screenheight in pixels     |
| **Geo / location**   |                |                   |               | _Information on the location of the visitor, derived from IP address_ |
| `geo_country`        | STRING         | Yes               | No            | Country visitor is located in |
| `geo_region`         | STRING         | Yes               | No            | Region visitor is located in, within country |
| `geo_city`           | STRING         | Yes               | No            | City visitor is located in |
| `geo_postcode`       | STRING         | Yes               | No            | Visitor postcode           |
| `geo_latitude`       | STRING         | Yes               | No            | Visitor location latitude  |
| `geo_longitude`      | STRING         | Yes               | No            | Visitor location longitude |
