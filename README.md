# ut-uptime

An off-site liveness probe for Universal Till's public endpoints. It exists to
answer one question the rest of the monitoring cannot:

> **Is the whole site dark?**

Every other check runs on the homelab cluster or the NAS. Both sit in the same
room, on the same power circuit and the same internet connection — so a power
cut, router failure or ISP outage takes out the checkers along with everything
they were checking, and no alert is sent. **Silence from those checks is not
health.** This probe runs on GitHub's infrastructure instead, so it survives
exactly the failure they cannot report.

## How you get told

There is no alerting code here, and deliberately no credential to send one.
When the probe fails, GitHub emails the repository owner about the failed
scheduled run. That is the entire mechanism.

To prove that path works without waiting for a real outage, run the workflow
manually with **self_test** enabled: it adds a hostname that cannot resolve,
the run fails, and the email should arrive.

## Why this repository is public

Public repositories get unlimited free GitHub-hosted Actions minutes. Private
ones meter against a shared monthly quota — which ran out on 2026-08-08 and
halted every private workflow, deploys included. A monitor that stops working
when the budget does is not a monitor.

## What is deliberately not here

- **No secrets.** Nothing to steal, nothing to leak.
- **No internal addresses, hostnames or paths.** An off-site probe reaches the
  service over the internet, so it only ever needs public names — all of which
  are already in public DNS and Certificate Transparency logs. Nothing here
  discloses anything new.
- **No self-hosted runners, ever.** A self-hosted runner attached to a public
  repository lets any fork's pull request execute arbitrary code on the machine
  running it.
- **No deep health checks.** Whether the catalog is populated or the database
  is serving is checked from inside, where that can be seen properly. This
  probe only reports reachability.

## Known caveat

GitHub disables scheduled workflows in repositories with no activity for ~60
days. If this repo goes quiet, re-enable the schedule from the Actions tab.
