<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Trackers**](choosing-a-tracker) > [**Javascript tracker**](Javascript-tracker-setup)

This is divided into two sections:

1. [Setting up Google Tag Manager](#setup-gtm)
2. [Integrating SnowPlow Javascript tracking tags with Google Tag Manager](#snowplow-setup)

If you have already setup Google Tag Manager on your website, you can proceed directly to [section 2](#snowplow-setup). However, we recommend at least skimming the section on [setting up Google Tag Manager](#setup-gtm), as we've seen a number of companies implement this badly, with the end result that they cannot pass all the data they would like to SnowPlow tags for analysis later.

<a name="setup-gtm" />
## 1. Setting up Google Tag Manager

### Overview

There are five steps to setting up GTM to the point you can integrate SnowPlow (or any other web analytics tool) with GTM:

1. [Create a Google Tag Manager account](#1.1)
2. [Integrate the container tag on your website](#1.2)
3. [Work out what data to pass to Google Tag Manager from your website, using the `dataLayer`](#1.3)
4. [Integrate the dataLayer onto your website](#1.4)
5. [Create Macros for the data objects in the `dataLayer` in in the GTM UI](#1.5)

Typically, the steps people get wrong (or miss out alltogether) are steps 3-5. Getting them right is critical, however, because if you do not setup a robust mechanism for passing **all** the relevant data you want to report on in SnowPlow (or any other web analytics program) into GTM, then GTM will not be able to pass that data into SnowPlow, in turn. Designing and managing the data pipeline between your website and GTM is therefore critical.

<a name="1.1" />
### 1.1 Create a Google Tag Manager account

Creating a Google Tag Manager account is very simple. Log on to [[http://www.google.com/tagmanager/]] and select the **sign up now** button. Once you have signed up, elect to create a **New Account**. Give the account a name and a timezone. Then within it, create a **Container**. 

[[/setup-guide/images/gtm/1-create-account.png]]

In general, you'll only need to setup a single account, and most likely a single container for each domain you manage. However, that is not necessarily the case:

1. A container is a single GTM tag that you will install on your web pages instead of the individual tags from different web analytics and other vendors. If you use a common tag setup across many domains, then you can use the same container across multiple domains, rather than create a container for each domain. This might save administrative time in GTM, because you can then add and manage tags across all the domains in one go. (It is straightforward to add tags for a single domain within a multi-domain container setup, and to distinguish data collected from each domain, so there really is no disadvantage in using a single container across multiple domains.)
2. An account is a grouping of containers for administrative purposes. In general, one account is all you would need. If you work with different teams to manage different containers, then it makes sense to group the containers used by each team into a single account, with each team member being granted access to the account.

### 1.2 Integrating the container tag on your website

Once you have created a container, Google will provide you with the actual container tag. You will need to integrate this on every web page on your website(s), in place of any tags that are currently directly integrated on those web pages. (These will need to be migrated into GTM.)

[[/setup-guide/images/gtm/2-grab-embed-code.png]]

The embed code needs to be inserted on your web pages immediately after the opening `<body>` tag.

<a name="1.3" />
### Work out what data to pass to Google Tag Manager from your website, using the `dataLayer`

[Back to top](#top)

<a name="snowplow-setup" />
## 2. Integrating SnowPlow Javascript tracking tags with Google Tag Manager

[Back to top](#top)