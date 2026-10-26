---
layout: post
title: "Released: Sentry, Uptime Kuma, Healthchecks.io and UptimeRobot, and a GitHub Action"
categories: [updates]
date: 2026-10-24 18:00:00 +0000
description: "Suitaar now accepts Sentry, Uptime Kuma, Healthchecks.io and UptimeRobot webhooks, has a GitHub Action, and serves the Opsgenie API at ogapi.suitaar.com."
---

These are live for every team since 23 October 2026.

![New in Suitaar, live since 23 October 2026: Sentry, uptime tools (Uptime Kuma, Healthchecks.io, UptimeRobot), a GitHub Action, and the ogapi host for the Go SDK and Terraform.](/assets/posts/card-released-sentry-uptime-tools-github-action.png){: loading="lazy" decoding="async"}

## Sentry

Sentry can send to Suitaar through an internal integration (a webhook). A new issue opens an alert. When the issue is resolved or ignored in Sentry, the alert closes. Metric alerts open and close too.

Optional: add a Sentry token to the source, and closing the alert in Suitaar also resolves the issue in Sentry (sentry.io).

Setup: [guide, Sentry](https://suitaar.com/guide#sentry).

## Uptime Kuma, Healthchecks.io and UptimeRobot

All three can send to Suitaar with their own webhooks. Monitor down opens an alert; monitor up closes it. UptimeRobot has webhooks on its Team and Enterprise plans.

Setup: [guide, Uptime Kuma](https://suitaar.com/guide#uptime-kuma).

## GitHub Action

[Suitaar/alert-action](https://github.com/marketplace/actions/suitaar-alert-opsgenie-compatible) creates, closes and acknowledges alerts, adds notes and pings heartbeats from a workflow. For example: open an alert when CI fails on main, and close it when the next run passes.

Setup: [guide, GitHub Actions](https://suitaar.com/guide#github-actions).

## Tools that take only a host

Some tools let you change only the host of the Opsgenie API, not the path. For them, `ogapi.suitaar.com` serves the same API at its root. That covers the Opsgenie Go SDK and the Terraform provider for Opsgenie.

Setup: [guide, tools that take only a host](https://suitaar.com/guide#host-only).

## Tested end to end

We ran a real alert through each of these on production: Alertmanager, Grafana, the Opsgenie Go and Python SDKs, Uptime Kuma, Healthchecks.io and Sentry. Each one opened, acknowledged and closed an alert in Slack and Discord.

The free plan stays free after the beta: 3 channels, no payment details.
