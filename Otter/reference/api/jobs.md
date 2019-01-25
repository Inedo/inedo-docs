---
title: Job Orchestration API Endpoints
subtitle: Job Orchestration API
keywords: otter,api,jobs
---

<style type="text/css">
    th {
        font-weight: normal;
        font-style: italic;
    }
</style>


The Job Orchestration API Endpoints offer a simple mechanism for triggering and checking the status of [orchestration jobs](../../core-concepts/jobs#orchestration); these should be used instead of the [Native API Methods](../api#native) when possible, as they are much easier to use and will likely not change.

As the name implies, these endpoints interact with [orchestration jobs](../../core-concepts/jobs#orchestration). For security and simplicity, they require that a [job template](../../core-concepts/jobs#templates) is created first. To create a remediation job, you can use the [configuration management](configuration-management#trigger-remediation) API.

A job template must be configured to allow API use, and each job template may specify an [API Key](../../administration/security#api-keys). Using this, you enable certain jobs to be created for low-security development server orchestration, and high-security production server orchestration.

### Trigger Job Endpoint {#trigger}

GET or POST ::/api/jobs/::{style="opacity: 0.5"}**trigger**?template=&laquo;template-name&raquo;&amp;key=&laquo;optional-secret-key&raquo; {.info}

Creates and triggers a job based on the specified template, returning a status of 200 (on success), 400 (missing or incorrect parameters), 404 (template not found), or 403 (invalid key), and a body containing only a *job token*.

Any key/value pairs that are passed in the POST body or as additional query string parameters will be treated as job-scoped variables, and may override any values specified in the template.

<tab-block>
<tab name="Simple GET">

{.info}
```
GET /api/jobs/trigger?template=hdars&key=secure123
```

```
Status: 200
Body: 471
```

</tab>
<tab name="POST with Variables">

{.info}
```
POST /api/jobs/trigger?template=ablast

TargetServer=ABLSV1&SendNotification=sdennis@example.com
```

```
Status: 200
Body: 1871
```

</tab>
</tab-block>

The returned token is *currently* the integer-based identifier used for jobs; however, please treat this as a string of arbitrary length, just in case the implementation detail changes to a GUID... or maybe even a [LongGuid](https://www.nuget.org/packages/Rubbishsoft.LongGuid/).

### Job Status Endpoint {#status}

GET or POST ::/api/jobs/::{style="opacity: 0.5"}**status**?token=&laquo;job-token&raquo;&amp;key=&laquo;optional-secret-key&raquo; {.info}

Gets the status of a job that was created with the [trigger](#trigger) endpoint, using the job token returned by that method. The API returns a status of 200 (on success), 400 (invalid parameters), or 403 (invalid key), and a body containing one of the following strings:

{.docs}
- `Pending` - indicates that the job dispatcher has not yet begun execution; unless the Otter service is disabled, it will be very rare to find a job in this status
- `Succeeded` - indicates that the job is complete (and did not end in Warning or Error state)
- `Warning` - indicates that the job is complete but ended in a Warning state
- `Error` - indicates that the job is complete but ended in an Error state
- `Executing` - indicates that the job has not yet completed

### Job Logs Endpoint {#logs}

GET or POST ::/api/jobs/::{style="opacity: 0.5"}**logs**?token=&laquo;job-token&raquo;&amp;key=&laquo;optional-secret-key&raquo; {.info}

Gets the logs of a job that was created with the [trigger](#trigger) endpoint, using the job token returned by that method. The API returns a status of 200 (on success), 400 (invalid parameters), or 403 (invalid key), and a body containing the *entire* set of logs produced by the job, with each entry prefixed with `DEBUG:`, `INFO:`, `WARN:`, or `ERROR:` and ending with a newline.

Note that the logs entries are returned in the order in which they were logged; because of Otter's [rich log scoping](../../execution-engine/statements-and-blocks#logs) and ability to execute many things [asynchronously](../../execution-engine/statements-and-blocks/general-blocks#asynchronous), this may not be the order you are used to seeing in the web application UI.
