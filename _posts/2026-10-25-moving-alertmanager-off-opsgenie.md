---
layout: post
title: "Moving Alertmanager off Opsgenie by changing one URL"
categories: [posts]
description: "Opsgenie stops on 5 April 2027. If Alertmanager sends your alerts to Opsgenie, here is how to keep them in Slack or Discord by changing one URL."
---


Opsgenie stops on 5 April 2027. Atlassian allows a renewal only if the term ends before that date, and data that is not moved to Jira Service Management is deleted on 5 April 2027.

If your Prometheus Alertmanager sends alerts to Opsgenie today, this post shows how to keep those alerts going to Slack or Discord with one change in `alertmanager.yml`. Suitaar is our product; this is how to set it up, and what is different from Opsgenie.

## First: which job do you need?

Opsgenie does two jobs for many teams:

1. **Paging**: wake up the person on call, with schedules, escalation and phone calls.
2. **Alerts in the team channel**: every alert in Slack or Discord, where the team already works.

Small teams often need only the second job. Suitaar does only that job. If you need paging at night, pair Suitaar with an on-call tool.

## The change

Alertmanager has an Opsgenie receiver, `opsgenie_configs`, with an `api_url` setting. Suitaar speaks the same Opsgenie Alert API, so you change the URL and the key:

```yaml
receivers:
  - name: suitaar
    opsgenie_configs:
      - api_key: <your Suitaar key>
        api_url: https://suitaar.com/api/og/
        send_resolved: true
```

Alertmanager adds `v2/alerts` to `api_url` itself, so the URL ends at `/api/og/`.

You get the key from a source on the Suitaar dashboard. A source sends to the Slack or Discord channels that you choose for it.

## What happens to each alert

- **A new alert group** becomes one alert in Suitaar and one message in each channel.
- **Repeats** of the same group stay one alert with a count. The channel gets a short summary at the end of its group window, not a message for every repeat.
- **A resolved group** closes the alert, because of `send_resolved: true`. The channel shows that it is resolved.
- **A flood** of new alerts in one channel becomes 10 messages and one summary in that minute, and a message when the flood is over.

![Three Suitaar messages in Slack for one Alertmanager alert: the new alert, "Repeated 6 times in the last 10 minutes", and "Resolved".](/assets/posts/slack-new-repeat-resolved.png){: loading="lazy" decoding="async"}
*Slack: the new alert, one summary for its repeats, and a message when it is resolved.*

![One Suitaar message in Discord, first as a new alert, then edited to "Resolved" with the total count.](/assets/posts/discord-new-repeat-resolved.png){: loading="lazy" decoding="async"}
*Discord: Suitaar edits the first message for repeats and when the alert is resolved.*

Two optional settings help:

```yaml
route:
  receiver: suitaar
  group_by: ['alertname']
```

With `group_by: ['alertname']`, the title starts with the alert name, for example `[FIRING:1] HighCPU (warning)`, and alerts with the same name come as one alert with a count. To keep them apart, add a label such as `instance` to `group_by`.

With `update_alerts: true` in the receiver, Alertmanager also keeps the title and the description of the alert current, for example when more alerts join the group.

## Try it next to Opsgenie first

You do not have to switch in one step. Send every alert to both for a while, and compare:

```yaml
route:
  receiver: opsgenie
  group_by: ['alertname']   # keep your own group_by and timings here
  routes:
    - receiver: suitaar
      continue: true
    - receiver: opsgenie    # your existing routes stay here, below the suitaar route
receivers:
  - name: suitaar
    opsgenie_configs:
      - api_key: <your Suitaar key>
        api_url: https://suitaar.com/api/og/
        send_resolved: true
  - name: opsgenie
    opsgenie_configs:
      - api_key: <your Opsgenie key>
        send_resolved: true
```

The Suitaar route comes first, with `continue: true`, so Alertmanager sends each alert to Suitaar and then goes on to your other routes. If you already have a route tree, put only the Suitaar route at the top and keep everything else below it.

A few things to know:

- During the trial the two copies are separate. An Ack or a Close in Suitaar does not change the alert in Opsgenie.
- Keep `group_by`. Without it, Alertmanager puts every alert into one group, so different alerts arrive as one.
- A resolved alert arrives at Alertmanager's next group flush, after up to 5 minutes with the default `group_interval`. That delay comes from Alertmanager, not from Suitaar.

When you are happy, remove the Opsgenie routes and the Opsgenie receiver.

## Watch Alertmanager itself

Alerts come only while Prometheus and Alertmanager run. If they stop, the channel goes quiet, and quiet looks like everything is fine.

A rule that always fires can ping a Suitaar heartbeat. When the pings stop, Suitaar opens an alert:

```yaml
# Prometheus rule
groups:
  - name: meta
    rules:
      - alert: Watchdog
        expr: vector(1)
```

```yaml
# Alertmanager: put this route first under route.routes
route:
  routes:
    - matchers: [alertname = "Watchdog"]
      receiver: suitaar-heartbeat
      group_wait: 0s
      group_interval: 1m
      repeat_interval: 50s
receivers:
  - name: suitaar-heartbeat
    webhook_configs:
      - url: https://suitaar.com/api/og/v2/heartbeats/prometheus-watchdog/ping
        send_resolved: false
        http_config:
          authorization:
            type: GenieKey
            credentials: <your Suitaar key>
```

Add the heartbeat `prometheus-watchdog` on the source page first, with an interval of 5 minutes and a grace time of 2 minutes. In our test, the alert came about 10 minutes after Prometheus stopped, and about 7 minutes after the last ping when Alertmanager itself stopped.

## What is different from Opsgenie

- No on-call schedules, escalation, phone calls or SMS. Suitaar sends to your chat.
- Only part of the Opsgenie API: create, read, close and acknowledge an alert, add a note, read the status of a request, and ping a heartbeat. Fields that Suitaar does not use, such as `responders`, are accepted and ignored, so Alertmanager keeps working.
- You acknowledge and close an alert on its page in the Suitaar dashboard. Buttons in Slack and Discord come later.
- If Suitaar cannot be reached for a short time, Alertmanager sends again by itself, so the alert is late, not lost.

We test this path end to end on production: a real alert from Alertmanager through Suitaar to Slack and Discord, then acknowledged and closed.

## Try it

The full setup, also for Grafana, Sentry, uptime tools and scripts, is in the [setup guide](https://suitaar.com/guide#alertmanager). You can also [try Suitaar without an account](https://suitaar.com/demo).

The free plan stays free after the beta: 3 channels, no payment details.
