---
title: Parsing Squid Logs
author: James Evans Morantus and Carolyn Duby
tutorial-id: 100
experience: Intermediate
persona: 
source: Hortonworks
use case: 
technology: Apache Metron
release: 
environment: Sandbox
product: HCP
series: HCP > Apache Metron
---

# Parsing Squid Logs

## Objectives

After this lab you will be able to:
 1. Collect a squid proxy log and move it to Kafka using Nifi.
 2. Create a Metron squid parser.
 3. Add some simple transformations to the events.
 4. View the parsed logs in the Metron Alerts UI.

## Generate Squid Access Events

1. Your Metron host comes with a squid proxy installed.   Configure your proxy settings to use your squid proxy.  If you are not able to change your proxy settings, skip this step and use curl commands to generate traffic.

### Chrome Proxy

The easiest way to control your proxy from Chrome is to install and extension such as Proxy SwitchySharp.  An extension enables you to select a menu item in Chrome to switch between your regular settings and your Metron proxy easily.   

On Mac open Chrome and select Chrome > Preferences from the main menu.  Scroll to the bottom of the settings page and click the Show advanced settings link.  Locate the Network section and click the Change proxy settings button. Check Web Proxy (HTTP) and enter `mobius.local` and the squid port 3128.  Check Secure Web Proxy (HTTPS) and enter your Metron name and the squid port 3128.  Click the OK button and then the Apply button to save your changes. 

### OSX Safari Proxy
On OSX go to System Preferences > Network > Wi-Fi. Click the Advanced button. Select the Proxies Tab, and check the Web Proxy (HTTP) and enter `mobius.local` and the squid port 3128.

### Windows Proxy

