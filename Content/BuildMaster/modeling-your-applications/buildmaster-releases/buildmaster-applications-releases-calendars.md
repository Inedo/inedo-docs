---
title: "Calendars"
order: 2
---

Expand BuildMaster's release coordination capabilities with Calendars and provide insight into planned organizational changes. Using Calendars in BuildMaster, you can:

* Track target deployment dates of your releases
* View past deployments
* Set an automated schedule for future releases
* Schedule additional dates for other workflow tasks

Navigate to the default Calendar by selecting the "Calendars" tab in the header. This will show all scheduled release deployment dates for all applications in your instance, including past deployment dates, additional configured dates and any automated dates for future releases. 

![](/resources/docs/buildmaster-default-calendar.png){height="" width="50%"}

You can switch between the default calendar and any custom calendars you have created using the dropdown at the top of the page. 

## Creating Custom Calendars
Creating a custom Calendar lets you view or filter deployment dates by application. 

To create a custom calendar, navigate to "Administration" > "Manage Calendars" (Global Components). Then select "Create Calendar". 

![](/resources/docs/buildmaster-calendar-create.png){height="" width="50%"}

Creating a custom calendar will let you set the name, and select which applications it will show, and several additional options:

#### Past Deployments
Set how past deployments are shown on the calendar:

| Option | Description |
| --- | --- |
| `Show Final Stage` | Shows only past deployments to the final release pipeline stage (e.g. `Production`)
| `Show All Stages` | Shows all past deployments to all stages of a release pipeline |
| `Do Not Show` | Hide all past deployments |

#### Future Deployments
Set how future deployments are shown on the calendar:

| Option | Description |
| --- | --- |
| `Show All` | Show all deployment dates set in active releases |
| `Do Not Show` | Hide all deployment dates set in active releases  |
| `Other` | Lets you show/hide the following in active releases:<br>- `Scheduled Deployments`: Any deployments scheduled on a pipeline<br>- `Generated Dates`: [Automated](#automated-release-dates) release target dates <br>- `Target Dates`: All release targets and any [Additional dates](#adding-additional-dates) |

#### Hide/Show Weekends
Set how weekends are shown on the calendar. When selected, Saturday and Sunday will be hidden on the Calendar. Any dates scheduled on a Saturday will instead be shown the previous Friday, and any dates scheduled on a Sunday will be shown on the following Monday.

Custom calendars can be selected from the dropdown box on the Calendar page:

![](/resources/docs/buildmaster-custom-calendar-select.png){height="" width="50%"}

## Release Target Dates
Target dates are now a built-in feature are promoted when creating a release. If entered, the target release will appear on the calendar. 

![](/resources/docs/buildmaster-release-set-target.png){height="" width="50%"}

Target dates can also be added or edited when editing a release:

![](/resources/docs/buildmaster-release-edit-target.png){height="" width="50%"}

## Automated Release Dates
You can configure your release calendar to add automated dates to predict your future release schedule. Once set on a pipeline, they will be calculated based on the previous release's target date.This is especially useful for routine releases that are deployed on a regular schedule. 

To configure automated dates, navigate to the pipeline associated with your release, and select "Edit Details" under "Pipeline Details"

![](/resources/docs/buildmaster-pipeline-details-edit.png){height="" width="50%"}

Set the interval of days between release dates under "Target Iteration Days", and select "Save Details" 

![](/resources/docs/buildmaster-release-edit-iteration.png){height="" width="50%"}

Automated release dates will be pre-populated when creating release. They will also be pre-populated when releases are automatically created (e.g. through git branch monitors or pipeline event listeners). Automated release dates will appear as dotted boxes on the Calendar. 

![](/resources/docs/buildmaster-calendar-auto-dates.png){height="" width="50%"}

## Adding Additional Dates
You can create additional target dates beyond the default release target. In addition to deployment dates, teams may wish to track other pipeline milestones, such as build completion, QA, and production readiness.

Additional dates can be added by navigating to a release pipeline, selecting "Edit Details" under "Pipeline Details", and then selecting "Manage Date Prompts".

![](/resources/docs/buildmaster-calendar-manage-date-prompts.png){height="" width="50%"}

By selecting "Add Date Prompt" you will be able to name the date, how many days after the release target date it will be scheduled, (or before, by entering a negative number, e.g. `-3`) and the duration that this additional prompt will last.

![](/resources/docs/buildmaster-calendar-add-date-prompt.png){height="" width="50%"}

These will be pre-populated when creating a new release. Note that additional dates will **not** be generated when a release target date is not set. 

![](/resources/docs/buildmaster-calendar-qa.png){height="" width="50%"}

Additional dates can also be added or edited when editing a release.

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