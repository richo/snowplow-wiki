# Storage in Amazon S3 / Apache Hive

1. [Current table definition](#table-def)
2. [Future table definition incl. all planned fields](#future-table-def)
3. [Optimizing the table structure for query performance: roadmap](#roadmap)

<a name="table-def" />
## Current table definition

The standard S3 / Hive implementation of SnowPlow storage uses Apache Hive on EMR to store SnowPlow data in a table partitioned by date. The current table definition is as follows:

	CREATE EXTERNAL TABLE IF NOT EXISTS `events` (
	tm string,
	txn_id string,
	user_id string,
	user_ipaddress string,
	visit_id int,
	page_url string,
	page_title string,
	page_referrer string,
	mkt_source string,
	mkt_medium string,
	mkt_term string,
	mkt_content string,
	mkt_campaign string,
	ev_category string,
	ev_action string,
	ev_label string,
	ev_property string,
	ev_value string,
	tr_orderid string,
	tr_affiliation string,
	tr_total string,
	tr_tax string,
	tr_shipping string,
	tr_city string,
	tr_state string,
	tr_country string,
	ti_orderid string,
	ti_sku string,
	ti_name string,
	ti_category string,
	ti_price string,
	ti_quantity string,
	br_name string,
	br_family string,
	br_version string,
	br_type string,
	br_renderengine string,
	br_lang string,
	br_features array<string>,
	br_cookies boolean,
	os_name string,
	os_family string,
	os_manufacturer string,
	dvce_type string,
	dvce_ismobile boolean,
	dvce_screenwidth int,
	dvce_screenheight int,
	app_id string
	)
	PARTITIONED BY (dt STRING)
	LOCATION '${EVENTS_TABLE}' ;

<a name="future-table-def" />
## Future table definition (including all planned fields)

Note: whenever new fields are added to the Hive table, they are always added to the **end** of the table definition. As a result, there's no need to reprocess old data as new fields are added. (Because Hive simply sets the value of fields that are missing at the end of lines to `null`.) As a result, however, the ordering of the fields in the Hive table definition is not as tidy as it might be, reflecting when teh fields were added to the table definition, rather than a natural way of grouping columns together.

	CREATE EXTERNAL TABLE IF NOT EXISTS `events` (
	tm string,
	txn_id string,
	user_id string,
	user_ipaddress string,
	visit_id int,
	page_url string,
	page_title string,
	page_referrer string,
	mkt_source string,
	mkt_medium string,
	mkt_term string,
	mkt_content string,
	mkt_campaign string,
	ev_category string,
	ev_action string,
	ev_label string,
	ev_property string,
	ev_value string,
	tr_orderid string,
	tr_affiliation string,
	tr_total string,
	tr_tax string,
	tr_shipping string,
	tr_city string,
	tr_state string,
	tr_country string,
	ti_orderid string,
	ti_sku string,
	ti_name string,
	ti_category string,
	ti_price string,
	ti_quantity string,
	br_name string,
	br_family string,
	br_version string,
	br_type string,
	br_renderengine string,
	br_lang string,
	br_features array<string>,
	br_cookies boolean,
	os_name string,
	os_family string,
	os_manufacturer string,
	dvce_type string,
	dvce_ismobile boolean,
	dvce_screenwidth int,
	dvce_screenheight int,
	app_id string,
	mkt_referrerurl,
	br_windowheight,
	br_windowwidth,
	br_colordepth,
	br_jsversion,
	os_version,
	dvce_name,
	dvce_isspider,
	geo_country,
	geo_region,
	geo_city,
	geo_postcode,
	geo_latitude,
	geo_longitude,
	social_network,
	social_action,
	social_target,
	social_pagepath,
	click_targeturl,
	click_targettype,
	click_sourceid,
	domain,
	connection_type
	)
	PARTITIONED BY (dt STRING)
	LOCATION '${EVENTS_TABLE}' ;

<a name="roadmap" />
## Optimizing the table format for querying performance - roadmap

There are a variety of things that can be done to improve Hive querying performance on data in S3 and a number of these involve changing the way data is structured in Hive. We have not yet had the chance to test the impact of them to see if any are worthwhile. We welcome any feedback from community members into which are worth exploring / implementing:

1. Changing the storage format of the files to e.g. to binary formats and/or columnar formats (e.g. RCFile) 
2. Bucketing data by `user_id`
3. Using [Qubole](http://www.qubole.com/) as an alternative to EMR