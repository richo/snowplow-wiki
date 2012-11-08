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

	mysql>  
