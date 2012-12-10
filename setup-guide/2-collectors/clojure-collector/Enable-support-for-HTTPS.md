[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**Collectors**](choosing-a-collector) > [**Clojure collector setup**](setting-up-the-clojure-collector) > [[Enable support for HTTPS]]

In order to track user behaviour on HTTPS web pages (e.g. shop checkouts), it is necessary to configure HTTPS for your AWS Elastic Beanstalk Environment. This requires that you use a custom domain as your endpoint (rather than the `{{ENVIRONMENT-NAME}}.elasticbeanstalk.com`) and have purchased an SSL certificate for that custom domain.

### 4.1 Using custom domains

Using a custom domain is straightforward. In this tutorial, we will use the custom domain `collector.snplow.com`. We own the domain `snplow.com` and have it managed through [Linode][linode]. If you host a domain name with a different 3rd party to Linode, the steps will be broadly the same, although the UI will likely be different. If you use Amazon Route 53 to host your domains, instructions on using these with Elastic Beanstalk can be found [here][route-53]. 

To use a custom domain, all we have to do is create a CNAME with our DNS provider, and map that CNAME to our Elastic Beanstalk environment URL. (In our case, `cc-endpoint.elasticbeanstalk.com`.) In Linode, we login and naviage to the **DNS Manager**, where we select the custom domain we want to use i.e. `snplow.com` and scroll down to the **CNAME Records**:

[[/setup-guide/images/clojure-collector-setup-guide/15.png]]

