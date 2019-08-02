---
title: Profiler Basics
author: Katy Brooks and Carolyn Duby
tutorial-id: 
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

# Profiler Basics

## Introduction



## Prerequisites

- Download the [Cloudera Cybersecurity Platform sandbox]
- Complete the previous tutorials for [hcp](..).

## Outline

- [Creating Profiles](#creating-profiles)
  - [Stellar Shell and Profile Debugger](#stellar-shell-and-profile-debugger)
  - [Create a Profile Execution Environment](#create-a-profile-exectution-environment)

## Creating Profiles

This section will describe how to create your very first “Hello, World” profile. It will also outline a useful workflow for creating, testing, and deploying profiles.

Creating and refining profiles is an iterative process. Iterating against a live stream of data is slow, difficult and error prone. The Profile Debugger was created to provide a controlled and isolated execution environment to create, refine and troubleshoot profiles.

Metron leverages the Stellar DSL for enhanced productivity. [Stellar Reference Guide](https://metron.apache.org/current-book/metron-stellar/stellar-common/index.html)

### Stellar Shell and Profile Debugger

Enter the same commands as shown below.

```
[root@node1 ~]# cd /usr/hcp/current/metron/
[root@node1 ~]# ./bin/stellar
Stellar, Go!
[Stellar]>>> %functions PROFILER
PROFILER_APPLY, PROFILER_FLUSH, PROFILER_INIT
```

Our simple hello-world profile will count the number of messages for each ip_src_addr. The SHELL_EDIT function will open an editor in which you can copy/paste the following profiler configuration.

```
[Stellar]>>> conf := SHELL_EDIT()
[Stellar]>>> conf
{
  "profiles": [
    {
      "profile": "hello-world",
      "onlyif":  "exists(ip_src_addr)",
      "foreach": "ip_src_addr",
      "init":    { "count": "0" },
      "update":  { "count": "count + 1" },
      "result":  "count"
    }
  ]
}
```

### Create a Profile Execution Environment

The Profiler will output the number of profiles that have been defined, the number of messages that have been applied, and the number of routes that have been followed. 

A route is defined when a message is applied to a specific profile.

If a message is not needed by any profile, then there are no routes. If a message is needed by one profile, then one route has been followed. If a message is needed by two profiles, then two routes have been followed.

```
[Stellar]>>> profiler := PROFILER_INIT(conf)
[Stellar]>>> profiler
Profiler{1 profile(s), 0 messages(s), 0 route(s)}
```

Create a message that mimics the telemetry that your profile will consume. This message can be as simple or complex as you like. For the hello-world profile all you need is a message containing an ip_src_addr field.

```
[Stellar]>>> msg := SHELL_EDIT()
[Stellar]>>> msg
{
	"ip_src_addr": "10.0.0.1"
}
```

Apply the message to your Profiler as many times as you like.

```
[Stellar]>>> PROFILER_APPLY(msg, profiler)
Profiler{1 profile(s), 1 messages(s), 1 route(s)}
[Stellar]>>> PROFILER_APPLY(msg, profiler)
Profiler{1 profile(s), 2 messages(s), 2 route(s)}
```
