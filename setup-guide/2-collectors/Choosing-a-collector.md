[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Collectors**](choosing-a-collector) 

[[/images/2-collectors.png]] 

## Available trackers

| **Tracker**                                    | **Description**                                     | **Status**       |
|:-----------------------------------------------|:----------------------------------------------------|:-----------------|
| [Cloudfront collector](setting-up-the-cloudfront-collector) | A simple, but very robust and scalable collector powered by AWS Cloudfront | Production-ready |
| [Clojure / Elastic Beanstalk collector](setting-up-the-clojure-collector)| A Clojure-based collector that enables user tracking across domains. Powered by Amazon Elastic Beanstalk | Beta      |
| [SnowCannon (node.js) collector](snowcannon-setup-guide) | A real-time, node.js based collector that enables user tracking across domains | Beta |


## Making a selection

Where you are tracking using across a single domain and do not require real-time analytics, we recommend using the [Cloudfront collector](setting-up-the-cloudfront-collector). This is the simplest collector (in terms of having no moving parts: because it does not set `user_id`s itself, but leaves that to be done client-side) and as a result, it is incredibly robust, reliable and scalabe. Indeed, we've heard that [Quantcast](http://www.quantcast.com/) use a Cloudfront in the same way to collect audience data across every website with Quantcast tracking tags around the world.

In order to track users across domains (so set `user_id`s server side), you will need a collector with some moving parts. Both the [Clojure collector](setting-up-the-clojure-collector) and [SnowCannon](snowcannon-setup-guide) provide this. They are both very similar architecturally (the Clojure collector is a port of the node.js in SnowCannon). Choosing between the two of them is as much a question of taste about node.js vs clojure as anything else.