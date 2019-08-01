---
title: Getting Started
author: Katy Brooks and Carolyn Duby
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

# Getting Started with Metron

## Introduction

Apache Metron is a cybersecurity pipeline platform that ingests network activity and assesses the potential threat levelof these events. Metron is more adaptable than a standard rule based system because it profiles normal user activity and responding to activity that is abnormal for a specific user type. Enrichments can be added to the events to create a more robust record and are completely configurable by the Metron user.

This tutorial will show you how to open and navigate the Metron UIs and gives you an understanding of the information each shows.

## Prerequisites

- Download the [Cloudera Cybersecurity Platform sandbox](http://needlink.com)
- Make sure you can open Ambari in the sandbox at http://mobius.local:8080 

## Outline

- [Metron Management UI](#metron-management-ui)
- [Metron Alerts UI](#metron-alerts-ui)
- [Ambari UI](#ambari-ui)
- [Appendix A: Quick Reference](#appendix-a-quick-reference)

## Metron Management UI

The management UI shows the sensors configured in Metron and their operational state (running/stopped and latency). From the management UI you can create new sensors, view sensor configuration, browse raw logs sent by the sensor, and change the configuration of sensors.

Open Chrome and go to http://mobius.local:4200/ and enter metron as both the user and the password as prompted to log in.

![Metron Login Screen](assets/metron_login.png)

The Metron Management UI will open and show the sensors configured and their operational state (running or stopped and latency).
![Metron Management Screen](assets/metron_management.png)

## Metron Alerts UI

The Metron Alerts UI shows enriched and triaged events stored in the Metron index. From the Alerts UI you can filter and sort events and view event fields. You can also escalate, add comments to, or group events. Open your browser to http://mobius.local:4201/ and once again enter metron for both user and password.

![Metron Login Screen](assets/metron_login.png)

The alerts UI wil open, listing the events reported to Metron, and allowing you to search, filter, and view details of alerts.
![Metron Alerts Screen](assets/metron_alerts.png)

You can consult the [Viewing Alerts User Guide](https://docs.hortonworks.com/HDPDocuments/HCP1/HCP-1.6.1/user-guide/content/viewing_alerts.html) for more information on how to use the Alerts UI.

## Ambari UI

## Appendix A: Quick Reference
