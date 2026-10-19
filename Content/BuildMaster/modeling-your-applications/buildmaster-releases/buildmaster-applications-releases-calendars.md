---
title: "Calendars"
order: 2
---

Expand BuildMaster's release coordination capabilities with Calendars and provide insight into planned organizational changes. Using Calendars in BuildMaster, you can:

* Track target deployment dates of your releases
* View past deployments
* Set an automated schedule for future releases
* Schedule additional dates for other workflow tasks 

## Viewing the Calendar
To access the default calendar in BuildMaster, select the "Calendars" tab.

![](/resources/docs/buildmaster-calendar-select.png){height="" width="50%"}

This view presents all scheduled release deployment dates for all applications in your instance, including past deployment dates, additional configured dates and any automated dates for future releases.

![](/resources/docs/buildmaster-default-calendar.png){height="" width="50%"}

### Creating Custom Calendars 
Creating a custom Calendar lets you view or filter deployment dates through a number of settings:

| Option | Description |
| --- | --- |
| Name | Preferred name of the custom calendar
| Applications | Select the applications to show in the custom calendar  |
| Past Deployments | Show/hide past deployments. Select from `Show Final Stage`, `Show All Stages`, or `Do Not Show` |
| Future Deployments | Show/hide future deployments. Select from `Show All`, `Do Not Show` or `Other`<br>`Other` Lets you filter `Scheduled Deployments`, `Generated Dates` and `Target Dates`|
| Hide Weekends | Show/hide weekends from the custom calendar |

To create a custom calendar, navigate to "Administration" > "Manage Calendars" (Global Components)

![](/resources/docs/buildmaster-administration-calendars.png){height="" width="50%"}

Then select "Create Calendar"

![](/resources/docs/buildmaster-calendar-select-create.png){height="" width="50%"}

From here configure your custom calendar, and then select "Create".

![](/resources/docs/buildmaster-calendar-create.png){height="" width="50%"}

Custom calendars can be selected from the dropdown box on the Calendar view:

![](/resources/docs/buildmaster-custom-calendar-select.png){height="" width="50%"}

## Creating Release Targets 
When creating a new release, set the release target by entering a date in the "Release Target" field.

![](/resources/docs/buildmaster-release-set-target.png){height="" width="50%"}

### Editing Release Targets 
To edit a a release target in an existing release, navigate to the release and select "edit" under "Details". Then change the release target under the "Release Dates" tab.

![](/resources/docs/buildmaster-release-edit-target.png){height="" width="50%"}

### Creating Automated Release Dates
You can configure your release calendar to add automated dates to predict your future release schedule. This is especially useful for routine releases that are deployed on a regular schedule. 

To configure automated dates, navigate to the pipeline associated with your release, and select "Edit Details" under "Pipeline Details"

![](/resources/docs/buildmaster-pipeline-details-edit.png){height="" width="50%"}

Set the interval of days between release dates under "Target Iteration Days", and select "Save Details" 

![](/resources/docs/buildmaster-release-edit-iteration.png){height="" width="50%"}

Automated release dates will appear as dotted boxes on the Calendar. 

![](/resources/docs/buildmaster-calendar-auto-dates.png){height="" width="50%"}

## Adding Additional Dates
You can create additional target dates beyond the default release target. In addition to deployment dates, teams may wish to track other pipeline milestones, such as build completion, QA, and production readiness.

To add an additional date, navigate to the pipeline associated with  your your release, select "Edit Details" under "Pipeline Details", and then select "Manage Date Prompts".

![](/resources/docs/buildmaster-calendar-manage-date-prompts.png){height="" width="50%"}

Then select "Add Date Prompt". From here you will be able to name the date, how many days after the target release date it will be scheduled, (or before, by entering a negative number, e.g. `-3`) and the duration that this additional prompt will last. Then select "Save".

![](/resources/docs/buildmaster-calendar-add-date-prompt.png){height="" width="50%"}

When creating a new release, the additional dates will be scheduled alongside the release target. 

![](/resources/docs/buildmaster-calendar-qa.png){height="" width="50%"}

## Legacy Calendars (BuildMaster 2025 and below)
For users upgrading to BuildMaster 2026, you can view any calendars you configured in previous versions by navigating to "Administration" > "Legacy Release Calendars". 

Calendars in BuildMaster 2025 and below can created within the Manage Calendars dashboard and can be categorized into three different types:
- Personal - Viewed and edited by only the user who created it
- Team - Shared among individuals, only users with permissions can edit
- Global - Configured by admins, and viewable to all named users in BuildMaster

Any number of calendars can be created, but are generally used by teams (i.e. QAteam, WebDev team, etc.), individuals, and organization-wide.

### Creating Views

Create views for each of your calendars to granularly filter and display only the information you need. Specify the application, pipeline, pipeline stage, and environment for each view, then filter by: release target date, scheduled deployments, build schedules, gate deployment windows, and past deployments. Use color coding to easily identify and differentiate each view.

### Calendar Dashboard 

The Calendar dashboard allows you to easily toggle through personal, team, and global calendars. You can quickly browse through release target dates, scheduled deployments, releases, and more; easily identify each view by their specific color designation.