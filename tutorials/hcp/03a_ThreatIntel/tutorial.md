---
title: Applying Threat Intelligence to Squid Logs
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

# Applying Threat Intelligence to Squid logs

## Objectives

After this lab you will be able to:
 1. Load threat intelligence stored in a file.
 2. Load threat intelligence through a streaming writer. 
 2. Apply threat intelligence to a field in an event.
 
## Overview

Machine-Readable Threat Intelligence consists of a set of indicators of compromise in the current threat landscape.  For example, known malicious ip and domain names.  Metron automatically applies threat intelligence to fields of incoming events and sets the is_alert field to true if there is a match.

## Loading malicious ip list threat intelligence from a file

1. Ssh into mobius.local.  Perform the following commands in the centos home directory.

2. Download the ip blocklist.

```
[centos@mobius ~]$ curl https://zeustracker.abuse.ch/blocklist.php?download=ipblocklist | grep -v "^#" | grep -v "^$" > malicious_ips.csv
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1999  100  1999    0     0  16618      0 --:--:-- --:--:-- --:--:-- 16658
```

3. Verify that the malicious_ips.csv file contains a list of ip addresses.

```
[centos@mobius ~]$ head malicious_ips.csv 
101.200.81.187
103.19.89.118
103.230.84.239
103.4.52.150
103.7.59.135
104.247.219.41
109.127.8.242
109.229.210.250
109.229.36.65
113.29.230.24
``` 

4. Open a new file called malicious_ip_extractor_config.json in a text editor such as vi.

```
vi malicious_ip_extractor_config.json 
```

5. Add extractor config file json below to the file and save it.  The extractor config specifies how Metron converts the columns of the CSV into name value pairs in HBase.  The type is the type of threat intelligence, i.e. malicious_host vs malicious_domain. 

```
{
    "config" : {
       "zk_quorum" : "localhost:2181"
       ,"columns" : {
           "ip" : 0
       }
       ,"indicator_column" : "ip"
       ,"type" : "malicious_ip"
       ,"separator" : ","
    }
    , "extractor" : "CSV"
}
```
 
6. Verify the extractor config file.

```
[centos@mobius ~]$ cat malicious_ip_extractor_config.json 
{
    "config" : {
       "zk_quorum" : "localhost:2181"
       ,"columns" : {
           "ip" : 0
       }
       ,"indicator_column" : "ip"
       ,"type" : "malicious_ip"
       ,"separator" : ","
    }
    , "extractor" : "CSV"
}
```

7. Use the flatfile_loader.sh to load the malicious ips into HBase.

```
/usr/hcp/current/metron/bin/flatfile_loader.sh -e malicious_ip_extractor_config.json -t threatintel -i malicious_ips.csv -c t 
```

8. Display a list of malicious_ips loaded into HBase. 

```
[centos@mobius ~]$ head malicious_ips.csv 
101.200.81.187
103.19.89.118
103.230.84.239
103.4.52.150
103.7.59.135
104.247.219.41
109.127.8.242
109.229.210.250
109.229.36.65
113.29.230.24
```

9. Choose one of the ip addresses loaded into threat intelligence.  Open the Stellar shell and enter the command, ENRICHMENT_GET command below.   The first parameter ('malicious_ip') is the type of the indictor specified in the extractor config file.   Enter a domain in the malicious_ips.csv file as the second parameter.  The last two parameters are the table and column family specified as parameters to the flatfile_loader.  The value returned from the Stellar command should be json with an ip attribute that is equal to the ip passed in.

```
[centos@mobius ~]$ /usr/hcp/current/metron/bin/stellar -z localhost:2181
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/hcp/1.9.0.0-9/metron/lib/metron-profiler-repl-0.7.1.1.9.0.0-9.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/hdp/2.6.5.1100-53/hadoop/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
Stellar, Go!
Functions are loading lazily in the background and will be unavailable until loaded fully.
{es.clustername=metron, es.ip=node1:9300, es.date.format=yyyy.MM.dd.HH, parser.error.topic=indexing, update.hbase.table=metron_update, update.hbase.cf=t, es.client.settings={}, solr.zookeeper=mobius.local.localdomain:2181/solr, profiler.client.period.duration=1, profiler.client.period.duration.units=MINUTES, user.settings.hbase.table=user_settings, user.settings.hbase.cf=cf, bootstrap.servers=mobius.local.localdomain:6667, source.type.field=source.type, threat.triage.score.field=threat.triage.score, enrichment.writer.batchSize=15, enrichment.writer.batchTimeout=0, profiler.writer.batchSize=15, profiler.writer.batchTimeout=0, geo.hdfs.file=/apps/metron/geo/1553998428743/GeoLite2-City.tar.gz, asn.hdfs.file=/apps/metron/asn/1553998428743/GeoLite2-ASN.tar.gz}
[Stellar]>>> ENRICHMENT_GET( 'malicious_ip', '101.200.81.187', 'threatintel', 't')
{ip=101.200.81.187}
[Stellar]>>> quit
```
