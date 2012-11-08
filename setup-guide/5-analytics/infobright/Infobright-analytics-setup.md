[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Analytics**](analytics-setup) > [**Infobright analytics setup**](infobright-analytics-setup)

Once [Infobright storage](infobright-storage-setup) has been setup, you can start performing analyses immediately.

There are, broadly speaking, 3 ways to query the data:

1. [Query the data directly in Infobright, at the command line](#command-line)
2. [Query the data in Infobright using a SQL admin tool](#sql-admin)
3. [Query the data in Infobright using an analysis tool e.g. Tableau, R](#analysis-tool)

<a name="command-line" />
## Querying the data directly in Infobright, at the command line

It is possible to query the data directly in Infobright. SSH into the server running Infobright, then fire it up at the command line:

	$ ssh {{your-infobright-server-name}}
	$ mysql-ib -u {{username}} -p

Enter your password when prompted.

Now switch to the SnowPlow database:

	mysql> use snowplow ;
	Database changed

We can now query the events table directly. For example, to count the number of uniques, visits and page views per day:

	SELECT
	dt,
	COUNT(DISTINCT(user_id)) AS uniques,
	COUNT(DISTINCT( CONCAT( user_id, "-", visit_id))) AS visits,
	COUNT(page_url) AS page_views
	FROM 
	events
	WHERE page_url IS NOT NULL
	GROUP BY dt ;

<a name="sql-admin" />
## Querying the data in Infobright using a SQL admin tool

There are a wide range of SQL admin tools that make executing SQL queries and performing analysis using SQL queries a lot easier. Because Infobright is a fork from MySQL, any SQL admin tool that works with MySQL will work with Infobright. (A decent list is available [here](http://www.veign.com/blog/2010/05/03/top-15-mysql-managers-and-tools/).)

When setting up your connection between your tool of choice and Infobright, follow the instructions as if you were setting up a connection between the tool and a MySQL database, but remember that Infobright's default port is `5029`. (Rather than MySQL's `3306`).

## Querying the data in Infobright using an analysis tool e.g. Tableau, R, Weka

Because any tool that can read data from MySQL can also read data from Infobright, it is possible to access and process the data stored in Infobright using some very powerful analysis tools e.g. [Tableau] [tableau], [R] [r] and [Weka] [weka]. 

In some cases, you will want to transform the format of the data into one suitable for your analysis tool of choice: for example, before the data can be read by Tableau, it should be formatted into a table containing clearly defined dimensions and metrics. You may want to peform the transformation in SQL, before importing the data into the analysis tool. (This depends on the analysis tool, and how much ETL functionality it has within it.)

[tableau]: http://www.tableausoftware.com/
[r]: http://www.r-project.org/
[weka]: http://weka.pentaho.com/

