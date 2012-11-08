[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](SnowPlow technical documentation) > [**Storage**](storage documentation)

## Contents

1. [Current table defition](#current)
2. [Future table definition (incl. planned fields)](#future)

<a name="current" />
## 1. Current table definition

	CREATE TABLE IF NOT EXISTS events (
		`dt` date,
		`tm` time,
		`txn_id` int,
		`user_id` varchar(16) comment 'lookup',
		`user_ipaddress` varchar(19) comment 'lookup',
		`visit_id` smallint,
		`page_url` varchar(2083) comment 'lookup',
		`page_title` varchar(2083) comment 'lookup',
		`page_referrer` varchar(2083) comment 'lookup',
		`mkt_source` varchar(255) comment 'lookup',
		`mkt_medium` varchar(255) comment 'lookup',
		`mkt_term` varchar(255) comment 'lookup',
		`mkt_content` varchar(2083) comment 'lookup',
		`mkt_campaign` varchar(255) comment 'lookup',
		`ev_category` varchar(255) comment 'lookup',
		`ev_action` varchar(255) comment 'lookup',
		`ev_label` varchar(255) comment 'lookup',
		`ev_property` varchar(255) comment 'lookup',
		`ev_value` float,
		`tr_orderid` varchar(255) comment 'lookup',
		`tr_affiliation` varchar(255),
		`tr_total` dec(18,2),
		`tr_tax` dec(18,2),
		`tr_shipping` dec(18,2),
		`tr_city` varchar(255) comment 'lookup'.
		`tr_state` varchar(255) comment 'lookup',
		`tr_country` varchar(255) comment 'lookup',
		`ti_orderid` varchar(255) comment 'lookup',
		`ti_sku` varchar(255) comment 'lookup',
		`ti_name` varchar(255) comment 'lookup',
		`ti_category` varchar(255) comment 'lookup',
		`ti_price` dec(18,2),
		`ti_quantity` int,
		`br_name` varchar(50) comment 'lookup',
		`br_family` varchar(50) comment 'lookup',
		`br_version` varchar(50) comment 'lookup',
		`br_type` varchar(50) comment 'lookup',
		`br_renderengine` varchar(50) comment 'lookup',
		`br_lang` varchar(10) comment 'lookup',
		`br_features_pdf` boolean,
		`br_features_flash` boolean,
		`br_features_java` boolean,
		`br_features_director` boolean,
		`br_features_quicktime` boolean,
		`br_features_realplayer` boolean,
		`br_features_windowsmedia` boolean,
		`br_features_gears` boolean ,
		`br_features_silverlight` boolean,
		`br_cookies` boolean,
		`os_name` varchar(50) comment 'lookup',
		`os_family` varchar(50) comment 'lookup',
		`os_manufacturer` varchar(50) comment 'lookup',
		`dvce_type` varchar(50) comment 'lookup',
		`dvce_ismobile` boolean,
		`dvce_screenwidth` mediumint,
		`dvce_screenheight` mediumint,
		app_id varchar(255) comment 'lookup'
	) ENGINE=BRIGHTHOUSE DEFAULT CHARSET=utf8;

<a name="future" />
## 2. Future table definition (incl. all planned fields)

TO WRITE