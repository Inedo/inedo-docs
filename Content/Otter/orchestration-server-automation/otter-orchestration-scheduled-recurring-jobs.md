---
title: "Scheduled & Recurring Jobs"
order: 3
---

While [Jobs and Templates](/docs/otter/orchestration-server-automation/otter-jobs-templates) allow you to create self-service templates that let anyone run complex scripts, Scheduled and Recurring Jobs allow you to use jobs in Otter to automate routine and recurring tasks using the built-in scheduler or your existing, third-party scheduler. 

![](/resources/docs/Scheduled-and-reccuring-jobs.png){height="" width=""}

## Scheduling Jobs to Run in the Future 

When running a job (whether from a template, ad-hoc, or remediating drift), select a start time:  

- Run now 
- Specify date/time to run 

By specifying a date/time to run the job, you can avoid downtime during work hours and other high-use times. 

![](/resources/docs/scheduling-jobs-to-run-in-the-future.png){height="" width=""}

## Integrating with Your Existing Job Scheduler 

For complex job scheduling, you should integrate your preferred, dedicated job scheduler. 

Otter can work with any job scheduler by using the [Job Orchestration API](/docs/otter/reference/api/jobs). It can be as simple as using an HTTP post or PowerShell: 

```PowerShell
Invoke-WebRequest -URI "https://«otter-host»/api/jobs/trigger?template=«template-name»&key=«secret-key"
``` 

Creating a job from the API first involves [creating an API key](/docs/otter/reference/api/jobs) with the “Job Orchestration” permission.  

You can also restrict the job to a specific API key, which may be useful if you’re using different tools and scripts run different jobs in Otter. 

When triggering jobs from the API, the job template’s variable prompts will be used to validate input: 

- If a variable is marked "required" but is not passed as a parameter, an error will be raised 
- If you pass a parameter that _isn’t_ specified as a variable prompt, an error will be raised 

If you select “Allow new variables when creating a job,” you can pass in any number of variable name/values when running the job from the API. 

## Creating a Recurring Job 
You can create a recurring job by navigating to Jobs > Upcoming & Recurring > Create Recurring Jobs. 

You can alternatively navigate to the script you’d like to run and select “Create a Recurring Job” from that script. 

![](/resources/docs/create-a-recurring-job.png){height="" width=""}

Like ad-hoc jobs, you must specify the script to run and server targeting for recurring jobs. 

### Schedule a Recurring Job 

If you need to run a job more than once in the future, use a recurring job. This can avoid down-time during work hours and other interruptions. 

There are four options for scheduling recurring jobs: 

- every day 
- every week 
- every month 
- custom 

![](/resources/docs/schedule-a-recurring-job.png){height="" width=""}

Custom schedules are defined with a Quartz-flavored Cron expressions. These can be a bit complex ([see cron expression syntax](https://en.wikipedia.org/wiki/Cron#CRON_expression){target="_blank"}), but if you search online for "cron expression generators" you may find a tool you like. For example: [https://freeformatter.com/cron-expression-generator-quartz.html  ](https://freeformatter.com/cron-expression-generator-quartz.html){target="_blank"}

### Variables for a Recurring Job  

Variables are used as parameters for the script before its executed as a recurring job. You can enter variable name/values, with one variable on each line. 

![](/resources/docs/variables-for-a-recurring-job.png){height="" width=""}

## Remediating Drift on a Recurring Basis 

A lot of configuration requires reboots or require resource-intensive operations. It therefore may be useful to remediate drift on testing environments on a nightly basis, as to not interrupt QA teams during the day. 

You can also create a recurring job that will remediate drift. Navigate to Jobs > Upcoming & Recurring > Create Recurring Job and select “Remediate Drift on a Recurring Basis.” 

You can target servers directly (by name) or indirectly (by roles/environments). 

![remediating-drift-on-a-recurring-basis](/resources/docs/remediating-drift-on-a-recurring-basis.png){height="" width=""}

 