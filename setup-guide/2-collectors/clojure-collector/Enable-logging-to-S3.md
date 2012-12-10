[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Collectors**](choosing-a-collector) > [**Clojure collector setup**](setting-up-the-clojure-collector) > [[Enable logging to S3]]

Now that your application is up and running, you need to update the configuration so that the Tomcat logs are pushed to S3. (These will be processed by the [etl](choosing an etl module) step to generate your SnowPlow data.) Click on the **Actions** dropdown and select **Edit/Load configuration**.

[[/setup-guide/images/clojure-collector-setup-guide/6.png]]

Select the **Container** tab in the dialogue box, and then check the box marked **Enable log file rotation to Amazon S3**:

[[/setup-guide/images/clojure-collector-setup-guide/9.png]]

Click the **Apply changes** button. The environment update will be processed, and the collector app should be live again after a few minutes.

### Checking that your logs are being pushed to S3

Amazon pushes Tomcat access logs to S3 hourly. (In our experience, they generally appear 10 minutes passed each hour.) Finding your logs in S3, however, is not entirely trivial...

On the Amazon Web Services console, navigate to the EC2 section of the site, and click on the **Instances** item in the Navigation menu on the left hand side. A list of all the EC2 instances you are running should show as below:

[[/setup-guide/images/clojure-collector-setup-guide/12.png]]

You should see at least one instance with the name given to the environment you created above. (In our case, 'cc-endpoint'.) Select it:

[[/setup-guide/images/clojure-collector-setup-guide/13.png]]

There are two identifiers we need to take note of. The first is the **security group** (highlighted in the screenshot above). Here we are interested in the characters between `awseb-` and `-stack-AWSEBSecurityGroup` i.e. in our case, `e-bgp9nsynv7`. The other identifier we need to note is the Instance identifier listed both in the summary menu (under **My Instances**) and in the more detailed pane. In our case, this is `i-ff035fb4`.

Now that we have those two identifiers, we can locate the logs in S3. In the web console, navigate to the S3 section. On the left hand side, you should see at least one bucket with a name that begins `elasticbeanstalk` and then follows with the region you setup Elastic Beanstalk in:

[[/setup-guide/images/clojure-collector-setup-guide/14.png]]

Select that bucket - you should find a folder within it called resources. Within that folder you should find another called `environments`. Within that folder you should find a logs folder, within that a publish folder and within that a folder with the security group identifier you noted above. (In our case `e-bgp9nsynv7`.) Within that folder you should find another with your instance identifier, also noted above. (In our case, `i-ff035fb4`.) The path to your logs is therefore:

	s3://elasticbeanstalk-{{REGION NAME}}-{{UUID}}/resources/environments/logs/{{SECURITY GROUP IDENTIFIER}}/{{INSTANCE IDENTIFIER}}

These logs will be processed by the [EmrEtlRunner](#emr-etl-runner).

Note - if you cannot find the logs as described above, it may be because Amazon has not written any yet. You may need to wait an hour for the logs to appear.

Next: [[enable support for HTTPS]]

