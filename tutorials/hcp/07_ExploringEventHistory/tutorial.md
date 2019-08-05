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

# Apache Zeppelin and Spark

[Apache Zeppelin](https://zeppelin.apache.org/) is an open source web based notebook for recording and sharing analytics and visualizations of event data stored in both HDFS and the index.  Zeppelin offers a number of interpeters including shell commands, Apache Spark and SQL queries.  Different interpreters can be combined in the same notebook as can python, scala and R code.

[Apache Spark](https://spark.apache.org/) is a distributed analytics engine capable of analyzing the entire haystack and building machine learning models. Spark supports scala, python, java, and R programming languages.

Spark code included in a notebook records commands for common cyber security analytics tasks such as dashboard visualizations, investigation run books, and threat hunting queries and visualizations.

## Exploring a Zeppelin Run Book 

1. Open Ambari in the browser:  
http://mobius.local:8080/

2. Select Zeppelin Notebook on the left hand side of the browser window.

3. Pull down the Quick Links menu to the right of the Summary and Configs tab and select Zeppelin UI.

![Starting Zeppelin](assets/01_starting_zeppelin.png)

4. The Zeppelin page appears.  Click the Login button on the upper right side of the page.

![Zeppelin No User](assets/02_zeppelin_no_user.png)

5. Enter the Zeppelin credentials (admin/admin).  Click Login. 

![Zeppelin Login](assets/03_zeppelin_login.png)

6. The Welcome to Zeppelin page opens.  Click on the Auth Investigation notebook link.

![Zeppelin Select Notebook](assets/04_zeppelin_select_notebook.png)

7. The Auth Investigation notebook is an example of a run book to assist a SOC analyst when investigating an authentication alert.   A notebook consists of a set of paragraphs.   Each paragraph starts with a % followed by an interpreter name.   For example the first paragraph uses the shell interpreter.  The second paragraph uses the spark2 interpreter.  The third paragraph uses the spark2.sql interpreter.  Following the interpreter is some commands or code in the language required by the interpreter.  

![Auth Investigation Notebook](assets/05_auth_investigation_notebook.png)

8. For better readability, the notebook author can show or hide the commands and command output independently.  The first paragraph lists the auth events files stored in HDFS.  The output is hidden.  Click on the Show output icon to the right of the "Find auth events in HDFS paragraph".  

![Auth Investigation Notebook](assets/06_show_output.png)

9. Metron groups events together and writes the fully enriched and triaged events to HDFS files in the directory /apps/metron/indexing/indexed/<sensor name>.  The hdfs dfs -ls shell command lists the auth events stored in HDFS.   

![HDFS file list](assets/07_indexed_data_file_structure.png)

10. A quick way to explore the events is to load them into a Spark dataframe, create a temporary table, and use SQL to access the events in the table.  The spark.read.json function reads all the json auth event files in the /apps/metron/indexing/indexed/auth HDFS directory and loads them into a DataFrame.  The createOrReplaceTempView function creates a temporary Spark SQL table called auth_events.  Once the table is created, we can query the auth events using SQL.

Click the Run button to the right of the "Register auth events as table so we can query" paragraph to reload the temporary table.  

In the spark2.sql "Which users log into many distinct hosts?" paragraph, Zeppelin sends the query over the auth_events temporary table to Spark and displays the results in a table. 

![HDFS file list](assets/08_create_table_from_files.png)

11. Zeppelin supports other query result visualizations including bar charts, pie charts, line charts, area charts, line charts and scatter charts.   Scroll down to the two line charts "Distinct host logons by time" and "Number of Logons by Time".  

![Zeppelin graphs](assets/09_create_graphs.png)

12. Click on the Show editor button on the upper right of the "Distinct host logons by time" paragraph to show the SQL that produced the points in the line graph below.   The value of the user in the where clause is parameterized.  Zeppelin replaces ${user} in the query with the value entered in the user field text entry (U66).  Parameterized queries are helpful in run books or for threat hunting when you want to use the same visualization for different entities.  

To change the visualization, click on the tool bar below the user text entry field.

![Show graph sql](assets/12_show_graph_sql.png)

13. Scroll through the visualizations in the rest of the notebook.  Go to the bottom of the notebook.  You will see an empty new paragraph.  

![New paragraph](assets/10_new_paragraph.png)

14.  Enter the commands below and click Run.  The number of events stored in HDFS should display in a table below.   

```
%spark2.sql
select count(*) from auth_events
```

If you see the error below, scroll back up to the top of the notebook and click the run button on the "Register auth events as table so we can query paragraph".  Then run the query again:

```
Table or view not found: auth_events; line 1 pos 21
set zeppelin.spark.sql.stacktrace = true to see full stacktrace
```

![Enter Run query](11_enter_run_query.png) 

15. Notebooks automate many common analytics tasks.  Once the data is loaded into a Spark DataFrame, you can perform common queries for threat hunting or other explorations.  The data loaded into a dataframe can also be used for training machine learning models.

## Creating a Solr Banana Dashboard

Banana is another option for visualizing data stored in Solr indices.   

1. In the browser, enter the Banana url:

http://mobius.local.localdomain:8983/solr/banana

2. The Basic Dashboard with Pointers appears. 

3. Click on the New icon in the upper right.  Select Time-series dashboard.

![Banana 01](assets/banana_01.png)

4. The New Dashboard Settings dialog appears.   Enter mysquid in the Collection Name.  Enter timestamp_solr in the Time Field.  Click Create.

![Banana 02](banana_02.png)

5. The New Time Series Dashboard appears with default visualizations.  The dashboard consists of horizontal rows.  Each row has one or more panels that display data.

![Banana 03](assets/banana_03.png)

6. Locate Total Hits in the upper right of the dashboard.   The total hits is zero.  Click on the gear icon in the Total Hits panel. 

![Banana 04](assets/banana_04.png)

7. The Hits Setting dialog opens.

![Banana 05](assets/banana_05.png)

8. Click on the Panel tab.  

9. Enter guid in the Field. Click Close.

![Banana 06](assets/banana_06.png)

10. The Total Hits will now display the number of mysquid events in the time period for the dashboard.

![Banana 07](assets/banana_07.png)

11. The Time Window in the upper left controls the time period of the data displayed in the dashboard.  Time periods are relative to the current time by default.

![Banana 10](assets/banana_10.png)

12. Click on the disk icon to save the dashboard.  Enter squid in the text entry field.  Click the disk icon to save. 

![Banana 08](assets/banana_08.png)

13. To select the dashboard displayed, select the folder icon.  Click on the squid link at the bottom of the dialog. 

![Banana 09](assets/banana_09.png)

14. Check Auto-refresh and the dashboard will read the latest data from the index and update the visualization. 

![Banana 10](assets/banana_10.png)

15. Scroll to the bottom of the window and click + Add a row. 

![Banana 11](assets/banana_11.png)

16. Enter Map in the Title and 300px in the Height.  Click Create Row.

![Banana 12](assets/banana_12.png)

17. Click the up arrow for the Map row to move the Map row above the Table.  Map should now be the second to last row.   Click Close.

![Banana 13](assets/banana_13.png)

![Banana 14](assets/banana_14.png)

18. An empty row now appears underneath the Event Counts histograms.   

![Banana 15](assets/banana_15.png)

19. Click Add panel to emptry row.  

20. Click the + to the right of Panels. 

![Banana 16](assets/banana_16.png)

21. Select map for the Panel Type.  Enter Destinations in Title.   Select 6 for the Span.  The Span controls the width of the panel.  A panel extending across the full width of the row is Span 12.  Enter enrichments.geo.ip_dst_addr.country.  Click Add Panel.  

![Banana 17](assets/banana_17.png)

22. Select sunburst for the Panel Type.  Enter Cities in the Title.  Select 6 for the Span.  Enter enrichments.geo.ip_dst_addr.country,enrichments.geo.ip_dst_addr.city in the Facet Pivot String. Click Add Panel. 

![Banana 18](assets/banana_18.png)

23. Click Close.

![Banana 19](assets/banana_19.png)

24. The dashboard now shows the location of the events on a map and in a sunburst.  The countries with more events have darker colors.  Hover over the country and the number of events destined to the country pops up. 

![Banana 20](assets/banana_20.png)

![Banana 21](assets/banana_21.png)

25. Hover over the inner ring of the sunburst to see the breakdown of the number of events destined for each country.  The outer ring of the sunburst shows the breakdown of the events destined for each city.

![Banana 22](assets/banana_22.png)

26. Go to the top right and click the save button.

27. Add a new row called Bytes and move it above the Table. Click Close.

![Banana 23](assets/banana_23.png)

28. Scroll the dashboard up and locate the new empty row.  Add a histogram panel.   Enter Bytes for the Title.  Enter 12 in the Span.  For Mode select values.  Enter Value Field bytes.

![Banana 24](assets/banana_24.png)

29. Scroll down to Chart Settings.  Check Lines and uncheck Bars.   Click Add Panel.

![Banana 25](assets/banana_25.png)

30. A line graph of the bytes over time is now shown on the dashboard.  

![Banana 26](assets/banana_26.png)

31.  Click Save to save the Dashboard.  
   

## Using a Business Intelligence Tool  

Many business intelligence tools include connectors to Solr, Elastic Search and Spark SQL.  For example, [ZoomData](https://www.zoomdata.com/) is convenient for creating visualizations and dashboards from Metron events with no programming.   

![Zoom dashboard](assets/00_zoom_dashboard.png)

# References

## Tutorials
[Spark Dataframe and DataSet Tutorial](https://hortonworks.com/tutorial/dataframe-and-dataset-examples-in-spark-repl/)

[Getting started with Apache Zeppelin Tutorial](https://hortonworks.com/tutorial/getting-started-with-apache-zeppelin/)

[Intro to Machine Learning with Apache Spark and Apache Zeppelin Tutorial](https://hortonworks.com/tutorial/intro-to-machine-learning-with-apache-spark-and-apache-zeppelin/)

## Documentation
[Apache Spark Component Guide](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.5/bk_spark-component-guide/content/ch_introduction-spark.html)

[Apache Zeppelin Component Guide](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.5/bk_zeppelin-component-guide/content/ch_overview.html)
