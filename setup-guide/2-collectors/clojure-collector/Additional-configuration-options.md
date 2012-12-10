[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Collectors**](choosing-a-collector) > [**Clojure collector setup**](setting-up-the-clojure-collector) > [[Additional configuration options]]

There are a environment configuration parameters that you may want to consider tailoring to your specific needs. All of them can be accessed via the **Edit Configuration** dialogue box in the AWS Managemetn Console.

<a name="3bi" ></a>

#### 1. Setting the environment to develop or production

You can set an environment mode in the collector to "development" or "production". This can be set via the Elastic Beanstalk UI. Simply select **Edit configuration**, select the **Conatainer** tab and scroll down. Enter your desired environment name in **Param1** and then select **Apply Changes**:

[[/setup-guide/images/clojure-collector-setup-guide/10.png]]

Setting the environment to 'development' means you can view the status of the collector on `http://{{COLLECTOR URL}}/status`. It is set to production by default.

<a name="3bii" ></a>

#### 2. Setting the P3P policy header

This can be entered directly into the same dialogue box as the environment name (see [3b i](#3bi) above), but in **Param2** rather than **Param1**.

If it is not set, the P£3P policy header defaults to:

	policyref="/w3c/p3p.xml", CP="NOI DSP COR NID PSA OUR IND COM NAV STA"

<a name="3biii" ></a>

#### 3. Setting the domain name

The domain name can be entered directly into the same dialogue box as the [environment name](#3bi) and [P3P policy header](#3bii) by entering it into **Param3**.

Setting the domain name can be useful if you want to make the cookie accessible to other applications on your domain. In our example above, for example, we've setup the collector on `collector.snplow.com`. If we do not set a domain name, the cookie will default to thsi domain. However, if we set it to `.snplow.com`, that cookie will be accessible to other applications running on `*.snplow.com`.

<a name="3biv" ></a>

#### 4. Setting the cookie duration

This can be entered into the same dialogue box as the [environment name](#3bi), [P3P policy header](#3bii) and [domain name](#3biii), by entering the value in **Param4**. The value entered should be an integer representing cookie duration measured in days.

If no value is provided, cookies set default to expiring after one year (i.e. 365 days).

#### 5. Auto scaling

Elastic Beanstalk can scale up the number of webservers running the collector to handle spikes in traffic.

Basic settings (minimum and maximum numbers of servers) can be set in the configuration dialogue box, under the **Auto Scaling** tab. 

[[/setup-guide/images/clojure-collector-setup-guide/11.png]]

You can tell Amazon in what circumstances to launch new instances by setting 'triggers'. More details on tuning Elastic Beanstalk can be found [here](http://docs.amazonwebservices.com/elasticbeanstalk/latest/dg/using-features.
