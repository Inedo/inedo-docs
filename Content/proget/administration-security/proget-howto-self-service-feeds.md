---
title: "HOWTO: Enable Self-Service Feeds"
order: 2
---

ProGet administrators control who can access and manage various feeds by granting or restricting permissions to users and groups. If these permissions or restrictions aren't set in advance, users must constantly contact an administrator to perform routine tasks.

Feed groups in ProGet allow administrators to enable self-service feeds so teams can act autonomously without affecting the rest of ProGet. When properly configured, a user can create and manage their own feeds without relying on an administrator.

This tutorial describes **how to use ProGet's Feed Groups feature** to configure which feeds a user can access and manage, which feed groups users can create new feeds in, and how to set up permissions for users to act autonomously in their assigned feeds.

You can immediately start making feed groups assuming you have have administrator control in ProGet. We recommend [connecting your company’s LDAP directory or Active Directory domain](/docs/installation/security-ldap-active-directory/various-ldap-integrated-authentication), but this feature can function normally with ProGet’s built-in user directory. We also recommend setting up user groups ahead of time.

## Step 1: Navigate to Settings > Manage Feed Groups

![ProGet settings page pointing out Users and Tasks ](/resources/docs/howtoselfservice_managefeedgroups.png){height="" width="50%"}

## Step 2: Click “Create Feed Group”
On the Manage Feed Groups page, click "Create Feed Group" at the bottom right of the page and a pop-up will open. 

## Step 3: Input Feed Group Information
In the pop-up window, based on the information you enter, a feed group is created in which users can trade.

For Name: enter the name of the team you want. For this demonstration, we will use "Devs-Chicago".

For Feeds: you have the option to associate all existing feeds with your feed group. Select the appropriate feeds from the dropdown menu.

![The "Create Feed Group" window displaying a group called 'Devs Chicago'](/resources/docs/selfservicefeedgroups-createfeedgroups-devschicago.png){height="" width="50%"}

Click "Save".

Feeds on the Feeds page are now divided between groups and “Ungrouped”.

![The ProGet feeds page displaying the Devs Chicago feed group has been separated into its own seciton.](/resources/docs/selfservicefeedgroups-feedpage-devschicagoandungrouped.png){height="" width="50%"}

## Step 4: Add Feed Group Permissions
Navigate to "Administration Overview" > "Manage Security" > "Tasks/Permissions" and click "add permission".

In the pop-up, select the desired user or user group ("Principals"), their feed group ("Feed or group"), and the "Manage Feed" task.

![The "Add Privilege" window in ProGet displaying Devs Chicago being granted a 'manage feed' permission.](/resources/docs/selfservicefeedgroups-addprivilege-devschicago.png){height="" width="50%"}

Click "Save Privilege".

“Manage Feed” allows the users of the group to create new feeds, manage their feed settings, add/push new packages, download and view packages, and [promote packages](/docs/proget/packages/package-promotion) to other feeds within the feed group.

:::(Warning)
Without any permissions, non-administrators cannot view any feeds (especially if anonymous access has been removed). 
:::

Permissions and restrictions can be very granular in ProGet, so we strongly recommend reading the full breakdown of them in our guide about [permissions](/docs/proget/administration-security). 

## After Feed Groups have been Configured

Users can now manage their own feeds within their approved groups without having to contact a ProGet administrator.

For example, Josh from Devs Chicago will only see the "Devs-Chicago" feed group. Note the pencil icon (✏) on the right side of the entry indicating that Josh can manage the feed (change settings, configure a drop path, etc.).

![The Feeds page displaying a Devs Chicago specific view, where they can only interact with Devs Chicago feeds.](/resources/docs/selfservicefeedgroups-feedpage-devschicagoview.png){height="" width="50%"}

Compare this to an administrator, who can see *and* manage all feeds. 

![The ProGet Feed page with an administrator view, so all feeds are displayed.](/resources/docs/selfservicefeedgroups-feedpage-adminview.png){height="" width="50%"}

When creating a new feed, users now see the "Feed group" line, but are limited to the feed groups they allow.

For example, Josh from Devs Chicago can only create a new feed in the "Devs-Chicago" feed group.

![The "Create New Feed" page with a Devs Chicago perspective](/resources/docs/selfservicefeedgroups-createnewfeed-selectfeedgroup-devschicago.png){height="" width="50%"}

Compare this to an administrator who can create a feed in any feed group, or ungrouped (“none”). 

![The "Create New Feed" window in ProGet with an administrator perspective.](/resources/docs/selfservicefeedgroups-createnewfeed-selectfeedgroup-admin.png){height="" width="50%"}

## Troubleshooting
### Permissions
Please read the “Troubleshooting Permissions” section of our granular ProGet permissions guide. 

### How to Check a User’s Permissions
Users may contact an administrator and ask why they cannot perform a particular function in ProGet. Users may encounter errors that occur because they do not have permission, so they must contact an administrator to continue.

![ProGet displaying an unexpected error saying the user does not have access.](/resources/docs/selfservicefeedgroups-unexpectederror-notauthorized.png){height="" width="50%"}

In these cases, an administrator can check what permissions or restrictions a user has via the settings page.

To check this, navigate to "Administration Overview" > "Manage Security" > "Tasks/Permissions".

Click "Test Privileges" from the Test Privileges dropdown menu.

In the pop-up window, enter the user you want to test and select the feed you want to test it with.

The window fills with a list of this user's permissions for the selected feed.

![The test privileges window in ProGet displaying all permission a user in the Devs Chicago feed group has.](/resources/docs/selfservicefeedgroups-testpermissions-josh.png){height="" width="50%"}

### Delete a Feed Group
Only administrators can delete a feed group. Non-administrator users (e.g. Josh) cannot access the Administration Overview page of ProGet and therefore cannot delete feed groups.

Navigate to "Manage Feed Groups" on the Administration Overview page.

On the Manage Feed Groups page, you will find a list of all feed groups.

![The ProGet Manage Feed Groups page displaying a list of all feed groups. ](/resources/docs/selfservicefeedgroups-managefeedgroups-list.png){height="" width="50%"}

Click the ❌ of the feed group you want to delete.

The deleted group’s feeds will now be moved to “Ungrouped”.

![The ProGet feed page showing how a deleted feed group has moved the feeds of that group to "ungrouped"](/resources/docs/selfservicefeedgroups-feedpage-deletedfeedgroup.png){height="" width="50%"}




