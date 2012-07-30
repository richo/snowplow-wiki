A very common requirement for web analytics platforms including SnowPlow is that they correctly identify traffic from different campaigns, so that it is possible to:

1. calculate the return on ad spend (ROAS) on those campaigns, and 
2. compare the behaviour of customers from different ad campaigns on your website.

In order to ensure that the campaigns you setup are correctly tracked by SnowPlow, it is important that any links from those campaigns to your site have the relevant query string parameters (described below) included in them. The query string parameters used are exactly the same employed by Google Analytics: as a result, any campaign that is setup to be correctly tracked in Google Analytics should automatically be correctly tracked in SnowPlow. (The only exception is campaigns in AdWords, which Google uses an alternative, proprietary technique for joining AdWords data to Analytics data.)

### Campaign tracking with SnowPlow: an overview

Your different campaigns (PPC campaigns, display ads, email marketing messages, Facebook campaigns etc.) will include one or more links to your website e.g.:

	<a href="http://mysite.com/myproduct.html">Visit website</a>

We want to be able to identify people who've clicked on ads e.g. in a marketing email as having come to the site having clicked on a link in that particular marketing email. To do that, we modify the link in the marketing email with query parameters, like so:

	<a href="http://mysite.com/myproduct.html?utm_source=newsletter-october&utm_medium=email&utm_campaign=cn0201">Visit website</a>

For the prospective customer clicking on the link, adding the query parameters does not change the user experience. (The user is still directed to the webpage at `http://mysite.com/myproduct.html`.) But SnowPlow then has access to the fields given in the query string, and uses them to identify this user as originating from the October Newsletter, an email marketing campaign with campaign id = `cn0201`.

### Query parameters used

As mentioned earlier, SnowPlow uses the same query parameters used by Google Analytics. Those parameters are:

| **Parameter**        | **Name**              | **Description**                                     |
|:--------------------:|:---------------------:|:---------------------------------------------------:|
| `utm_source`         | Campaign source       | Identify the advertiser driving traffic to your site e.g. Google, Facebook, autumn-newsletter etc.  |
| `utm_medium`         | Campaign medium       | The advertising / marketing medium e.g. cpc, banner, email newsletter, in-app ad, cpa |
| `utm_campaign`       | Campaign id           | A unique campaign id. This can be a descriptive name or a number / string that is then looked up against a campaign table as part of the analysis |
| `utm_term  `         | Campaign term(s)      | Used for search marketing in particular, this field is used to identify the search terms that triggered the ad being displayed in the search results. |
| `utm_content`        | Campaign content      | Used either to differentiate similar content or two links in the same ad. (So that it is possible to identify which is generating more traffic.) |

The parameters are descibed in the [Google Analytics help page] [gahelppage]. Google also provides a [urlbuilder] [gaurlbuilder] which can be used to construct the URL incl. query parameters to use in your campaigns.

### Tracking AdWords campaigns in SnowPlow

When tracking AdWords campaigns in SnowPlow, 2 things should be highlighted:

1. Google offers customers who use both AdWords and Analytics a custom integration between the two programmes that means Analytics correctly reports which AdWords campaigns particular visitors to your site **without** adding the relevant query parameters to the query string. As a result, additional work is required to setup your AdWords campaigns so they are correctly tracked in SnowPlow. (In contrast, any other marketing channel that is correctly setup for Google Analytics will automatically work with SnowPlow with no additional work, because Google only offers the custom integration between AdWords and Analytics.)
2. AdWords includes a number of dynamic parameters (called [ValueTrack parameters][gavaluetrackparameters]) that makes setting parameters that might change for a particular campaign (like the particular keyword that triggered the ad) very easy

#### Adding the query parameters to the AdWords link

When setting up or editing an ad in AdWords, you can specify a link to the landing page a user is directed to on clicking the ad:

![adwordsediturl] [gaadwordsediturl]

Assume that the landing page URL is:

	http://mysite.com/myproduct.html

##### 1. Adding utm_source

First let's identify the source of this campaign as Google AdWords, by updating the landing page URL with the utm_source parameter:

	http://mysite.com/myproduct.html?utm_source=Google

We might want to distinguish ads from Google Search results with results from the Google content network. In this case, we can use [ValueTrack parameters] [gavaluetrackparameters] as follows:

	http://mysite.com/myproduct.html?utmsource=Google{ifsearch:Search}{ifcontent:Content}

The above makes use of the [Value Click parameters] [gavaluetrackparameters]. These are set when an ad is shown: if the ad is shown on the Google Display Network the link will read:

	http://mysite.com/myproduct.html?utmsource=GoogleSearch

Hence SnowPlow will classify the campaign as having source='GoogleSearch'.  If on the other hand the ad is shown on the Google Display Network, the link will read:

	http://mysite.com/myproduct.html?utmsource=GoogleDispay

Note: what actual text you choose to display if the source is Google, Google Search or Google Display is up to you: SnowPlow take whatever text you provide and let you report against it. (So differentiate traffic from this source with others.) The important thing is that you consistently identify traffic from the same sources using the same `utm_source` parameter, so it is all correctly identified as coming from the same source. What you call that parameter, however, is entirely up to you.

##### 2. Adding utm_medium

Next, let's identify the medium of this campaign. If it is an AdWords campaign, then it is probably a CPC (cost-per-click) campaign, so we add `&utm_medium=cpc` to the end of the query string. (If it's been setup as a CPA (cost per acquisition) campaign, we can add `&utm_medium=cpa` instead):

	http://mysite.com/myproduct.html?utmsource=Google{ifsearch:Search}{ifcontent:Content}&utm_medium=cpc

##### 3. Adding utm_campaign

A sensible campaign name / ID is the campaign name given in Google AdWords. If this is "handbag-summer-2012-promotion" then we add `&utm_campaign=handbag-summer-2012-promotion` to the query string:

	http://mysite.com/myproduct.html?utmsource=Google{ifsearch:Search}{ifcontent:Content}&utm_medium=cpc&utm_campaign=handbag-summer-2012-promotion

##### 4. Adding utm_term

It is valuable to know which keywords a user entered in his / her search query to trigger the ad being shown. (Or if it's the display network, which words on the web page triggered the ad.) Because a single ad may have many different keywords associated with it, this value needs to be set dynamically when an ad is shown. Fortunately, Google has provided a [value parameter] [gavalueparameters] that does just that: `{keyword}`. Hence, all we have to do is add `&utm_term={keyword}` to our link:

	http://mysite.com/myproduct.html?utmsource=Google{ifsearch:Search}{ifcontent:Content}&utm_medium=cpc&utm_campaign=handbag-summer-2012-promotion&utm_term={keyword}

##### 5. Adding utm_content

There may be many different creatives associated with each campaign in AdWords. To identify which it was that this user clicked on, we can use the `{}` value parameter by adding: `&utm_content={creative}` to the link:


[gahelppage]: https://support.google.com/analytics/bin/answer.py?hl=en&answer=1033863&ctx=cb&src=cb&cbid=-oxeewb61m1du&cbrank=1 
[gaurlbuilder]: https://support.google.com/analytics/bin/answer.py?hl=en&answer=1033867
[gavaluetrackparameters]: http://support.google.com/adwords/bin/answer.py?hl=en&answer=2375447
[gaadwordsediturl]: /snowplow/snowplow/wiki/technical-documentation/images/adwords-query-string.png
[zoneappend]: /snowplow/snowplow/wiki/technical-documentation/images/03a_zone_prepend_openx.png