On Windows, follow the manual instructions on [this page](http://www.dummies.com/computers/operating-systems/windows-10/how-to-set-up-a-proxy-in-windows-10/)

2. In the browser configured with the Metron squid proxy, open a web site that generates web requests such as google.com or a news website.  

To generate data using curl, open a command line tool and enter:
```
curl -I --proxy mobius.local:3128 *web_url*
```
 
For example to access google using a proxy, 
```
curl -I --proxy mobius.local:3128 http://google.com
```

3. Open the Metron alerts ui by entering http://mobius.local:4201 in the browser. 

By default you will see the most recent events at the top of the UI.  At this point you should see some squid events with recent timestamps in the alerts ui.
![Metron Alerts Squid](parsingsquid_imgs/metron_alerts_squid.png)

If you click in the space between the columns, the full details of the event will open on the right side of the browser.
![Metron Alerts Details](parsingsquid_imgs/metron_alerts_details.png)

## Adding the  mysquid sensor
Now we will build a mysquid sensor from scratch using a grok parser. 
1. Open the Metron Management UI.
2. Click on the + button on the lower right corner to add a new sensor.
3. Enter the following:
```
	Name: mysquid
	Kafka Topic: mysquid
	Parser Type: Grok
```
Don't worry if you see the "No Matching Kafka Topic", the Kafka Topic will be created automatically on save.  
![Create My Squid Sensor](parsingsquid_imgs/mysquid_create_sensor.png)

4. Click to expand the Grok Statement.  Paste the sample squid raw log entry below into the Sample field. 

```
1528766038.123  70328 75.133.181.135 TCP_TUNNEL/200 420 CONNECT data.cnn.com:443 - HIER_DIRECT/2.20.22.7 -
```

![My Squid With Sample](parsingsquid_imgs/mysquid_grok_sample.png)

5. Copy and paste the grok expression below to the right of the MYSQUID grok expression name:

```
%{NUMBER:timestamp}[^0-9]*%{INT:elapsed} %{IP:ip_src_addr} %{WORD:action}/%{NUMBER:code} %{NUMBER:bytes} %{WORD:method} %{NOTSPACE:url}[^0-9]*(%{IP:ip_dst_addr})?
```

6. Press the Test button. The Preview section will update with the fields parsed from the raw log.  If there is an error and the preview does not appear, verify the sample and grok expression field values.   Adjust the sample or grok expression field values so the preview appears.

![My Squid Sensor](parsingsquid_imgs/mysquid_grok.png)

7. Click Save.
8. Verify that sensor name and topic name are "mysquid" with NO extra spaces or special characters.  
9. Click Save on the mysquid sensor.
10. The mysquid sensor appears in the management UI.  Click on the pencil icon to edit the mysquid sensor.  The mysquid configuration opens.
11. Scroll down to the Parser Config section.   
12.  In the enter field point, enter timestampField.
13. In enter value, enter timestamp.

![Setting the timestamp field](parsingsquid_imgs/timestamp_parser_config.png)

14. Click the Save button.

## Installing the mysquid index template

After events are enriched and triaged, metron stores the events in an index.  The index template specifies how to interpret the metron events and how to index strings using either a keyword or full text search.  

Open Solr and follow the instrutions in the corresponding section below.

### Creating a Collection in Solr


1. SSH into the metron host.   If you are logging in from windows, ssh using putty.

```
ssh -i <pem file> centos@mobius.local
```
2. Run the create_solr_collection command to create a new collection and install the collection schema.  The schema defines the types and search capabilities of each field in the events stored in the index.  

```
./create_solr_collection.sh mysquid
``` 
3. To see the definition of the schema use the more command on the metron host.  Press enter to advance one line.  Press space to advance one page.  To exit the more command enter 'q':

```
more /usr/hcp/current/metron/config/schema/mysquid/schema.xml
``` 

4. The solrconfig.xml file defines the configuration of the index.  The solr config does not vary by the index content.  When creating a new index, use the metron solrconfig.xml as is.

```
more /usr/hcp/current/metron/config/schema/mysquid/solrconfig.xml
``` 

5. In the browser, open solr collections interface to see the mysquid collection:
http://mobius.local:8983/solr/#/~collections
 
![Viewing collections in solr](parsingsquid_imgs/solr_collections.png)

## Starting the mysquid sensor

1. Return to the Metron Management UI.

2. Push the play button on the mysquid sensor to start the parser.

## Moving squid access.log lines to Kafka for processing by Metron

1. Enter the Nifi URL in your browser:
 http://mobius.local:9090/nifi/

2. The Nifi flow on the canvas tails the squid access.log and sends the lines to the PublishKafka.  PublishKafka breaks the log sample into individual lines and writes each line as a Kafka message to the squid topic.
![Original Nifi Flow](parsingsquid_imgs/nifi_01.png)

3. Right click on the PublishSquidToMetron processor and select Copy.

4. Right click on an empty area of the canvas and select Paste.  
![Pasted Nifi PublishSquidToMetron](parsingsquid_imgs/nifi_02.png)

5. Right click on the copy of PublishSquidToMetron and select Configure.

6. Click on the Settings tab and change the name to PublishMySquidToMetron.
![Change Processor Name](parsingsquid_imgs/nifi_04.png)

7. Click the Properties tab and change Topic Name to mysquid.
![Change Topic Name](parsingsquid_imgs/nifi_03.png)

8. Click Apply to save the changes.

9. Hover the cursor over the Read Squid Log processor until the connect icon appears.   Click and drag the flow to the middle of the PublishMySquidToMetron.  Drop the cursor and the Create Connection dialog appears.
![Create Connection](parsingsquid_imgs/nifi_05.png)

10. Click Add and make sure you Start the processor by clicking Start in the Operate panel.

11. The flow should look as below:
![Flow with Mysquid](parsingsquid_imgs/nifi_06.png)

12.  The flow can now write squid data to both topics or either topic.  It will be easier to see what is happening if we stop writing to the squid topic.   

13. Right click on PublishSquidToMetron and select Stop.

14. Right click on PublishMySquidToMetron and select Start.
![Mysquid Complete Flow](parsingsquid_imgs/nifi_complete_mysquid_started.png)

15. Set the browser to use the Metron proxy to start data flowing to the mysquid topic.  Enter google or a news site URL or any other URL that creates web request traffic to the squid proxy. 

16. When log lines are flowing to the mysquid sensor, the In field of the PublishMySquidToMetron processor should be greater than zero. 

![Mysquid Complete Flow](parsingsquid_imgs/nifi_input_annotated.png)

17. Open Metron Alerts UI.  In a few minutes the Metron Alerts UI show mysquid events. If you don't see event with mysquid source, go to the Metron Configuration UI, stop mysquid and start it again.  It might take a minute or two for events to start flowing.
 
![Alerts UI with mysquid events](parsingsquid_imgs/alerts_ui_mysquid.png)


18.  Congratulations!  You are now ingesting squid log events.  The next step is to add enrichments.

## Next Lab
[Enriching squid logs.](../03_EnrichingSquid/tutorial.md)

## References

[Metron Run Book](https://docs.hortonworks.com/HDPDocuments/HCP1/HCP-1.6.1/runbook/cybersecurity-runbook.pdf)

[Metron Source Code](https://github.com/apache/metron)
