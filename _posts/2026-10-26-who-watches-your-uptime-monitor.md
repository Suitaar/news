---
layout: post
title: "Who watches your uptime monitor?"
categories: [posts]
description: "An uptime monitor tells you when a site is down, but not when the monitor itself stops. Let it ping a heartbeat, and silence becomes an alert."
---

An uptime monitor, for example Uptime Kuma, tells you when a site is down. But when the monitor or its server stops, nothing tells you. The channel goes quiet, and quiet looks like everything is fine.

![Diagram: Uptime Kuma checks your sites and pings a Suitaar heartbeat every minute; when Kuma stops, an alert goes to Slack or Discord. In our test, the alert came 73 seconds after Kuma stopped.](/assets/posts/card-who-watches-your-uptime-monitor.png){: loading="lazy" decoding="async"}

The fix is to turn it around: instead of something checking the monitor, the monitor proves it is alive. It pings a heartbeat on another service every minute. When the pings stop, that service raises an alert.

Suitaar is our product, and it has heartbeats for this.

## With Uptime Kuma: no cron job needed

1. On a source page in Suitaar, add a heartbeat `uptime-kuma`. Set the interval to 2 or 3 times the check interval of the monitor, and add a grace time. For a check each minute: an interval of 3 minutes and a grace time of 2 minutes.
2. In Uptime Kuma, add a monitor of the type HTTP(s), with a check interval of 60 seconds:

```
URL:     https://suitaar.com/api/og/v2/heartbeats/uptime-kuma/ping
Method:  GET
Headers: {"Authorization": "GenieKey <your Suitaar key>"}
```

Each check of this monitor is a ping. When Kuma or its server stops, the pings stop, and Suitaar opens an alert in the channel of the source.

In our test (Uptime Kuma 1.23.16, a heartbeat of 1 minute without a grace time), the alert came 73 seconds after the last ping. The first ping after the restart closed it.

## With another tool

Ping from a cron job on its server:

```sh
* * * * * curl -fsS -m 10 -H "Authorization: GenieKey <your Suitaar key>" \
  https://suitaar.com/api/og/v2/heartbeats/uptime-kuma/ping
```

This shows that the server runs, not that the tool runs. A ping answers 202 also for a name that does not exist, so check "Last ping" on the source page after 2 minutes.

## What this does not show

It shows that the monitor runs. It does not show that the monitor's own notifications work, for example a Slack webhook in Uptime Kuma that is no longer valid. If you want both, let Kuma also send its down and up alerts to Suitaar, with its own webhook.

Setup steps: [guide, who watches your uptime monitor](https://suitaar.com/guide#watch-uptime-monitor).

The free plan stays free after the beta: 3 channels, no payment details.
