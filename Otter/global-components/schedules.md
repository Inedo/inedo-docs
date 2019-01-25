---
title: Schedules
keywords: otter,schedules
show-headings-in-nav: true
---

## About Otter Schedules {#about data-title="About Schedules"}

Otter has a global list of named schedules that can be used to automatically run recurring jobs.

Recurring jobs using a schedule are run independently of the configuration drift setting of a server.

A schedule simply defines the time (or times) when a job should run. Once created, a schedule may be assigned to any number of recurring jobs.

## Creating and Managing Schedules {#creating data-title="Creating and Managing"}

Schedules can be created, editied, or deleted from the Administration -> Schedules page.

The following configuration is available for a schedule:

{.docs}
- **Name** - Identifies the schedule in Otter.
- **Days of week** - Specifies which days of the week when jobs are allowed to run.
- **Starting at** - The time of day when the job should run (or if **interval** is specified, the time of day when the job should *first* run).
- **Interval** - When specified, a job will run multiple times a day from the starting time to the ending time with this specified interval in between runs. Interval may be specified in hours and/or minutes (for example, 1h 30m).
- **Ending at** - When specified with **interval**, detemines the time after which no more jobs will be run for the day. This value is ignored if **interval** is not set. When an explicit end time is not specified but an interval is specified, jobs will run until the end of the day.
- **Schedule is active** - When not checked, recurring jobs which use this schedule will never run.

## Recurring Jobs {#recurring-jobs data-title="Recurring Jobs"}

A recurring job can be created on the Jobs page under the **Recurring** tab. A recurring job has all of the same configuration as a [one-off job](../core-concepts/jobs), but also includes a schedule that determines when the job will run.
