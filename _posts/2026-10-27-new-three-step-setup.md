---
layout: post
title: "New: a three-step setup after your first login"
categories: [updates]
description: "New teams now get a three-step setup: add a Slack or Discord webhook, add a source and copy its key, and send a test alert. Suitaar connects them for you."
---


When you log in to Suitaar for the first time, a short setup now takes you to your first alert in your team's chat.

1. **Choose where your alerts go.** Add a Slack incoming webhook or a Discord webhook. Suitaar sends a test message.
2. **Choose where your alerts come from.** Add a source for Alertmanager, Grafana or another tool, and copy its key. Suitaar connects the source to your channel.
3. **Send a test alert.** It shows up in your channel. If it does not, the page says what to check.

![Setup step 1 of 3, "Where should alerts go?": choose Slack or Discord, the steps to get the webhook URL, a field for the webhook URL and a name such as #ops, and a Send a test message button.](/assets/posts/setup-step1.webp){: width="680" height="790" loading="lazy" decoding="async"}
*Step 1: choose where your alerts go.*

![Setup step 3 of 3, "Send a test alert": a preview of the P5 Suitaar test alert as it appears in #ops in Slack, with I see it and I do not see it.](/assets/posts/setup-step3.webp){: width="680" height="570" loading="lazy" decoding="async"}
*Step 3: send a test alert and look for it in your channel.*

Then change one URL in your tool, and your real alerts go to the same channel.

!["Your first alert is in #ops." Then "Now change the URL in Alertmanager", with the API key (hidden here), tabs for Alertmanager, Grafana, Uptime Kuma, Healthchecks.io, UptimeRobot, Sentry and Script or API, and the opsgenie_configs lines.](/assets/posts/setup-success.webp){: width="680" height="770" loading="lazy" decoding="async"}
*The last screen shows your key once, and the change for your tool.*

If you stop halfway, the overview shows "Finish setup" and the step you are on.

[Log in with Google or GitHub](https://suitaar.com/login) · [Setup guide](https://suitaar.com/guide)

The free plan stays free after the beta: 3 channels, no payment details.
