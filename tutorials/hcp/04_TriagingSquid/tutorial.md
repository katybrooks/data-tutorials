---
title: Triaging Squid Events
author: James Evans Morantus and Carolyn Duby
tutorial-id: 
experience: Beginner
persona: 
source: Hortonworks
use case: 
technology: Apache Metron
environment: Sandbox
product: HCP
series: 
---

# Triaging Squid events

## Objectives

After this lab you will be able to:
1. Determine which squid events are alerts.
2. Assign a score to squid alerts using [typosquatting](https://metron.apache.org/current-book/use-cases/typosquat_detection/index.html) detection.
3. Find the highest score alerts using the Metron Alerts UI.

## Triaging Background

Security event triage rules determine which events require further follow up and which events can be archived without further investigation.   Metron will process many events every day so effective triage helps analysts focus on the most important events.  The two components of triage are:

1. Determine if the event is an alert.
2. If the event is an alert, assign a score.  If the event is not an alert, it is not scored.
For this example, we use a very simple triage rule to detect [typosquatting](https://en.wikipedia.org/wiki/Typosquatting), installing malicious web content using common domain name misspellings.
