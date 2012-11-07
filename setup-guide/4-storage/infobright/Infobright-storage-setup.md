## Table of contents

1. [Introduction](#introduction)
2. [Installing ICE](#install)
3. [Configuring ICE](#configure)
4. [Testing ICE](#test)


<a name="introduction" />
## 1. Introduction

[Infobright Community Edition (ICE)](http://www.infobright.org/) is an open-source columnar database. For all but the largest SnowPlow users, columnar databases such as Infobright should be an attractive alternative to doing all of your analysis in Hive:

* Queries are typically faster, especially when they do not involve processing the complete data set (e.g. only a handful of fields).
* Unlike EMR, you do not have to 'pay' for every query. (The computing costs associated with running Infobright are fixed, although you will need to invest in more hardware as your data volumes and user numbers rise.)
* SnowPlow data is well suited to analysis in Infobright and other columnar databases: our single fat events table processes especially efficiently in Infobright, because it is a columnar database, and because many of the fields contain fewer distinct values than total values.


<a name="install" />
## 2. Installing ICE

This guide walks you through the process of installing ICE on Debian / Ubuntu, using instructions adapted from Infobright's own [DEB/RPM](http://www.infobright.org/wiki/Install_Guide_for_Linux/) and tar ([part one](http://www.infobright.org/wiki/Install_Guide/), [part two](http://www.infobright.org/wiki/Install_Guide_Part_2/)). ICE can also be run on Windows Servers and other variants of Linux: for details on how to setup ICE in those environments, please refer to the [Infobright website](http://www.infobright.org/wiki/Getting_Started/).

### Download

You can download the latest version of ICE on the [Infobright downloads](http://www.infobright.org/index.php/Download/ICE/) page. At the time of writing, the latest version of ICE was v4.0.7:

	$ cd ~/downloads
	$ wget http://www.infobright.org/downloads/ice/infobright-4.0.7-0-x86_64-ice.deb

### Install

To install:
	
	$ sudo -i
	$ cd ~/downloads
	$ dpkg -i infobright-4.0.7-0-x86_64-ice.deb

<a name="config" />
## 3. Configuring ICE

### Configuration file

You need to enable the standard configuration file:

	$ cp /etc/my-ib.cnf.inactive /etc/my-ib.cnf

### Install as a service

Unfortunately, the Debian installer has some problems setting up the ICE daemon. Check for:

	$ which /etc/init.d/mysqld-ib
	/etc/init.d/mysqld-ib

If it's not there, you will have to copy support-files/mysql.server to the right place for your system.

Assuming it is there, start the service:

	$ /etc/init.d/mysqld-ib start
	Starting MySQL
	..

### Secure your installation

Now we need to secure your ICE installation. Assuming you are still sudo'ed in as root, run:

	$ PATH=$PATH:/usr/local/infobright-4.0.7-x86_64/bin
	$ /usr/local/infobright-4.0.7-x86_64/bin/mysql_secure_installation

And set the root password:

	Set root password? [Y/n] Y
	New password: <<Enter password>> 
	Re-enter new password: <<Enter password>> 
	Password updated successfully!
	Reloading privilege tables..
	 ... Success!    

And get rid of anonymous users:

	Remove anonymous users? [Y/n] Y
	 ... Success!

And disable root login:

	Disallow root login remotely? [Y/n] Y
 	... Success!

Drop the test database:

	Remove test database and access to it? [Y/n] Y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!

And finally reload the privilege tables:

	Reload privilege tables now? [Y/n] Y
	 ... Success!

Now exit from root:

	$ exit

### Configure memory

ICE needs the appropriate Server Main Heap Size and Loader Main Heap Size based on how much physical RAM you have:

| **System Memory** | **Server Main Heap** | **Size Loader Main Heap Size** |
|-------------------|----------------------|--------------------------------|
| 64 GB             | 48000                | 800                            |
| 48 GB             | 32000                | 800                            |
| 32 GB             | 24000                | 800                            |
| 24 GB             | 16500                | 800                            |
| 16 GB             | 10000                | 800                            |
| 8 GB              | 4000                 | 800                            |
| 4 GB              | 1300                 | 400                            |
| 2 GB              | 600                  | 320                            |

The installer should set this automatically - to check, edit the memory configuration file:

	$ sudo vi /usr/local/infobright/data/brighthouse.ini

And check, updating if necessary, the following variables as per your available RAM:

	# ServerMainHeapSize - Size of the main memory heap in the server process, in MB
	ServerMainHeapSize=600

	# LoaderMainHeapSize - Size of the memory heap in the loader process, in MB.
	LoaderMainHeapSize=320

Note that some ICE documentation and files mention an additional "Server Compressed Heap Size" variable. This variable is obsolete - you can forget it.

If you change it, restart:

	$ sudo /etc/init.d/mysqld-ib restart

### Set it to survive restart

We don’t need to create aliases for the various init commands, because they are already loaded from scripts.aliases. The relevant aliases are:

	alias icestop="sudo /etc/init.d/mysqld-ib stop"
	alias icestart="sudo /etc/init.d/mysqld-ib start"
	alias icerestart="sudo /etc/init.d/mysqld-ib restart"

And finally make sure that it survives reboot - you might not have to do this step:

	$ sudo /usr/sbin/update-rc.d -f mysqld-ib defaults
	update-rc.d: using dependency based boot sequencing

<a name="testing" />
## Testing ICE

You can test ICE using standard MySQL client tools, although note Infobright's non-standard port number:

	5029

To test using the client that comes with ICE:

	$ cd /usr/local/infobright
	$ bin/mysql --defaults-file=/etc/my-ib.cnf -uroot --password
	Enter password: <<Enter password>> 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 12
	Server version: 5.1.40 build number (revision)=IB_4.0.7_r16961_17249(ice) (static)

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql>

Alternatively you can also test by running Navicat Lite or similar and logging into the database that way.