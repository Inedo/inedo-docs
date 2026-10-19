---
title: "Calendars"
order: 2
---

Expand BuildMaster's release coordination capabilities with Calendars and provide insight into planned organizational changes. Using Calendars in BuildMaster, you can:

* Track target deployment dates of your releases
* View past deployments
* Set an automated schedule for future releases
* Schedule additional dates for other workflow tasks 

# Viewing the Calendar
To access the default calendar in BuildMaster, select the "Calendars" tab.

![](/resources/docs/buildmaster-calendar-select.png){height="" width="50%"}

This view presents all scheduled release deployment dates for all applications in your instance, including past deployment dates, additional configured dates and any automated dates for future releases.

![](/resources/docs/buildmaster-default-calendar.png){height="" width="50%"}

## Creating Custom Calendars 
Creating a custom Calendar lets you view or filter deployment dates through a number of settings:

| Option | Description |
| --- | --- |
| Name | Preferred name of the custom calendar
| Applications | Select the applications to show in the custom calendar  |
| Past Deployments | Show/hide past deployments. Select from `Show Final Stage`, `Show All Stages`, or `Do Not Show` |
| Future Deployments | Show/hide future deployments. Select from `Show All`, `Do Not Show` or `Other`<br>`Other` Lets you filter `Scheduled Deployments`, `Generated Dates` and `Target Dates`|
| Hide Weekends | Show/hide weekends from the custom calendar |

To create a custom calendar, navigate to "Administration" > "Manage Calendars" (Global Components)

![](/resources/docs/buildmaster-administration-calendar.png){height="" width="50%"}

Then select "Create Calendar"

![](/resources/docs/buildmaster-calendar-select-create.png){height="" width="50%"}

From here configure your custom calendar, and then select "Create".

![](/resources/docs/buildmaster-calendar-create.png){height="" width="50%"}

Custom calendars can be selected from the dropdown box on the Calendar view:

![](/resources/docs/buildmaster-custom-calendar-select.png){height="" width="50%"}

# Creating Release Targets 
When creating a new release, set the release target by entering a date in "

## Editing Release Targets 
Demonstrate with screenshots (go into release, edit)

# Adding Additional Dates
You can create additional dates beyond the default release target by navigating to your release and then selecting "" and the "" tab.

Default target date prompt 7 days after last target date 
Can add extra Demonstrate with screenshots e.g. QA 
Note these can be edited along with the target release date in the release Show screenshot

# Legacy Calendars (BM ver. 2025 below)
What was shown before
Note these can be found (Admin > Legacy Release Calendars (deprecated)) if upgraded



Expand BuildMaster's release coordination capabilities with Calendars and provide insight into planned organizational changes. Calendars are created within the Manage Calendars dashboard and can be categorized into three different types:


- Personal - Viewed and edited by only the user who created it
- Team - Shared among individuals, only users with permissions can edit
- Global - Configured by admins, and viewable to all named users in BuildMaster

Any number of calendars can be created, but are generally used by teams (i.e. QAteam, WebDev team, etc.), individuals, and organization-wide.


## Creating Views

Create views for each of your calendars to granularly filter and display only the information you need. Specify the application, pipeline, pipeline stage, and environment for each view, then filter by: release target date, scheduled deployments, build schedules, gate deployment windows, and past deployments. Use color coding to easily identify and differentiate each view.


## Calendar Dashboard 

The Calendar dashboard allows you to easily toggle through personal, team, and global calendars. You can quickly browse through release target dates, scheduled deployments, releases, and more; easily identify each view by their specific color designation.


Refer to the Legend to manage views and gain insight into views created by someone else in a shared calendar.

