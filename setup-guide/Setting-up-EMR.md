# Setting up Amazon Elastic MapReduce

## Table of Contents

1. [Before you get started...](#intro)
2. [A note on OSes](#oses)
3. [Installing Ruby](#rubyinstall)
4. [Installing the Amazon Elastic MapReduce Ruby Client](#rubyclient)

<a name="intro"/>
## Before you get started...

This is a guide to setting up Amazon Elastic MapReduce. It assumes you are self-hosting SnowPlow on your own Amazon Web Services account. (Note: if a 3rd party is hosting your SnowPlow tracking pixel and data, you will need to liaise with them to setup the EMR tools to access data on their Amazon account.) 

Amazon's Elastic MapReduce enables you to crunch log file data stored in S3, out of the box. To do so, however, we recommend installing [Amazon Elastic MapReduce Ruby Client](http://aws.amazon.com/developertools/2264). This will enable you to connect to Elastic MapReduce using Hive interactive sessions, run queries and develop analyses, all at the command line. 

Amazon offers a number of other [tools to use Elastic MapReduce](http://aws.amazon.com/developertools/Elastic-MapReduce), however the Ruby Client is the only one we will cover in the documentation. (And the one we use most for SnowPlow.)

Amazon has published a very good [getting started](http://docs.amazonwebservices.com/ElasticMapReduce/latest/GettingStartedGuide/Welcome.html?r=7956). This guide can be used as a standalone guide, or read in connection with Amazon's own guide.

To use Elastic MapReduce you will need to install Amazon's "EMR Command Line Interface". This will, in particular, let you run "Hive interactive sessions" in which you can try different queries and develop differnet analyses, using the results of each analysis to inform the next query.

This document will guide you through the steps required to install the EMR tools. The [following guide](Querying-the-data-using-Hive) will talk you through the steps required to use Apache Hive via the command-line tools to query your SnowPlow data.

<a name="oses"/>
## A note on OSes

We have had reports from community members that setting up Ruby and Amazon's "EMR Command Line Interface" is more straightforward in a Unix-based environment (such as Ubuntu or OS X) than it is in Windows.

You may want to bear this in mind if you have a choice of different environments to use to set up EMR.

<a name="rubyinstall"/>
## Installing Ruby

[Amazon Elastic MapReduce Ruby Client](http://aws.amazon.com/developertools/2264) is built in Ruby, so unless you have already have Ruby installed, you'll need to install it. Full instructions on downloading and setting up Ruby can be found [here](#http://www.ruby-lang.org/en/downloads/). There are many ways to install Ruby - if you don't have a strong preference for one of them, we recommend Mac OS X and Linux users use RVM, whilst Windows users use Ruby Installer.

### Installing Ruby on Mac

Mac OS X comes with Ruby installed. You can check the version (you
need Ruby 1.8 or higher) with this command:

    $ ruby -v

### Installing Ruby on Linux

On a Debian-like OS, installing Ruby is easy:

    $ sudo apt-get install ruby-full

### Installing Ruby on Windows

* Go to [RubyInstaller](http://rubyinstaller.org/). Click "Download"

![Downloading Ruby](setup-guide/images/emr-guide/ruby-1.PNG)

* Select the most recent RubyInstaller. The download should begin immediately

![Select the latest Ruby verison](setup-guide/images/emr-guide/ruby-2.PNG)

* Launch the executable file you just downloaded, by double clicking on it. Accept the license terms. When prompted, remember to "Add Ruby executables to your PATH". We also recommend checking the box "Associate .rb and .rbw files with this Ruby installation."

![Select the relevant options](setup-guide/images/emr-guide/ruby-5.PNG)

* The installation should proceed automatically. When completed, click "finish".

* Verify the installation: in either the command-prompt or Windows PowerShell, enter "ruby -v". The Ruby version should display as below:

![Verify installation was successful](setup-guide/images/emr-guide/ruby-6.PNG)

<a name="rubyclient"/>
## Installing the Amazon Elastic MapReduce Ruby Client

### Downloading the Client 

* On **Linux** or **Mac**, install the Elastic Map Reduce tools into
  your apps folder:
  
    $ mkdir -p ~/Apps/elastic-mapreduce-cli
    $ cd ~/Apps/elastic-mapreduce-cli
    $ wget http://elasticmapreduce.s3.amazonaws.com/elastic-mapreduce-ruby.zip
    $ mv elastic-mapreduce-ruby.zip ~/downloads/

* On **Windows**, navigate to your Ruby folder, and in it, create a new folder for the command line tools:

![Create a directory for the command-line tools](setup-guide/images/emr-guide/install-cli-1.PNG)

* Go to [http://aws.amazon.com/developertools/2264](http://aws.amazon.com/developertools/2264). Login if prompted and click download.

![Download CLI](setup-guide/images/emr-guide/install-cli-2.PNG)

* Now go to the folder you just installed Ruby in, and in it, create a new folder where you'll save the Elastic Map Reduce tools. Give this folder an appropriate name e.g. `elastic-mapreduce-cli`. Unzip the download into the new folder, by double clicking on the Zip file (to access the contents), selecting the contents, copying it and then pasting it into the new folder.

![Unzip CLI](setup-guide/images/emr-guide/install-cli-3.PNG)
    
### Configuring the client

* To use the client successfully, you will need the tools to talk directly to your Amazon account without any difficulty. That means ensuring that the correct security / login details are available to the command-line tools

* Start by fetching your AWS Access Key ID and AWS Secret Access Key. To get both of these, log in to [http://aws.amazon.com](#http://aws.amazon.com)

![Download CLI](setup-guide/images/emr-guide/install-cli-4.PNG)

* Click on *My Account* (by clicking on your name on the top right of the screen) and select *Security Credentials*. Scroll down to *Access Credentials*: you will be able to take a copy of your *Access Key ID*. To reveal the associated *Secret Access Key*, click on the _Show_ link. Copy and paste both of these to a text-editor: in a bit you will use these to create a config file.

![Download CLI](setup-guide/images/emr-guide/install-cli-5.png)

* Now you have your *access key* and *secret access key*, you need to create an *Amazon EC2 Key Pair*. Elastic MapReduce is built on top of EC2, and Key Pairs are a key part of Amazon's security apparatus. Click on the *Key Pairs* tab (2 along from *Access Keys* in the same screen) and click *Access your Amazon EC2 Key Pairs using the AWS Management Console*. (Don't be distracted by the CloudFront Key Pairs section above - that is not relevant here...)

![Navigate to key pair](setup-guide/images/emr-guide/install-cli-6.PNG)

* In the EC2 window, check what region has been set in the top left, and if necessary, change the region to the one in which you plan to do your analysis. (We use EU West (Ireland) for most of our analysis because we're based in the UK, but Amazon still often defaults to one of the US data center locations...)

* Click on *Key Pairs* on the left hand navigation. (In the _Network & Security_ section.) 

![Create key pair](setup-guide/images/emr-guide/install-cli-7.PNG)

* Click on the *Create Key Pair* button

![Create key pair](setup-guide/images/emr-guide/install-cli-8.PNG)

* When prompted, give the key pair an appropriate name of your choice. Note it down in your text-editor

![Name new key pair](setup-guide/images/emr-guide/install-cli-0.PNG)

* The `.PEM` file should automatically download. Move it to a safe location (e.g. in a subdirectory of `elastic-mapreduce-cli`) and note the location, again in your text editor, along with the name of the key pair. (This should be the same as the name of the `.PEM` file)

* Now go to your text-editor. Create a file `credentials.json` in the `elastic-mapreduce-cli` folder

* Add the following (JSON) code to it, substituting the relevant values, noted above, for `access_id`, `private_key`, `keypair`, `key-pair-file`:

	```javascript	
	{
		"access_id": "[Your AWS Access Key ID. (See above)]",
		"private_key": "[Your AWS Secret Access Key. (See above)]",
		"keypair": "[Your key pair name. (See above)]",
		"key-pair-file": "[The path and name of your PEM file. (See above)]",
		"log_uri": "[A path to the bucket on S3 where your SnowPLow logs are kept. We will identify this in the next section.]",
		"region": "[The Region of yoru job flow, either us-east-1, us-west-2, us-east-1, eu-west-1", eu-west-1, ap-northeast-1, ap-southeast-1, or sa-east-1. We will identify this in the next section]"
	}
	
	```

* The `log-uri` parameter in the `credentials.json` file needs to point at the Amazon S3 bucket that you will use to store the outputs of your analysis. (And any logging of Hadoop sessions, if you desire.) It makes sense to create a new bucket to store these results. To do so, click on the `S3` tab at the top of the _AWS Management Console_, and in the left hand menu under _Buckets_ click the *Create Bucket* button:

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-11.PNG)

* Name the bucket. (You'll need top pick a name that is unique across Amazon S3, to `snowplow-analysis` will not be an option, unfortunately 

* Select which Region you want the bucket located in. Because we're based in the UK, we've picked Ireland: pick the data center that makes the most sense for you. (Most likely the same location as the S3 buckets with the raw SnowPlow data you intend to analyse.) Click the *create* button.

* Now update `credentials.json` with the `log-uri`. This will be `s3n://` + the bucket name + `/`. For us, then 

	`"log_uri" = "s3n://snowplow-analysis/"`

* You also need to set the `region` field in `credentials.json`. Pick the appropriate region - this should match the Region you selected when you created the bucket. So for us that is `eu-west-1`. (we selected _Ireland_)

<table>
	<tr><td>Region name</td><td>Region code</td></tr>
	<tr><td>US Standard</td><td>us-east-1</td></tr>
	<tr><td>Oregon</td><td>us-west-2</tr>
	<tr><td>Northern California</td><td>us-west-1</td></tr>
	<tr><td>Ireland</td><td>eu-west-1</td></tr>
	<tr><td>Japan</td><td>ap-northeast-1</td></tr>
	<tr><td>Singapore</td><td>ap-southeast-1</td></tr>
	<tr><td>Sao Paulo</td><td>sa-east-1</td></tr>
</table>

* You need to set permissions on the S3 bucket, so that the command line tools can write results output to it. To do so, go back to the [S3 console](https://console.aws.amazon.com/s3/), right click on the bucket you created and click on *properties*

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-12.PNG)

* A _Properties_ pane will appear at the bottom of the screen. On the _Permissions_ tabl, click *Add more permissions*

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-13.PNG)

* Select *Authenticated Users* from the *Grantee* dropdown, then select *List* just next to it and click *Save*

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-14.PNG)


### Setting up SSH

Now that you have configured the Ruby client, the last thing to do before you can run a Hive interactive session is setup SSH. This process looks different, depending on whether you using a Mac / Linux or PC.


#### SSH Setup: for Mac and Linux

* Navigate to your `.PEM` file in the command line tool and set the permissions on the file as below:

    $ chmod og-rwx mykeypair.pem 

#### SSH Setup: for Windows

* Download PuTTYgen.exe from [here](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-15a.PNG)

* Double click on the download to launch PuTTYgen. Click on the *Load* button and select the `.PEM` file you downloaded from Amazon earlier, when you created the EC2 key-pair

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-15a.PNG)

![Name new S3 bucket to house analysis](setup-guide/images/emr-guide/install-cli-16.PNG)

* Enter a passphrase in the dialogue box, confirm the passphrase, and click the *Save private key* button. Save the file down as a `.ppk` file: this will be what you use to establish a secure SSL connection.

* Exit the PUTTYgen application

* Now download *PUTTY* and *Pageant* from
  [the same webpage you downloaded PUTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). You
  will need these to establish the SSH connection and run Hive
  
### Testing

Now you're ready to test! Check that the EMR command-line tool runs
okay:

    $ cd ~/Apps/elastic-mapreduce-cli
    $ ./elastic-mapreduce --help
    Usage: elastic-mapreduce [options]

      Creating Job Flows
      <snip>
      
Let's try something more adventurous:

    $ ./elastic-mapreduce --list
    j-11IY9G5EHZGP1     WAITING        ec2-54-247-19-229.eu-west-1.compute.amazonaws.com Development Job Flow (requires manual termination)
       COMPLETED      Setup Hive
       <snip>
       
And finally, let's try starting and ending a job:

    $ ./elastic-mapreduce --create --alive
    Created job flow j-284VOXKIH634U
    $ ./elastic-mapreduce --terminate --jobflow j-284VOXKIH634U
    Terminated job flow j-284VOXKIH634U

Obviously update the jobflow ID with yours. Excellent! Your EMR tool
is now working. Next up, you can proceed to the guide to [[Querying the data using Hive]].