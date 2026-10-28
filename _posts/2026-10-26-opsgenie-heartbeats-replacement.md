---
layout: post
title: "Opsgenie heartbeats: what replaces them"
categories: [posts]
description: "Opsgenie heartbeats tell you when a job stops running. Suitaar keeps the same ping URL format, so a cron job that pings Opsgenie needs only a new URL and key."
---

Some failures make no noise. A backup job that stops running sends no error; it just stops. Opsgenie heartbeats catch that: a job pings Opsgenie when it runs, and Opsgenie raises an alert when the pings stop. When Opsgenie stops on 5 April 2027, those heartbeats stop too.

Suitaar is our product. It has heartbeats that use the same ping path as Opsgenie, so moving a job is a change of URL and key.

## How a heartbeat works

1. On a source page in Suitaar, add a heartbeat with a name, an interval and a grace time. For a nightly backup: the name `nightly-backup`, an interval of one day, and a grace time that fits how long the job may run late.
2. At the end of the job, ping it:

```sh
0 2 * * * /usr/local/bin/backup.sh && curl -fsS -X POST \
  -H "Authorization: GenieKey <your Suitaar key>" \
  https://suitaar.com/api/og/v2/heartbeats/nightly-backup/ping
```

The `&&` matters: the ping runs only when the backup worked. When no ping comes within the interval and the grace time, Suitaar opens an alert in the channel of that source. The next ping closes it.

![Heartbeat table of a source. nightly-backup: every 1440 minutes, grace 30 minutes, status late. cert-renew: every 720 minutes, grace 0 minutes, status ok.](/assets/posts/heartbeats-table.webp){: width="720" height="229" loading="lazy" decoding="async"}
*The heartbeats of a source, with their last ping. When no ping comes within the interval and the grace time, Suitaar opens an alert and the status says late.*

## Moving an Opsgenie heartbeat

The ping path is the same as Opsgenie's: `/v2/heartbeats/<name>/ping`, with the key in an `Authorization: GenieKey` header. So:

- Add the heartbeat in Suitaar with the same name.
- Change the host in the ping URL to `https://suitaar.com/api/og`, and the key to your Suitaar key.

One difference: you make heartbeats on the source page in Suitaar. Of Opsgenie's Heartbeat API, only the ping works.

As with Opsgenie, a ping answers 202 also for a heartbeat name that does not exist. So after the first run, check "Last ping" on the source page.

## Other jobs that can ping

- **A Zap, a Make scenario or an n8n workflow**: a last step that sends a POST to the ping URL. See the guide for each.
- **Prometheus itself**: an alert that always fires pings a heartbeat, so you hear when Prometheus or Alertmanager stops.
- **An uptime monitor**: Uptime Kuma can ping a heartbeat as one of its own checks, so you hear when the monitor stops.

Setup steps: [guide, heartbeats](https://suitaar.com/guide#heartbeats).

The free plan stays free after the beta: 3 channels, no payment details.
