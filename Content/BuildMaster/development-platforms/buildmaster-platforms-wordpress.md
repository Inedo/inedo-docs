---
title: "WordPress CI/CD"
order: 6
---


[WordPress](https://wordpress.org/) is a an open-source content management system (CMS) written in PHP. It is the most popular CMS on the market because it is simple, fast, reliable, and secure. Furthermore, WordPress removes the need to have a developer build your website or blog; it is typically managed instead by a marketing team. It is also simple to customize WordPress using their theme and plugin directories.


## WordPress in Organizations 

Since WordPress is open source, it can be hosted multiple ways. While some organizations host it on their internal or private cloud infrastructure, most leverage professional-grade WordPress hosting services such as [Pressable](https://pressable.com/), [Pantheon](https://pantheon.io/product/wordpress-hosting), and [WP Engine](https://wpengine.com/).

The most immediate benefit of professional WordPress hosting providers is that they free IT from the need to deal with WordPress hardware and server management. With minimal training, the marketing team can then take over: 
- Upgrading WordPress themselves via the control panel 
- Cloning WordPress instances to allow forthe following 
- Website testing  
    - Upgrades that don't impact production 
    - Testing of custom plugins and themes without affecting production 
    - Blue/Green development of complex new content

This ultimately helps transfer responsibility for the company's website from the IT department to the marketing department. However, it's important that IT is involved to some degree, both to facilitate customization and to enable best practices in modern marketing (aka MarketingOps).

## Enabling MarketingOps with WordPress and BuildMaster 

MarketingOps is a buzzword, and there is a standard definition for it. But like DevOps, MarketingOps is about enabling better communication and faster change through automation and process improvement.

WordPress simplifies the process of building and maintaining a website, and at its core it is very reliable and secure because it is maintained by the WordPress team. However, using the very popular third-party themes and plugins can be a big risk as they are managed by the community. Among the concerns are:

- Unknown maintenance of plugins and themes
- Unknown third-party tools used to build plugins or themes
- Unclear what data might be inadvertently shared with your end users

Since not all themes in the directory fit your look and feel, and to avoid security concerns, many organizations hire a development team (either within the organization or from a marketing agency) to build custom themes and plugins to meet their visual and security needs.

### Where BuildMaster Helps

BuildMaster facilitates both the technology side of MarketingOps—the process of creating, testing and releasing custom themes and plugins—**and** and the communications aspect of MarketingOps. BuildMaster is a communication *and* automation tool because it will:

- Allow developers to build and maintain WordPress customization, while your marketing team remains in charge of the site and content
- Separate your testing environments from your production environment so that different versions of themes and plugins can be built and tested before they go into production
- Automate communication between development and marketing
- Notify the right people or groups at the right time (for example, when a plugin is ready)
- Ensure that the right approvals are granted before changes are pushed between environments

## Configuration in Source Control

The first step is to put your WordPress theme or plugin code into source control.  Next, create a delivery pipeline for your custom Wordpress theme or plugin. BuildMaster integrates with a variety of [source control systems](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control/buildmaster-ci-cd-continuous-integration-server-source-control), but we'll use GitHub for our examples in this document.

### Example: Getting Your Code from GitHub

To begin a custom WordPress build in BuildMaster, create a GitHub project secure resource. After creating a GitHub project [secure resource](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials), you can get the source code as follows:

```
GitHub::Get-Source
(
    From: InedoWordPressTheme,
    CommitHash => $CommitId,
);
Set-BuildVariable CommitId
(  
    Value: $CommitId  
);
```

This will also create a build-scoped variable to record the commit used.

## Creating Artifacts 

A [build artifact](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) is essentially a .zip file that is captured at the first stage of a pipeline and then deployed to each subsequent stage. Build artifacts ensure that the exact same code is deployed to each environment.

To create an artifact that can be used in WordPress:

```
Create-Artifact InedoWordPressTheme
(
    From: $WorkingDirectory
);
```

### Leveraging Custom Build Tools 

Due to the languages used to extend WordPress (i.e., PHP, CSS, JavaScript, etc.), the code is not compiled. However, you can use custom tools to support your CSS and JavaScript development.

Some standard tools you can use are:
- CSS pre-compiler (LESS, SASS, etc.)
- CSS Linters (CSSLint, StyleLint, etc.)
- JavaScript linters (JSLint, ESLint, etc.)
- JavaScript trans-pilers (TypeScript, CoffeeScript, etc.)

These tools all have CLIs that are used to compile or parse the code. To run these in BuildMaster, simply use an `Exec` command.

An example using the [less.js for Windows](https://github.com/duncansmart/less.js-windows) Pre-Compiler:

```
set $LessFile = style.less;
Exec
(
    FileName: {LESS_HOME}\lessc,
    Arguments: $LessFile $Replace(.less, $LessFile, .css, true),
    WorkingDirectory: $WorkingDirectory
);
   
```

## Deploying Your Theme or Plugin 

Custom themes and plugins in WordPress are usually installed by manually uploading them through the admin screens. It is recommended to create a clone of your production site and test new changes to themes and plugins on the clone first to avoid interruptions and downtime in your live production environment.

BuildMaster streamlines this process by notifying the user responsible for testing each environment. This can be tracked using the `Perform-ManualOperation`, which halts deployment until the specified user certifies they performed the specified task. 

For example:
```
InedoCore::Send-Email
(
    To: "email@email.com, email2@email.com",
    Text: A new Theme is ready to be installed in $Environment
);

Perform-ManualOperation
(
    Name: Install Theme in $Environment
);
``` 

### Automating Using WP-CLI

WordPress has added official support for a command line interface (CLI). The [WP-CLI](https://make.wordpress.org/cli/handbook/) can be used to automate the deployment of themes and plugins for WordPress, as well as other development tasks. Many WordPress hosting providers support or plan to support WP-CLI. You will need to check with your hosting provider to see if they support WP-CLI.

```
Exec
(
    FileName: {PHP_HOME}\php,
    Arguments: wp-cli.phar theme install {URL_OF_ZIP} --activate
)
```

Refer to the [WordPress installation document](https://make.wordpress.org/cli/handbook/installing/) to installing the WP-CLI.


## Tagging Applied Theme or Plugin 

At the last stage of your pipeline, it's a good idea to apply a tag to the exact code that was elevated. This can be done using the commit captured in the first stage:

```
GitHub::Tag
(
    From: InedoWordPressTheme,
    Tag: $ReleaseNumber.$BuildNumber,
    Branch: $Branch,
    CommitHash: $CommitId
);
```