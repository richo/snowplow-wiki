SnowPlow is a powerful web analytics platform that takes a fundamentally different approach to other web analytics packages. Those key differences are:

1. Users capture and have access to atomic, event-level and customer-level data
2. The data is stored in a big data warehouse (hosted by Amazon) that makes it easy to use a wide range of cutting edge big data tools (incl. Apache Hive, Hbase and Hadoop) to process and analyse that data
3. All data is kept on user's own Amazon accounts
4. The complete data collection and storage stack is open source. (Going forwards, we do intend to release additional analytics tools on a SaaS basis.)

### The benefits of the SnowPlow approach

There are several key benefits to using SnowPlow either instead of, or in addition to, standard web analytics packages:

1. [Own your data](#own-your-data)
2. [Improve your understanding of your customer with sophisticated customer analytics](#customer-analytics)
3. [Better understand how effectively your digital product developing with product analytics](#product-analytics)
4. [Analytics for all companies (not just publishers and retailers)](#not-just-pubs-and-retailers)
5. [Integrate your web analytics data with other customer data sources (e.g. social, CRM)](#other-data-sources)
6. [Use your web analytics data to develop content and product recommendation engines](#recommendation)

<a name="own-your-data" />
#### 1. Own your data

With SnowPlow, your web analytics data is stored in your own Amazon S3 account. You control it absolutely: you can export it into other systems, archive it or keep it in Amazon, regardless of whether you continue to use SnowPlow. The data is yours. Forever.

<a name="customer-analytics" />
#### 2. Improve your understanding of your customer with better customer analytics

Traditional web analytics packages report visits and pageviews.

Because SnowPlow exposes granular customer-level and event-level data, you can perform a host of customer analyses that are not possible with traditional web analytics packages:

1. Identify your most valuable customers
2. Segment your customers by value
3. Compare average customer values between users acquired from different channels, users who joined in different months and other relevant customer data
6. Predict which customers are likely to be valuable for you long term, by identifying events that are preditive of value
8. Identify _sliding doors_ moments in a customer's journey that impact long term customer value, and look at ways to encourage customers to take one course of action rather than another at those junctures
7. Accurately measure your return on marketing spend based on the lifetime value of the customer acquired. (Not simple click throughs and conversions.)
8. Analyse customer behaviour across multiple visits to your site
9. Zoom in on data for particular customers, and use that to assist with customer support and relationship management

<a name="product-analytics" />
##### 3. Better understand how effective your digital product (website) is with product analytics

Traditional web analytics packages provide canned reports that let companies assess how effective their website is at driving customers through predefined funnels.

SnowPlow allows a much more expansive set of product analytics, enabling companies to:

1. Identify where your product works well, and where it does not. (E.g. web pages or points in customer journeys where customers "get stuck")
2. Measure the improvement in engagement and customer value based on the roll out of new product developments
3. Look at the way customers actually engage with your site, instead of simply the fraction of all customers that progress down a pre-defined funnel
4. Compare how effectively different segments of the customer base use different parts of the website, to understand if some customer segments are less well served than others

<a name="not-just-pubs-and-retailers" />
##### 4. Analytics for all companies (not just publishers and retailers)

Traditional web analytics reports have been developed primarily for content businesses (which want to understand the number of page views different content pieces attract) and retailers (who want to track clicks and conversions).

By enabling companies to perform a much broader set of [customer analytics](#customer-analytics) and [product analytics](#product-analytics) (see above), SnowPlow is a great web analytics platform for a much wider set of digital businesses, including:

1. Social networks and communtiy sites
2. Online games
3. B2B SaaS products (e.g. email marketing, analytics, financial services)
4. Online productivity tools

<a name="other-data-sources" />
#### 5. Integrate your web analytics data with other customer data sources to create a _single customer view_

Web analytics data has historically been hard to integrate with other data sources, because of the volume of cusotmer data, and the requirement to keep it granular (separate data by customer) to enable joins between data sets.

SnowPlow makes it possible both to export web analytics data out of SnowPlow for analysis with other data sources elsewhere, AND to import other data sources into SnowPlow. Because SnowPlow is based on big data technology, it is even possible to import in external big data sources e.g. data from social networks.

Types of data you may want to integrate with SnowPlow data, as part of a single customer view include:

1. Social media data
2. CRM data
3. In-store data
4. Mobile app data. (If a separate system is used for mobile app tracking)

<a name="recommendation" />
#### 6 Use your web analytics data to develop content and product recommendation engines

Web analytics data collected by SnowPlow need not only be used to drive reporting. It can also be used to drive a range of data-driven operational processes including:

1. Product recommendation
2. Ad serving
3. Content personalisation

SnowPlow can turbo-charge development of the above applications by:

1. Providing the raw data to "feed" those recommendation / personalisation engines
2. Providing an analytics framework to develop and test those recommendation algorithms offline, before hard wiring them into live, production systems
3. Being used to periodically test and iterate those recommendation engines