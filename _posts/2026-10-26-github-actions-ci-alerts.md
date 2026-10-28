---
layout: post
title: "An alert in Slack or Discord when CI fails on main"
categories: [posts]
description: "Our GitHub Action opens an alert when a workflow fails and closes it when a later run passes, so a broken main shows once in the channel, not on every run."
---

When CI breaks on main, you want to know, once. Not a new message for every failed run, and not silence because nobody looks at the Actions tab.

![Diagram: CI fails and opens an alert with alias ci-main; a second failure is a repeat; the next passing run closes the alert with action close.](/assets/posts/card-github-actions-ci-alerts.webp){: width="1200" height="675" loading="lazy" decoding="async"}

Suitaar (our product) has a GitHub Action, [Suitaar/alert-action](https://github.com/marketplace/actions/suitaar-alert-opsgenie-compatible), on the GitHub Marketplace. It creates, closes and acknowledges alerts, adds notes and pings heartbeats from a workflow.

## Open an alert when the build fails

Keep the key of a Suitaar source in a repository secret, then add a step:

```yaml
- if: failure()
  uses: Suitaar/alert-action@9d9de90bc2e026c016563f0907d8d06e6f64681d  # v0.1.2
  with:
    api-key: ${{ secrets.SUITAAR_KEY }}
    alias: ci-main
    message: "CI failed on main"
```

The alias does the grouping: a second failure while the alert is open is a repeat, not a new alert. The channel shows one alert with a count.

## Close it when a later run passes

Add a step with `action: close` and the same alias, on success. The alert closes, and the channel shows it as resolved.

## A few details

- Use the full commit, as above: a tag can move, a commit cannot.
- A failed call to Suitaar is a warning, never a failed workflow, unless you set `fail-on-error: "true"`.
- The action tries again for about a minute if Suitaar does not answer.
- The key never appears in the log.
- It speaks the Opsgenie API: it can also send to Opsgenie, so moving from Opsgenie to Suitaar is a change of one input and the key.

## Also for scheduled jobs

A scheduled workflow can ping a Suitaar heartbeat at the end. When the schedule stops running, the pings stop, and Suitaar opens an alert.

Setup steps: [guide, GitHub Actions](https://suitaar.com/guide#github-actions).

The free plan stays free after the beta: 3 channels, no payment details.