We enter the host name we want to use for our collector (we'll use `collector.snplow.com`), alias it to our Elastic Beanstalk environment URL (`cc-endpoint.elasticbeanstalk.com`) and set the TTL to the shortest time that our provider offers:

[[/setup-guide/images/clojure-collector-setup-guide/16.png]]

In due course, we should be able to enter our custom domain with a `/i` in a browser URL window (in our case `collector.snplow.com`). Inspecting the page with developer tools, we should be able to see that a cookie has been set i.e. the domain is correcting aliasing our collector on Elastic Beanstalk:

[[/setup-guide/images/clojure-collector-setup-guide/18.png]]

### 4.2 Configuring HTTPS for Elastic Beanstalk

Now that we are running our collector on our own domain, we can serve it via HTTPS. 

#### 4.2.1 Pre-requisites

1. An SSL certificate for your custom domain
2. [OpenSSL][open-ssl]
3. [Java][java]. This is required by the Amazon command line tools which are used to upload your security certificate to Amazon.

#### 4.2.2 Steps required

The following will walk you through the process of enabling HTTPS using a wildcard SSL certificate purchased from [Comodo][comodo] and setup using Linux / Ubuntu. For instructions on performing the setup on a Windows machine consult the [Amazon guide][amazon-https-eb-setup].

The following steps are required to setup SSL:

1. [Download and install the AWS Identity and Access Management (IAM) command line tools](#download-and-install-cli)  
2. [Use OpenSSL to convert the signed certificates received from your SSL provider (in our case, Comodo) into a format suitable for Amazon](#openssl-prep-keys)  
3. [Use the command line tools to upload the certificates to Amazon](#upload-cert-to-amazon)  
4. [Update your Elastic Beanstalk environment to use HTTPS](#use-https)  

<a name="download-and-install-cli"></a>

#### 4.2.1 Download and install AWS Identity and Access Management command line tools

Navigate [here](http://aws.amazon.com/developertools/AWS-Identity-and-Access-Management/4143) and click the download button.

Extract the contents of the zip file to a suitable location on your hard drive.

Now we need to set a variable `JAVA_HOME` and point it at our JAVA installation. At the command prompt:

	$ export JAVA_HOME=/usr/lib/jvm/java-6-sun

(You may need to alter the location of `JAVA_HOME` depending on where yours lives.)

Now set the variable `AWS-IAM_HOME` to the location where you saved the command line tools:

	$ export AWS_IAM_HOME=~/Apps/IAMCli-1.5.0

(Again - update the path to reflect the location you extracted the tools to.) For simplicity, add the tools to your `PATH` directory:

	$ export PATH=$AWS_IAM_HOME/bin:$PATH

Use a text-editor to create a file in command line tools directory with your AWS Access Key and Secret Key specified e.g.:
	
	AWSAccessKeyId=AKIAIOSFODNN7EXAMPLE
	AWSSecretKey=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

You can get these details from the AWS Management Console, by clicking on your username (top right of the screen) and selecting **Security Credentials**.

[[/setup-guide/images/clojure-collector-setup-guide/17.png]]

Save the file down. (The  name doesn't matter - we called ours `account-key`.) Now set `AWS_CREDENTIAL_FILE` to the path and filename of the credential file you just created e.g.

	$ export AWS_CREDENTIAL_FILE=~/Apps/IAMCli-1.5.0/account-key

To check your installation is working, try running `iam-usercreate -h` at the command prompt. You should see:

	Creates a new user in your account. You can also optionally add the user to one or more groups, and create an access key for the user.
	iam-usercreate [options...] arguments...
	 --aws-credential-file CREDENTIALFILE  : path to the file containing your AWS cr
	                                         edentials
	 --client-config-file CLIENTCONFIGFILE : path to the file containing client conf
	                                         igurations
	 --url SERVICEENDPOINT                 : endpoint URL to be used when making the
	                                          service call
	 -d (--debug)                          : enable debug logging
	 -g GROUPS                             : add user to group(s)
	 -h                                    : print out this message
	 -k                                    : create a key for the user
	 -p PATH                               : the path of the user, defaults to /
	 -u USERNAME                           : the name of the user
	 -v VERBOSE                            : print out the newly created user's arn 
	                                         and guid

<a name="openssl-prep-keys" ></a>

#### 4.2.2 Use OpenSSL to convert the signed certificates received from your SSL provider (in our case, Comodo) into a format suitable for Amazon.

When we bought our SSL wildcard certificate for `snplow.com` domain, we received three files from Comodo:

1. `STAR_snplow_com.crt`. This is the file that is unique to our domain.
2. `AddTrustExternalCARoot.crt`. This is the root certificate for our domain.
3. `PostiveSSLCA2.crt`. This is an intermediate certificate, which is used which the root certificate (above) to form a certificate chain for our domain.

In addition, when we applied for the SSL certificate, we generated a private key file called `snplow-ssl.key`. We will need to use this again, now.

There are four things we need to upload into Amazon to enable HTTPS on our collector:

1. A `pem` encoded private key file. This will be generated from `snplow-ssl.key`.
2. The `pem` encoded public domain certficate. This will be generated from `STAR-snplow_com.crt`
3. A `pem` encoded certificate chain file. This will be generated from `AddTrustExternalCaRoot.crt` and `PositiveSSLCA2.crt`.


We use OpenSSL to create the `pem` encoded private key file. Navigate to the directory where your private key lives and execute the following command:

	openssl rsa -in snplow-ssl.key -out snplow-ssl.pem

Note: you will need to substitute the  name of your private key for our `snplow-ssl.key` and the name of the `.pem` file you create for `snplow-ssl.pem`. This will be one of the files you upload to Amazon.

We use OpenSSL to create the `pem` encoded public domain certificate, by executing the following command:

	openssl x509 -inform PEM -in STAR_snplow_com.crt -out STAR_snplow_com.pem

And again, we use OpenSSL to create the `pem` encoded certificate chain file:

	(openssl x509 -inform PEM -in PositiveSSLCA2.crt; openssl x509 -inform PEM -in AddTrustExternalCARoot.crt) > certificate-chain-file.crt

Now we're ready to upload our certificate to Amazon.

<a name="upload-cert-to-amazon" ></a>

#### 4.2.3 Upload the certificate to Amazon

Execute the following command at the command prompt to upload the certificate to Amazon:

	$ iam-servercertupload -b STAR_snplow_com.pem -c certificate-chain-file.crt -k snplow-ssl.pem -s snplow_certificate

Notes:

* Substitute in your PEM encoded certificate body file for `STAR-snplow_com.pem` i.e. the file you created from the domain specific file you received when you bought the SSL certificate
* Substitute in your PEM encoded certificate key chain file for `certificate-chain-file.crt`
* Substitute in your PEM encoded private key file for `snplow-ssl.pem`
* You can choose whatevert name you give your certificate (we've used `snplow_certificate`). Just make a note of your choice, as you'll need to reference it within Amazon.

You then need to get your SSL Certificate ID from Elastic Beanstalk, by running the following command:

	$ iam-servercertgetattributes -s snplow_certificate

Amazon will respond with something like:

	arn:aws:iam::889434468096:server-certificate/snplow_certificate
	ASCAJLVQA7JJZQYPM64FC

The first line (i.e. `arn:aws:iam::889434468096:server-certificate/snplow_certificate`) is the **Amazon Resource Number** (ARN). Take a note of yours, we will need this in the next step.

<a name="use-https"></a>

#### 4.2.4 Update your Elastic Beanstalk environment to use HTTPS

Return back to the AWS Management Console ([[console.aws.amazon.com]]). In the Elastic Beanstalk section, select your environment and choose to **Edit / load configuration** from the **Actions** dropdown. Then select **Load Balancer** from the tab:

[[/setup-guide/images/clojure-collector-setup-guide/19.png]]

Set the **HTTPS Listener Port** to on (either 443 or 8443) and add your ARN into the **SSL Certificate ID** field:

[[/setup-guide/images/clojure-collector-setup-guide/20.png]]

Click the **Apply Changes** button. The environment will update. When it has completed, you will be able to test that HTTPS access is working, by using your browser to navigate to `https://{{MY CUSTOM COLLECTOR DOMAIN}}/i` and look to see if the cookie is set via developer tools:

[[/setup-guide/images/clojure-collector-setup-guide/20.png]]

Note the padlock icon by the address bar.

Next: [[set your tracker to point at the Clojure collector endpoint]]


[linode]: http://www.linode.com/
[route-53]: http://docs.amazonwebservices.com/elasticbeanstalk/latest/dg/customdomains.html
[open-ssl]: http://www.openssl.org/
[comodo]: http://ssl.comodo.com/index.php?ap=ComodoSSLJun12&key1sk5=3159&key1sk1=sem&gclid=CLzP7KPOhbQCFe7MtAodBAsApQ
[amazon-https-eb-setup]: http://docs.amazonwebservices.com/elasticbeanstalk/latest/dg/configuring-https.html
[java]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
