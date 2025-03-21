---
title: "HOWTO: Configure Restricted Feeds"
order: 1
---


​ProGet lets you control access to various feeds by allowing or restricting which users and groups can view, publish and promote packages. There are many options for defining more detailed access rules, and this guide walks you through some common scenarios for creating restricted feeds.

To start, if you haven't already, [download ProGet](https://inedo.com/proget/download), and [create your feeds](/docs/proget/feeds/feed-overview).

## Step 1: Plan out Desired Access & Restrictions

Permissions and restrictions vary from organization to organization and team to team. In this article, we use three different NuGet feeds (unapproved-nuget, approved-nuget, and mycompany-nuget) configured for a [package approval workflow](https://blog.inedo.com/nuget/package-approval-workflow). 

This means that we need the following rules:
- **anyone can view and download packages** from the mycompany-nuget feed
- **Senior developers can promote** from the unapproved-nuget to the approved-nuget feed
- **Build servers can view and publish** from the mycompany-nuget feed

These three basic rules are relatively easy to implement.

### Advanced Permissions
Later in the article we'll add new access controls to be more granular using custom tasks.

- **Anyone can view packages** from all feeds
- **Developers can view and download packages** from all feeds
- **Developers cannot download packages** from the "Unapproved Nuget" feed

## Step 2: Ensure Users and Groups are Configured
You can create users and groups in ProGet, but it's much easier to [integrate LDAP/Active Directory and ProGet](/docs/installation/security-ldap-active-directory/various-ldap-ldap-active-directory) so that you don't have to manage users/groups for everyone who needs to login.

ProGet has three "pseudogroups" that you can also use to control access:
- **Authenticated** - all users who have logged into ProGet
- **Anonymous** - all users who access ProGet but aren't logged in
- **Everyone** - all users in all groups, whether anonymous or authenticated

For our example, we'll use the "Everyone" pseudogroup and the "Developer" and "Senior Developer" groups. These groups can be defined in ProGet or in Active Directory; creating permissions and restrictions works the same way.

:::(Info) (Best Practice: Use Groups)
Although you can configure permissions for individual users, we recommend using groups because it's easier to manage and change permissions over time.
:::

## Step 3: Configure Permissions

By default, only administrators have permissions in ProGet. This means that you must add permissions to allow other users to access ProGet.

This is done with tasks that essentially describe what users can do. For example, the built-in Manage Feed task allows "access to manage feed settings, delete packages, and overwrite packages"

To see which users and groups have which permissions, navigate to Admin (the gear icon) > Manage Security > Tasks/Permissions:

![Tasks Overview Page](/resources/docs/progetpermis-tasksoverview-unassigned.png)
 
On this page, permissions and restrictions are grouped according to task and scope.
* There are five built-in tasks (Administer, Manage Feed, Promote Packages, Publish Packages, and View & Download Packages), and you can add more as needed
* Scope refers to the feed or feed group to which the task applies

### Add Permissions for "Everyone"

Based on the desired access we defined in step 1, we need to create a permission that allows anyone to view and download packages from the mycompany-nuget feed.

To do this, click on "Add Permission" and enter the desired permission.

![Add Privileges for "Everyone"](/resources/docs/progetpermis-addprivilege-everyone.png)

Once this is added, anyone - whether they have signed up or not - can view and download packages in the mycompany-nuget. However, they can no longer access the "unapproved-nuget" or "approved-nuget" feeds.

### Add Permission for "Senior Developers"

We would also like to allow senior developers to move packages from the unapproved Nuget feed to the approved Nuget feed. This requires adding two permissions:
|Principal|Feed or Group|Task
|-|-|-
|Senior Developers|unapproved-nuget|View & Download Packages
|Senior Developers|approved-nuget|Promote Packages

After configuring these permissions, the Tasks page will look like this:

![Tasks Overview Page With Assigned Permissions](/resources/docs/progetpermis-srdeveloperspermissionsoverview.png)

## Step 4: Create API Key
[API keys](/docs/proget/api/apikeys) allow you to connect your ProGet feeds to other automated tools like a build server, and they work a bit differently than tasks.

Our desired access for build servers (from step 1) was to allow the mycompany-nuget feed to be displayed and published. To create an API key for this purpose, navigate to Admin > API Keys, click "create api key", and enter a name, description, and desired access.

![Create API Key for Build Server](/resources/docs/progetpermis-createapikey.png)

Although not required for the sample configuration, you can create several types of API keys:
* **System API keys** help automate the management and configuration of ProGet
* **Feed API keys** can be restricted to a single feed or a group and can only use feed-related APIs
* **Personal API keys** can be managed by ProGet users and embody each user's access to ProGet

After clicking Save API Key, the newly created API key is displayed on the API Key List page:

![API Key Generated](/resources/docs/progetpermis-apikey-generated.png)

### Viewing & Using API Keys

If you do not specify a value for the API Key field, ProGet will automatically generate a value for you after you click Save API Key.

To view an API key, click the Edit link. From there, you can copy and paste the key into your build server to give it access to ProGet and the mycompany-nuget feed.

## Step 5: Customizing Tasks (Advanced)

To enable more granular permissions to better model governance and compliance policies, you can [customize the tasks available in ProGet](/docs/proget/administration-security/creating-tasks) by navigating to Admin > Manage Security > Tasks / Permissions > select [Customize Tasks] from the "Test Privileges" dropdown menu.

![ProGet Built in Tasks](/resources/docs/progetpermis-builtintasks.png)

On this page, you can add, edit, and delete tasks.

:::(Info) (Best Practice: Don't Modify Built-in Tasks)
You can change ProGet's built-in tasks. However, future updates may change your description or add additional attributes as new features are added.
:::

Our example of desired access can be implemented with two new tasks: "View Packages" and "Download Packages. We can then grant permission and restrict who can perform these tasks.

### Create "View Packages" Task

To create a custom task, click "Add Task" and enter the name, description, and desired attributes.

![Create View Packages Task](/resources/docs/progetpermis-createviewpackagestask%281%29.png)

### Create "Download Packages" Task

This task will be similar identical to the View Packages task, and contain only a single task.

![Create Download Packages Task](/resources/docs/progetpermis-createdownloadpackagestask.png)

However, instead of using this task to give permissions, we will use it to restrict access.


## Step 6: Configure Permissions and Restrictions (Advanced)

The additional desired access rules we want to create are:
- **Everyone can View Packages** from all feeds
- **Developers can View & Download Packages** from all feeds
- **Developers cannot Download Packages** from the 'unapproved-nuget' feed

The last access control rule is a bit tricky: if we were to use permissions only, we would have to add "View & Download Packages" to all feeds except one. Instead, we can use a combination of a permission and a restriction to accomplish this.

You can add a restriction by clicking "Add Restriction" on the Tasks/Permissions page.

![Add Restriction for "Developers"](/resources/docs/progetpermis-adddeveloperrestriction.png)

In addition to this restriction, we'll need to add two new permissions:

|Principal|Feed or Group|Task
|-|-|-
|Everyone|all feeds|View Packages
|Developers|all feeds|View & Download Packages


After configuring these, the Tasks page will show all of access control rules.

![Tasks Overview After Configuration](/resources/docs/progetpermis-finalpermissions.png)

These access control rules are complicated, but so are the desired access requirements.


## Troubleshooting Permissions

To determine if a user has access to something in ProGet, all permissions from all their groups are combined.

A restriction takes precedence over a permission unless that permission is more detailed. For example, although developers are not allowed to download packages via the `unapproved-nuget` feed, you can add a user-based permission to allow an individual user to download packages. A user is more granular than a group, so the permission would override the restriction. The same is true for permissions and restrictions at the system, feed group, and feed level.

Since access control rules can get complicated, you can test access using the Test Permissions button.

![Test Task Privileges](/resources/docs/progetpermis-testprivileges.png)

This will show you what permissions a specific user has for any given feed.


## Next Steps
Permissions are very complex and can be very detailed. Therefore, it's important to plan in advance the groups, tasks, permissions, and restrictions you want to set up.

[Feed Groups](/docs/proget/feeds/feed-overview#configuring-multiple-feeds) can help simplify permissions by grouping feeds that require the same permission or restriction rules.

Remember that while there is no limit, too granular permissions and restrictions mean that you define a lot of permissions that will one day need to be changed.

Feed-level permissions are only available in paid versions of ProGet. Request [a free trial key for ProGet](https://my.inedo.com/) to set up your Restricted NuGet feed today.
