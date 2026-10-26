---
layout: post
title: "Uptime Kuma, Healthchecks.io and UptimeRobot alerts in Slack or Discord"
categories: [posts]
description: "The Opsgenie integrations of Uptime Kuma, Healthchecks.io and UptimeRobot send only to Opsgenie. Their own webhooks can send to Suitaar instead: down opens an alert, up closes it."
---

Uptime Kuma, Healthchecks.io and UptimeRobot each have an Opsgenie integration. Those integrations send only to Opsgenie's own servers, with no URL setting, so they stop working when Opsgenie stops on 5 April 2027.

Each tool also has a plain webhook. Suitaar reads each tool's own webhook: down opens an alert, up closes it. Suitaar is our product; here is the setup for each.

## Uptime Kuma

1. Settings → Notifications → Setup Notification. Notification Type: **Webhook**.
2. Post URL: `https://suitaar.com/api/v1/webhooks/uptimekumapet`
3. Request Body: Preset - application/json.
4. Additional Headers: `{"Authorization": "Bearer <your Suitaar key>"}`
5. Click **Test**: Suitaar shows an info alert "Test from Uptime Kuma". Then turn the notification on for your monitors.

Pending and maintenance change nothing. Tested with Uptime Kuma 1.23.

![Uptime Kuma Setup Notification: type Webhook, Friendly Name Suitaar, Post URL https://suitaar.com/api/v1/webhooks/uptimekumapet, Preset application/json, Additional Headers {"Authorization": "Bearer a placeholder for your Suitaar key"}.](/assets/posts/uptime-kuma-webhook.png){: loading="lazy" decoding="async"}
*Uptime Kuma: the Webhook notification for Suitaar. Click Test, then turn it on for your monitors.*

## Healthchecks.io

1. Integrations → add a **Webhook**.
2. Under "Execute when a check goes down" and again under "Execute when a check goes up": method POST, URL `https://suitaar.com/api/v1/webhooks/healthchecksio`, request body `$JSON`, request header `Authorization: Bearer <your Suitaar key>`.
3. Click **Test**: Healthchecks.io sends a check "TEST" down and then up, so Suitaar opens and closes the alert "TEST is down".

The alert has the check's tags and its last ping. Grace, new, paused and started change nothing. Tested with Healthchecks.io 4.4 (self-hosted).

## UptimeRobot

UptimeRobot has webhooks on its Team and Enterprise plans.

1. Integrations → add a **Webhook**. URL to notify: `https://suitaar.com/api/v1/webhooks/uptimerobotcom`
2. Tick **Send default variables as POST parameters**.
3. Add the custom HTTP header `Authorization: Bearer <your Suitaar key>`.

A certificate or domain that expires soon opens its own low alert, which you close by hand.

## For all three

- The alerts are P3 (moderate). Add `?priority=high` (or `critical`, `low`, `info`) to the URL for another priority.
- Suitaar keeps only the fields it uses, never the rest of the tool's message.
- A monitor that goes down and up again and again stays one alert with a count, not a message every time.

Uptime Kuma and Healthchecks.io are part of our end-to-end test on production.

Setup steps: [guide, Uptime Kuma](https://suitaar.com/guide#uptime-kuma).

The free plan stays free after the beta: 3 channels, no payment details.
