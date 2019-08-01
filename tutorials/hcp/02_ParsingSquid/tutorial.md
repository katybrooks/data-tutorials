---
title: Parsing Squid Logs
author: James Evans Morantus and Carolyn Duby
tutorial-id: 
experience: Beginner
persona: 
source: Hortonworks
use case: 
technology: Apache Metron
release: 
environment: Sandbox
product: HCP
series: 
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
![Metron Alerts Squid](images/metron_alerts_squid.png)

If you click in the space between the columns, the full details of the event will open on the right side of the browser.
![Metron Alerts Details](images/metron_alerts_details.png)

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
<img src="images/mysquid_create_sensor.png" width="35%" height="35%" title="Create My Squid Sensor">
