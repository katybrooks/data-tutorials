---
title: Host Name Setup
author: Katy Brooks and Carolyn Duby
tutorial-id: 
experience: Beginner
persona: 
source: Hortonworks
use case: 
technology: Linux
release: 
environment: Sandbox
product: 
series: HCP
---

# Host Name Setup

## Introduction

In most cases this setup will be completed for you, but in case it hasn't or doesn't work we are providing the information so you can set it up. Setting up a host name allows us to use easier to remember and type phrases, such as mobius.local (in HCP tutorials), instead of using your IP address to navigate your browser.

## Prerequisites

There are no prerequisites for this tutorial.

## Outline

- [Linux (Unix)](#linux-unix)

## Linux (Unix)

Before you can set up your host name you need to know your IP address inside the sandbox you're running. Open terminal and enter `ifconfig` to see more information about your VM. There you should be able tyo locate your IP address. I recommend testing the IP with tthe port you intend to use before setting it to your host name.

When you are ready to continue fter testing it, go back to terminal and enter `sudo vi /etc/hosts` to open the hosts file. Hit `i` to edit the file and add a line `host_ip mobius.local` where *host_ip* is your IP address (*mobius.local* may be different if you are doing something other than the HCP tutorials).

![Hosts file](assets/hosts_file.png)

Once that is done hit escape to get back to the command line and type `:wq` and enter to save and exit the file. Now you should be able to 
