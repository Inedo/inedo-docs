---
title: "Job Orchestration API"
order: 5
---

The Job Orchestration API Endpoints offer a simple mechanism for triggering and checking the status of [orchestration jobs](/docs/otter/reference/api/jobs); these should be used instead of the [Native API Methods](/docs/otter/reference/api#native-api-reference) when possible, as they are much easier to use and will likely not change.

As the name implies, these endpoints interact with [orchestration jobs](/docs/otter/reference/api/jobs). For security and simplicity, they require that a [job template](/docs/otter/orchestration-server-automation/otter-jobs-templates) is created first. To create a remediation job, you can use the [configuration management](/docs/otter/reference/api/configuration-management) API.

A job template must be configured to allow API use, and each job template may specify an [API Key](/docs/otter/configuring-for-your-team/otter-administration-security-api-keys). Using this, you enable certain jobs to be created for low-security development server orchestration, and high-security production server orchestration.

### Trigger Job Endpoint
```
GET or POST /api/jobs/trigger?template=«template-name»&key=«optional-secret-key»
```

Creates and triggers a job based on the specified template, returning a status of 200 (on success), 400 (missing or incorrect parameters), 404 (template not found), or 403 (invalid key), and a body containing only a *job token*.

Any key/value pairs that are passed in the POST body or as additional query string parameters will be treated as job-scoped variables, and may override any values specified in the template.

#### Simple GET
```
GET /api/jobs/trigger?template=hdars&key=secure123
```

```
Status: 200
Body: 471
```

#### POST with Variables
```
POST /api/jobs/trigger?template=ablast

TargetServer=ABLSV1&SendNotification=sdennis@example.com
```

```
Status: 200
Body: 1871
```

The returned token is *currently* the integer-based identifier used for jobs; however, please treat this as a string of arbitrary length, just in case the implementation detail changes to a GUID... or maybe even a [LongGuid](https://www.nuget.org/packages/Rubbishsoft.LongGuid/).

### Job Status Endpoint 
```
GET or POST /api/jobs/status?token=«job-token»&key=«optional-secret-key»
```

Gets the status of a job that was created with the [trigger](#trigger) endpoint, using the job token returned by that method. The API returns a status of 200 (on success), 400 (invalid parameters), or 403 (invalid key), and a body containing one of the following strings:

- `Pending` - indicates that the job dispatcher has not yet begun execution; unless the Otter service is disabled, it will be very rare to find a job in this status
- `Succeeded` - indicates that the job is complete (and did not end in Warning or Error state)
- `Warning` - indicates that the job is complete but ended in a Warning state
- `Error` - indicates that the job is complete but ended in an Error state
- `Executing` - indicates that the job has not yet completed

### Job Logs Endpoint

```
GET or POST /api/jobs/logs?token=«job-token»&key=«optional-secret-key»
```

Gets the logs of a job that was created with the [trigger](#trigger) endpoint, using the job token returned by that method. The API returns a status of 200 (on success), 400 (invalid parameters), or 403 (invalid key), and a body containing the *entire* set of logs produced by the job, with each entry prefixed with `DEBUG:`, `INFO:`, `WARN:`, or `ERROR:` and ending with a newline.

Note that the logs entries are returned in the order in which they were logged; because of Otter's [rich log scoping](/docs/executionengine/otterscript/statements-and-blocks) and ability to execute many things [asynchronously](/docs/executionengine/otterscript/statements-and-blocks/general#asynchronous), this may not be the order you are used to seeing in the web application UI.