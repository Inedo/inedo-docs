---
title: PHP
subtitle: Building and Deploying PHP Applications in BuildMaster
sequence: 500
---

# Building and Deploying PHP Applications in BuildMaster

PHP is one of the oldest and arguably most common languages used for the web. And over the years, it's evolved from a scripting language to an application development platform.

PHP is very simple platform to deploy: Just the copy files to the web root (a folder configured by apache or another web server) and edit the [configuration file](#php-configuration-files) (which may be variables in a `.php` file or an `.ini` file).

But because it's so simple to deploy, it's easy to deploy sub-optimally. For example, using the "cherry pick" method of file-by-file deployments or "deploy-by branch" result in the major problem of working with different software than the previous environment.

The best way to deploy PHP is using artifacts or packages. To do this:

- [Build](https://docs.inedo.com/docs/buildmaster/reference/operations/artifacts/create-artifact) or import artifacts
- Deploy to test environment(s) in an [advanced pipeline](https://inedo.com/support/tutorials/buildmaster/deployments/creating-advanced-pipelines)
- Receive approvals
- [Deploy](https://docs.inedo.com/docs/buildmaster/reference/operations/artifacts/deploy-artifact) to production

This is the best way to deploy because artifacts are more stable and reliable because each stage of deployment receives equivalent output, whether you build your artifacts in your CI/CD tool or import from a CI server. 

## Configuring BuildMaster to Build PHP Applications

**See it live!** Create a new application using the *PHP CI/CD template* in your own instance of BuildMaster.

The process is relatively simple:

- Get code from your [source control repository](/docs/buildmaster/builds/continuous-integration/source-control)
  - This could be GitLab, GitHub, svn, etc.
- You also could use a [drop folder](/docs/buildmaster/builds/external-systems/drop-folder).

PHP does not require compilation, so from here you could simply package it as an artifact or universal package.

But there are [some advanced things](#advanced-php) you can do as well.

## Managing your PHP Configuration Files in BuildMaster

PHP applications rely on [configuration values](https://stackoverflow.com/questions/14752470/creating-a-config-file-in-php) that can change from environment to environment and even contain sensitive data such as database connection strings, third-party service URLs, API keys, etc.

There are two general approaches for handling this in PHP:

- .ini files that are parsed using `parse_ini_file()`, which returns an array of configuration values. (For more, see [https://www.php.net/manual/en/function.parse-ini-file.php#refsect1-function.parse-ini-file-examples](https://www.php.net/manual/en/function.parse-ini-file.php#refsect1-function.parse-ini-file-examples))
- .php files that are included and directly incorporate those configuration values as runtime variables

Both methods are equally popular; it’s generally a matter of your Development team’s preferences. Either way, BuildMaster manages both by using configuration file assets. [Configuration file assets](http://docs.inedo.test/docs/buildmaster/deployments/configuration-files) store multi-instance configuration files and provide a multi-tabled editor to edit and compare these instances.

## Deploying Your PHP Applications with BuildMaster

PHP is typically hosted in apache, but may also be hosted in nginx or IIS. You can also host in containers, though this involves a different process (for more, check out [container docs]).

We recommend following the following process ((p37 of book)):

- Stop
- Deploy files
- Start

Since you can't overwrite code that's currently executing, you have to stop it before making your changes, then restart the code for the changes to take effect. 

There are also advanced patterns like [blue/green](inedo.com/docs/buildmaster/deployments/patterns/blue-green).

## CI/CD for PHP Applications

Once you've set build and deploy, using Continuous Integration and Continuous Delivery for PHP is easy: just configure a [build trigger](http://docs.inedo.test/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors) to automatically monitor for changes.

## Advanced PHP Automation Practices

Before you package your application, there are more advanced actions you can take with PHP, such as:

- run unit tests (phpunit) [link below]
- install dependencies (composer) [link below]

You can also use Grunt or Gulp to:

- minify javascript and css
- compile less or sass resources to css
- compile typescript to javascript

Ideally, your developer experience and BuildMaster experience should be as close as possible, but you also want BuildMaster to record unit test results. You may also want to capture open source licenses from third-party packages as a [build report](http://docs.inedo.test/docs/buildmaster/builds/tests/build-reports).

## Using Composer and BuildMaster

To install packages, you can use the `Composer::Install-Packages` operation.

General best practices for installing packages are:

- don't check in your [dependencies](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md).
- use a lock file.
- run composer before capturing the artifact.

BuildMaster also lets you report on packages and licenses used by those packages, which protects your company from possible liability due to hard-to-find licensing info.

Using third-party, open source packages brings their licensing terms along for the ride. For example, using a GNU3-licensed package in your application requires you to open source your application and license it under GNU3. If you don’t do this, your organization could face a lawsuit from the package authors. Even worse: finding these licenses is challenging. You have to dig through source control and then go to each package to uncover the information you need.

BuildMaster can automatically capture the licenses that third-party packages are using the Composer::Capture-LicenseReport and watch it **live** in the `Build-Advanced` OtterScript plan in the template application. You can also add a manual approval, so that a human must verify these licenses before deploying to production.

## Using PHPUnit and BuildMaster

BuildMaster supports [automatically running unit tests](https://docs.inedo.com/docs/buildmaster/builds/tests/unit-tests) for your PHP applications.

Unit testing offers several benefits, including automatic detection of problems before human testing, saving time and pushing problems further left in your pipeline.

To test with BuildMaster, use the `PHP::Execute-PhpUnit` operation, which you can also see **live** in the `Build-Advanced` OtterScript plan in the template application.


> Written with [StackEdit](https://stackedit.io/).