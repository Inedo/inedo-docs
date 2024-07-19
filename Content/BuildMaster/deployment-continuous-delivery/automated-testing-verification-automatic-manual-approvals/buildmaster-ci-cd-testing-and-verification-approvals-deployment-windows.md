---
title: "Deployment Windows"
order: 2
---

Deployment windows allow you to restrict the days and times that builds may be deployed. They can be used in conjunction with manual deployments to enforce existing policies, or as part of a continuous delivery pipeline to ensure deployments occur within known times. Like [user approvals](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-user-approvals), a build may be forced to a stage even if it's not within the specified window. However, this requires a special action and a specific permission.

#### Configuring Deployment Windows

A deployment window consists of day of the week, as well as a start and end time.

![deploy-window.png](/resources/docs/deploy-window.png){height="" width=""}

When the end time is earlier than the start time, the window will carry over to the following day. For example, specifying "Sundays after 11:00pm and before 2:00am" will create a three-hour window from Sunday at 11:00pm until Monday at 2:00am.

You can specify deployment windows on the edit pipeline page.

![deploy-window-set.png](/resources/docs/deploy-window-set.png){height="" width=""}

When there are multiple deployment windows specified, they are treated like an "or" condition; that is, as long as one of the deployment windows is valid, a deployment may proceed.