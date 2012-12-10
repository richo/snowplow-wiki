[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Collectors**](choosing-a-collector) > [**Clojure collector setup**](setting-up-the-clojure-collector) > [[Create a new application in Elastic Beanstalk and upload the WAR file into it]]

Amazon makes it easy to create a new application in Elastic Beanstalk and upload your `war` file into it. All of this is possible via the web UI.

On your web browser, log into the [AWS control panel][aws]. From the **Services** dropdown menu select **Elastic Beanstalk**. 

Before you create your application, you need to switch to the region you want your web server located. Select your region from the dropdown on the top right of the screen:

[[/setup-guide/images/clojure-collector-setup-guide/0.png]]

Once you've selected your region, you're ready to create your application. Click on the **Create application** button (located on the top right of the screen, below the top menu bar with the region selection):

[[/setup-guide/images/clojure-collector-setup-guide/1.png]]

Give your application a suitable name and description. Select **32bit Amazon Linux running Tomcat 7** for the container type. For the **Application Source**, select **Upload your Existing Application**. Use the **Choose File** button to point Elastic Beanstalk at the `war` file from [part 1](#war-file).

[[/setup-guide/images/clojure-collector-setup-guide/2.png]]

Next you need to configure the environment details. You can setup multiple environments for each application, but for our purposes one is enough.

Keep the **Launch a new environment running this application** checkbox checked (i.e. selected), but **deselect** the 2nd option: **Create an RDS DB Instance with this environment**. We do **not** need a database to run the collector.

Give your environment a suitable name, URL and description:

[[/setup-guide/images/clojure-collector-setup-guide/3.png]]

Next we need to specify another set of configuration details. Set a suitable instance type (we recommend at least `m1.small`). If you have an EC2 key pair configured, you can enter the key pair name at this stage: this will enable you to use the key pair to SSH in should you wish. (This is not required, and can be added later without any difficulty.)

For the **Application Health Check URL** enter a single slash i.e. `/`:

[[/setup-guide/images/clojure-collector-setup-guide/4.png]]

Click **Continue**. Amazon gives you the chance to review your inputs. When you've checked them click **Finish**. 

Amazon then sets up your the application and environment. When this is complete, you should see a screen like the one below. Note the green box, and the **Successflly running version First Release** notice.

[[/setup-guide/images/clojure-collector-setup-guide/5.png]]

To test that all is working as expected, select the **Environment Details** dropdown:

[[/setup-guide/images/clojure-collector-setup-guide/7.png]]

Click on the **URL** link. (This is [[http://cc-endpoint.elasticbeanstalk.com]] in the example above.) This should return a 404. If you add `/i' to the path (e.g. `http://cc-endpoint.elasticbeanstalk.com/i`), right click on the window and select **Inspect Element** in Chrome or **Inspect with Firebug** in Firefox, you should be able to see if the cookie has been set:

[[/setup-guide/images/clojure-collector-setup-guide/8.png]]

Next: [[enable logging to S3]]

[aws]: https://console.aws.amazon.com/
