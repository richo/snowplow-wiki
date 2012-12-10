[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](SnowPlow setup guide) > [**TRACKERS**](choosing-a-tracker) > [**Clojure collector setup**](setting-up-the-clojure-collector)  

1. [Introduction / overview](Setting up the Clojure collector)
2. [Download the Clojoure collector WAR file, or compile it from source](Download-the-Clojoure-collector-WAR-file-or-compile-it-from-source). (Required)  
3. [Create a new application in Elastic Beanstalk, and upload the WAR file into it] (Create-a-new-application-in-Elastic-Beanstalk-and-upload-the-WAR-file-into-it). (Required)  
4. [Enable logging to S3](Enable logging to S3). (Required)  
5. [Enable support for HTTPS](Enable support for HTTPS). (Optional, but recommended.)  
6. [Set your tracker to point at the Clojure collector endpoint](Set your tracker to point at the Clojure collector endpoint). (Required)  
7. [Update the EmrEtlRunner configuration YAML file](Update-the-EmrEtlRunner-configuration-YAML-file). (Required)  

In addition, we document [additional configuration options](additional configuration options) at the end of this guide.

[Back to **Choosing a collector**](choosing-a-collector)  
[Back to **SnowPlow Setup Guide**](SnowPlow setup guide)  
[Back to **wiki home**](Home) 