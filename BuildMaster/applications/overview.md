---
title: Applications in BuildMaster
sequence: 60
show-headings-in-nav: true
---

Applications represent the components and services that you release in BuildMaster, and contain the pipelines, templates, variables, and plans used to perform releases. You can also use applications for security access control by granting and denying tasks to different users across applications.

### Creating a New Application {#creating-new-applications data-title="Creating a New Application"}

To go to he You can open the Create New Application page from the application selector drop down or the applications overview page.

{.attention .best-practice} Application Templates are only available in BuildMaster 6.2 (or BuildMaster 6.1.12+ with preview features enabled)

After clicking "New Application" you will be presented with a variety of options.

{.docs}
* **Blank Application** - create a new application with no pre-built templates
* **Clone Application** - create a clone of an existing application
* **Featured Templates** - a listing of the most commonly-used templates that we've built. This listing is driven by use-cases from our customers and is updated from time-to-time
* **User Templates** - a listing of templates created within your organization by exporting applications to a template. This listing will be maintained by your organization and will require a ProGet feed as a package source. See <a href="applications/import-export">application import and export</a>
* **All Templates and Samples** - this will be a full listing of all application templates and samples from the Templates feed

Selecting any of the templates will import the target template and will require you to fill out a few more customization settings on the Application Setup page.

### Cloning and Template Applications{#cloning-template-applications data-title="Cloning and Template Applications"}
There are times you may want to copy or "clone" an application, for example:
<ul class="docs">
    <li>
        <b>Templating</b> – using an application with a common set of variables and other configuration that can serve as a template for creating new applications
    </li>
    <li>
        <b>Refactoring</b> – instead of doing a major refactor on an existing application, you can clone it to a new application and modify the new application instead
    </li>
</ul>
<p>
    You can use the <a href="applications/import-export">application import and export</a> feature for this.
</p>

#### Legacy Application Clone Feature
Prior to BuildMaster v6, the only way to clone applications was through the feature now called legacy application cloning. Importing applications is strongly preferred, but if you need to copy <em>legacy features</em> or configuration such as <em>legacy deployment plans</em>, you can use this feature from Admin > Legacy Features.

### Deactivating and Purging Applications{#deactivating-puring-apps data-title="Deactivating and Purging Applications"}
You can deactivate or purge applications from Admin > Deactivate and Purge Applications.

Deactivating an application will cancel all of its releases, deactivate automatic builds, and remove it from navigation and other menus throughout BuildMaster, while keeping historical data for the application. You can still access a deactivated application by URL directly.

If you do not wish to keep any data or history for these applications, you may purge them from the system completely.

## Application Groups{#apps-groups data-title="Application Groups"}
Applications may be placed in an application group, which serves a few purposes:
<ul class="docs">
    <li>Visually grouping related applications in the user interface</li>
    <li>Common set of variables across applications</li>
    <li>Permissions that are shared across all applications in the group</li>
</ul>

Application groups are created from the application settings page, by typing in the name of a group that doesn't exist. Empty application groups are automatically deleted.

Once an application group has been created in this manner, you can use that group name when scoping variables (Admin > Variables) or access controls (Admin > Tasks).