---
layout: post
title: "Before you move off Opsgenie: a short checklist"
categories: [posts]
description: "Five things to check before Opsgenie stops on 5 April 2027: your renewal date, what sends to Opsgenie, your heartbeats, who needs paging, and a trial period."
---

Opsgenie stops on 5 April 2027. Whatever you move to, these five checks save surprises. Suitaar is our product, but this list applies to any replacement.

![Checklist: 1 check your renewal date, Opsgenie stops on 5 April 2027; 2 list everything that sends to Opsgenie; 3 list your heartbeats; 4 split paging from alerts in the team channel; 5 run both side by side for a while.](/assets/posts/card-before-you-move-off-opsgenie.webp){: width="1200" height="675" loading="lazy" decoding="async"}

## 1. Your renewal date

Atlassian allows a renewal only if the term ends before 5 April 2027. Data that is not moved to Jira Service Management is deleted on 5 April 2027. Check when your current term ends, so the move is not decided for you.

## 2. Everything that sends to Opsgenie

List every tool that sends alerts to Opsgenie, and check how each one connects:

- **Tools with a URL setting** (Alertmanager, Grafana, Zabbix, scripts on the Opsgenie SDKs, Zapier's OpsGenie app) can point at any service that speaks the Opsgenie Alert API.
- **Tools that send only to Opsgenie's own servers** (for example the Opsgenie integrations of Uptime Kuma, Healthchecks.io and Sentry) need another way in, usually the tool's own webhook.

That list decides how much work the move is.

## 3. Your heartbeats

Opsgenie heartbeats are easy to forget, because they are quiet until something stops. List them, with their intervals, and plan where each one moves.

## 4. Who needs paging, and who needs the channel

Opsgenie does two jobs for many teams: paging the person on call at night, and putting alerts in the team channel. Split them. Paging needs an on-call tool, with schedules, escalation and phone calls. The channel needs alerts that stay readable: repeats grouped, floods held back. Small teams often need only the second job.

## 5. Run both for a while

Most senders can send to two places. Send to Opsgenie and the new service side by side for a week or two, and compare. For Alertmanager, a route with `continue: true` does it (tested with Alertmanager 0.28.1): see [Moving Alertmanager off Opsgenie](/posts/moving-alertmanager-off-opsgenie/).

## Where Suitaar fits

Suitaar does the second job: it speaks the Opsgenie Alert API and sends alerts to Slack or Discord, with grouping, flood control and heartbeats. It does not page anyone at night; pair it with an on-call tool for that.

[Setup guide](https://suitaar.com/guide) · [Try it without an account](https://suitaar.com/demo)

The free plan stays free after the beta: 3 channels, no payment details.
