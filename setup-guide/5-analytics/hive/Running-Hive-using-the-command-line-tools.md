[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Analytics**](analytics-setup) > [**Hive analytics setup**](hive-analytics-setup)

# Querying your SnowPlow web analytics data using Hive

Many of the analyses we perform in SnowPlow use Hive. We tend to use Hive Interactive Sessions to develop queries and analyses. Once a set of queries has been developed in the interactive sessions, they can be stored as a text file in S3 and run as a batch process directly from the Elastic Mapreduce Command Line tools.

This part of the guide walks through the process of launching and running a Hive Interactive session. The steps involved are:

1. [Starting a job](#startingajob) i.e. firing up a set of instances to run Hive / Hadoop
2. [SSHing in to the instances and launching Hive](#sshin)
3. [Running Hive queries](#runninghivequeries)
4. [Terminating the session](#terminatingthesession)

Most of the analyses we perform are in Hive interactive sessions: because it is in these types of sessions that we can actively query data, explore results and develop more sophisticated analyses.

New sessions can either be initiated at the command-line, or via aws.amazon.com console. 

<a name="startingajob"/>
## Starting a job

There are 2 ways to start a job / fire up instances to run Hive: 

1. [Using the Ruby Client](#usingtherubyclient)
2. [From the Amazon Web UI](#fromtheamazonwebui)

<a name="usingtherubyclient"/>
### Starting a job using the Ruby Client

#### Starting a job using the Ruby Client on Mac / Linux

To initiative a new session on Mac / Linux, navigate to the `elastic-mapreduce-cli` folder (where you saved the command-line tools) and enter

	$ ./elastic-mapreduce --create --alive --name "Hive Job Flow" --hive-interactive

You should see something like:

![Launch a Hive session from the command-line](setup-guide/images/emr-guide/run-hive-interactive-session-1.jpg)

Note: The Ruby command line interface tools uses the security information you provided in the `credentials.json` file and takes a set of default values for e.g. the number of instances that are fired up. For more details, consult the [EMR command line tools documentation](http://aws.amazon.com/developertools/2264).

If rather than run an interactive version, you wanted to execute a script (i.e. file containing multiple Hive commands), you would upload the script to s3 and then run the following command:

	$ ./elastic-mapreduce --create --name "script example" --hive-script s3://{{bucket-name}}/{{path of script file}}

#### Starting a job using the Ruby Client on PC

TO WRITE: Add instructions to launch a session from the PC command-line, incl. setting up Putty and Pageant to SSH successfully

### Starting a job using the web UI

TO WRITE: Add instructions on creating jobs via the Amazon web UI. 

### Checking that the job has been setup using the Amazon web UI

Note: this will work however you initiated the job. (Whether you used the Ruby Client or the Web UI.)

Log into the [Amazon Web Console](https://console.aws.amazon.com/console/home) and click on [Elastic MapReduce] in the top menu bar. You should see the job you created listed. (In the screenshot below you'll see that we've initiated 2 Hive sessions.)

![Launch a Hive session from the command-line](setup-guide/images/emr-guide/run-hive-interactive-session-2.jpg)

Note: you can also check on the status of your current jobs via the command-line interface:

	./elastic-mapreduce --list

The above command will list all the current jobs including their statuses.

<a name="sshin"/>
## Establishing an SSH connection

### Establishing the SSH connection: Mac / Linux users

Return to the command-line, establish an SSH connection by entering the following

	$ ./elastic-mapreduce --ssh --jobflow {{JobFlowID}}

You can get your jobflowID either from the Amazon web UI or by listing all the jobs using the `./elastic-mapreduce --list` command line tool.

Substituting the JobFlowID generated when you created the session. You should see:

![Launch a Hive session from the command-line](setup-guide/images/emr-guide/run-hive-interactive-session-3.jpg)

Now you can launch Hive by typing `Hive` at the command line:

![Launch a Hive session from the command-line](setup-guide/images/emr-guide/run-hive-interactive-session-4.jpg)

### Establishing the SSH connection: PC users

TO WRITE

<a name="runninghivequeries"/>
## Running Hive queries

SnowPlow data is stored in a table called `events`. Before we can query it, we need to let Hive know about it (define it in Hive). We do so using the `CREATE EXTERNAL TABLE` statement:

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
	platform string,
	event_name string,
	v_tracker string,
	v_collector string,
	v_etl string
	)
	PARTITIONED BY (dt STRING)
	LOCATION '${EVENTS_TABLE}' ;	

**Notes:**

1. Don't forget to include the trailing slash in the location address e.g. `LOCATION 's3://snowplow-hive-tables/events/'`. 
2. The table is `EXTERNAL` because the data in it is not managed by Hive: it is stored in S3 (and only accessed by Hive). As a result, if you drop the table in Hive (`DROP TABLE snowplow_events_log`), the data will remain safely in S3, even if the table disappears from Hive. 
3. You need to list every field in the statement, which is why it's so long. To save time, copy and paste the query to the command line :-)

In general, SnowPlow users partition their data by date. We need to let Hive know that this table is partitioned, and to look to spot the partitions that already exist:

	ALTER TABLE events RECOVER PARTITIONS;

Now that the table has been created in Hive it is possible to query it:

	SHOW TABLES ;

Should return a list of tables: in this case our single `events` table.

We can now try running some simple queries. Remember: these will take some time from large data sets. (Especially if we're using the default cluster size - which is only two small EC2 instances.) To speed up query performance, limit the volume of data by specifying a data range e.g. `WHERE dt >='2012-09-01 AND dt<='2012-09-25'`.

The following query will return the number of unique visitors by day:

	SELECT 
	dt,
	COUNT(DISTINCT user_id) AS unique_visitors
	FROM events
	GROUP BY dt ;

To get the number of visits per day:

	SELECT 
	dt,
	COUNT(DISTINCT (CONCAT(user_id, visit_id))) AS visits
	FROM events
	GROUP BY dt ;

Of course, in either of the above cases, we could aggregate our results by month (or any other time period) rather than by year:

	SELECT 
	YEAR(dt) AS yr,
	MONTH(dt) AS mnth,
	COUNT(DISTINCT user_id) AS unique_visitors,
	COUNT(DISTINCT (CONCAT(user_id, visit_id))) AS visits
	FROM events 
	GROUP BY YEAR(dt), MONTH(dt) ;


We can look at the number of 'transactions' (incl. page views, add-to-baskets, and other user actions) by each user to get a sense of whom are most engaged users are, just over May 2012:

	SELECT 
	user_id,
	COUNT(txn_id)
	FROM events
	WHERE YEAR(dt) = 2012 AND MONTH(dt) = 5
	GROUP BY user_id ;

The results of the queries can be copied and pasted directly into Excel or a text-editor (from the command line terminal) for quick-and-dirty ad hoc analysis. More properly, it is trivial to write the results to S3, where they can be migrated into an analytics database for further analysis, used to feed a KPI dashboard or accessed on subsequent Hive analytics sessions. To do so, simply create a new table (making sure it is `EXTERNAL`), and output the contents of the `SELECT` statement into it as follows:

	CREATE EXTERNAL TABLE `{{YOUR-RESULTS-TABLE}}`
	LOCATION 's3://{{BUCKET-AND-FOLDER-WHERE-YOU-WISH-TO-SAVE-THE-DATA}}/'

	INSERT OVERWRITE `{{YOUR-RESULTS-TABLE}}`
	SELECT 
	user_id,
	COUNT(txn_id)
	FROM events
	WHERE YEAR(dt) = 2012 AND MONTH(dt) = 5
	GROUP BY user_id ;

There are many ways of fetching the data from S3, either directly via the S3 web UI, or programmatically via the API. 


<a name="terminatingthesession"/>
## Terminating the session

Interactive sessions have to be terminated manually. (Or else you risk running up high Amazon fees...) Sessions can either be terminated at the command-line, using the Ruby Client, or via the web UI.

### Terminating the session using the web UI

TO WRITE

### Terminating the session at the command-line using the Ruby Client

To terminate the sessions via the command line, simply exit the session (by typing `exit ;` to escape Hive, then `exit` again at the EC2 command line to end the SSH session.) You then use the EMR command line tools to terminate the session:

	./elastic-mapreduce --terminate --jobflow {{JOBFLOW ID}}

## Want to learn more?

Consult the SnowPlow [Analytics Cookbook](http://snowplowanalytics.com/analytics/index.html) for more Hive recipes.