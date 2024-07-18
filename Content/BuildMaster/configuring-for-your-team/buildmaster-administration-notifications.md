---
title: "Notifications & Event Listeners"
order: 3
---

:::(Internal) (LOW QUALITY)
This article is rubbish, but so is the underlying feature. Just some things to make the article much less horrible.
- compare/contacts wit pipeline events
- list events
- give more examples and describe fields
:::
Event listeners are useful for monitoring the actions and operations that occur in deployment scripts and pipelines in BuildMaster. Event listeners trigger specific actions or tasks (such as sending an email) when the specified event occurs.

BuildMaster has a handful of common built-in event listeners.

![event-listeners.png](/resources/docs/event-listeners.png){height="" width=""}

Event listeners can be added either as a post deployment step in a [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines){target="_blank"} or specific to user via the <em>My Event Listener</em> user drop down.

![myevent-listeners.png](/resources/docs/myevent-listeners%281%29.png){height="" width=""}

## Real-world Example
Organizations often use event listeners when a particular person owns an application or a group of applications. In this scenario, the application owner would receive an email when a build has been successfully deployed to production.
  
![listener-deployed.png](/resources/docs/listener-deployed.png){height="" width=""}

:::(Info)
The **Email Notification: Approval Required** event listener triggers on pipeline stage *completion*, not the start of a new pipeline stage. Because of this, when filtering to a specific pipeline stage, the name of the stage *before* the approval gate should be used, not the stage that is blocked by the gate.
:::
