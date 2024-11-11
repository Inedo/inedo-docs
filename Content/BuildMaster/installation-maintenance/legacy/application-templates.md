---
title: "Application Templates"
order: 4
url-slug: "buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-application-templates"
---

:::(Error) (Application Templates were Removed in BuildMaster 2024)
This feature was mainly used in BuildMaster 6.2 and BuildMaster 7.0 to display template applications maintained by Inedo when a new application is created.

With the introduction of improved application creation in BuildMaster 2022, this feature is obsolete and was removed in BuildMaster 2024; see [Legacy (Deprecated) Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy) to learn more.
:::

An application template (not to be confused with an [setup template](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-concepts-setup-templates) or a [text template](/docs/executionengine/overview/executionengine-components-text-templating)) is an [exported application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts/buildmaster-applications-concepts-backup-restore) that you can use to create new applications.

### Inedo-provided Templates

We've built a number of application templates to help get you started, from `GitHub CI/CD` to `Import from TeamCity`. These templates are hosted on [proget.inedo.com](https://proget.inedo.com/feeds/Templates), which BuildMaster will automatically pull from when you go to the create new application page. 

### Templates Package Source 

Behind the scenes, the Create New Application page uses a [package source](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) named `Templates` to retrieve a list of available templates. You can change this package source to a feed into your instance of ProGet and use a connector to mix-and-match Inedo-provided templates with your own.

### Creating Your Own Templates
To create your own application, simply go to the Advanced Settings Tab in your application and export as a template. This requires that you configure your `Templates` package source to be an instance of ProGet that BuildMaster can publish packages to.

### Application Template Webinar
![BuildMaster Application Template Webinar](https://www.youtube.com/watch?v=uLc_1CWiwqU&ab_channel=Inedo)