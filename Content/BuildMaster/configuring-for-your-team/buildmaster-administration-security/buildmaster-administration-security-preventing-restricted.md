---
title: "Preventing Deployment to Restricted Servers"
order: 2
---

Because a deployment script can reference a server by name, users who have permission to edit a deployment script could easily reference a restricted server (such as production), thus defeating any deployment restriction policies. To prevent this, you can elect to "restrict deployments" to that server.

::: (info) (Best Practice)
To enable this feature, you must go to All Settings and enable server restrictions.
:::

At runtime, when a restricted server is requested, if the server is not associated with the targeted environment (or, if no environment was specified on the pipeline target), then a runtime error will occur. This allows you to grant a wide array of permissions while keeping narrow restrictions, such as:

 - permission to edit plans, pipelines, and deploy builds
 - restriction to deploy builds to production

:::(Error)
This can be unexpected and frustrating behavior, and we generally don't recommend it except for when there's a need for restricted servers.
:::