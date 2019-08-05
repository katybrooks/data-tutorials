---
title: Exploring Event History - Dashboards and Run Books for Analysis, Threat Hunting and Investigations
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

# Exploring Event History - Dashboards and Run Books for Analysis, Threat Hunting and Investigations

# Objectives

After this lab you will be able to:

1. Describe how to access the events stored in the index and HDFS to create dashboards, analytics for threat hunting, and run books for investigations. 

# Background

After an event is fully enriched and triaged, Metron automatically stores the event in an index (Solr or Elastic Search) and in HDFS.  HDFS stores the entire history of events for the specified retention period.  The index typically stores several months of the most recent events.  The retention period for each storage mechanism is configurable and there are several cost effective options for storing and analyzing years of events.

Apache Metron and the Hadoop ecosystem offer many options for analyzing stored cybersecurity events.  For example:

1. Metron Alerts UI

2. Apache Zeppelin Notebooks using Apache Spark

3. Banana and Kibana Dashboards

4. Third party business intelligence tools 

# Metron Alerts UI

The Metron Alerts UI reads events from the index which provides a very flexible search API that quickly filters the haystack to a subset of events.  See the [Getting started](../01_GettingStarted/tutorial.md) lab for instructions on opening the Metron Alerts UI.  Once the UI is open, enter a simple or complex query in the UI and the filtered events will display in the UI.  Click on the headers to sort ascending or descending by an event field.  Complete the earlier labs to see the Alerts UI in action.

