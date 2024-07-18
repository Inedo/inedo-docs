---
title: "PHP"
order: 4
---


PHP is one of the oldest and probably most widely used languages used for the web. And over the years it has evolved from a scripting language to a platform for application development.

PHP is a simple platform to deploy: Just copy the files to the web root (a folder configured by Apache or another web server) and edit the [configuration file](#php-configuration-files) (which may be variables in a `.php` or `.ini` file).

Because it's so easy to deploy, it's can often be deployed suboptimally. For example, with the cherry-pick method of file-by-file deployment or branch-by-branch deployment, both lead to the big problem of working with different software than in the previous environment.

The best way to deploy PHP is to use artifacts or packages. To do this:

- [Build](/docs/buildmaster/reference/operations/files/create-artifact) or import artifacts
- Deploy to test environment(s) in an [advanced pipeline](https://inedo.com/support/tutorials/buildmaster/deployments/creating-advanced-pipelines)
- Receive approvals
- [Deploy](/docs/buildmaster/reference/operations/files/deploy-artifact) to production

This is the best way to deploy because the artifacts are more stable and reliable, as each stage of the deployment gets equivalent output whether you build your artifacts in your CI/CD tool or import them from a CI server.

## Configuring BuildMaster to Build PHP Applications 

::: (INFO)

**See it live!** Create a new application using the *PHP CI/CD* template in your own instance of BuildMaster.
:::

The process is simple:

- Get code from your [source control repository](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control/buildmaster-ci-cd-continuous-integration-server-source-control) regardless of platform, e.g., GitLab, GitHub, SVN
- You also could use a [drop folder](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts/buildmaster-drop-folders)

PHP does not require compilation, so you could simply package it as an artifact or universal package.

But there are [some advanced things](#advanced-php) you can do as well.

## Managing Your PHP Configuration Files in BuildMaster 

PHP applications rely on [configuration values](https://stackoverflow.com/questions/14752470/creating-a-config-file-in-php) that can change from environment to environment and even contain sensitive data such as database connection strings, third-party service URLs, API keys, etc.

There are two general approaches for handling this in PHP:

- .ini files that are parsed using `parse_ini_file()`, which returns an array of configuration values. For more information on this pattern, visit the  [PHP `parse_ini_file` documentation](https://www.php.net/manual/en/function.parse-ini-file.php#refsect1-function.parse-ini-file-examples)
- .php files that are included and directly incorporate those configuration values as runtime variables

Both methods are equally common; it's generally a matter of your development team's preferences. BuildMaster manages both by using configuration file assets. [Configuration file assets](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) store multi-instance configuration files and provide a multi-tabled editor to edit and compare these instances.

## Deploying Your PHP Applications with BuildMaster 
PHP is commonly hosted in Apache, but may also be hosted by any other webserver including nginx, IIS, etc. 

We recommend the following process:

- Stop website
- Deploy files
- Start website

Since you can't overwrite code that's currently executing, you have to stop it before making your changes, then restart the code for the changes to take effect. There are also advanced patterns like [blue/green](/docs/buildmaster/deployment-continuous-delivery/buildmaster-ci-cd-deployment-patterns/buildmaster-ci-cd-deployment-patterns-blue-green).

## CI/CD for PHP Applications

Once you've set build and deploy, using Continuous Integration and Continuous Delivery for PHP is easy: just configure a [build trigger](/docs/buildmaster/administration/buildmaster-resource-monitors) to automatically monitor for changes.

## BuildMaster's PHP Extension 

BuildMaster's PHP integration is handled by the [PHP extension](https://github.com/Inedo/inedox-php). Once installed, this extension adds the following plan operations:

 - `Execute-PHPUnit` - runs [PHPUnit tests](#php-unit)
 - `Composer::Install-Packages` - installs [Composer packages](#composer)

In order for these operations to function, the following extension configuration variable values must be set *if they are located in a different path than the default*:

 - `$ComposerExePath` - Full path of composer. The default is `/usr/bin/composer` on Linux and `C:\ProgramData\ComposerSetup\bin\composer` on Windows
 - `$PHPExePath` - Full path of php.exe. The default is `/usr/bin/php` on Linux and `C:\Program Files\PHP\php.exe` on Windows
 - `$PHPUnitPath` - Full path of phpunit. The default is `/usr/bin/phpunit` on Linux and `C:\bin\phpunit.phar` on Windows

You can set these variables under Administration > Extensions > PHP > Configuration at the system level, or any scope that you'd like (such as per-server).

## Advanced PHP Automation Practices

Before you package your application, there are more advanced PHP actions you can take, such as:

- Run unit tests [PHPUnit](https://phpunit.de/)
- Install libraries and code dependencies with [Composer](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md)

You can also use tools like [Grunt](https://gruntjs.com/) or [Gulp](https://gulpjs.com/) to:

- Minify JavaScript and CSS
- Transpile LESS or SASS resources to CSS
- Transpile TypeScript to JavaScript

Ideally, your developer experience and BuildMaster experience should be as close as possible, but you also want BuildMaster to record unit test results. In addition, you may want to capture open-source licenses from third-party packages as a [build report](/docs/buildmaster/builds-continuous-integration/automated-testing-verification/buildmaster-ci-cd-testing-and-verification-reports).

## Using Composer and BuildMaster

To install packages, you can use the `Composer::Install-Packages` operation.

General best practices for installing packages are:

- Don't check in your [dependencies](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md)
- Use a lock file
- Run Composer before capturing the artifact

BuildMaster also lets you report on packages and licenses used by those packages, which protects your organization from potential liability due to hard-to-find license information.

Using third-party open-source packages comes with their license terms. For example, if you use a GNU3-licensed package in your application, you must open source your application and license it under GNU3. If you do not, your company could face a lawsuit from the package authors. Worse, it's difficult to find these licenses. You'll have to dig through the source code repository and track down each package to find the information you need.

BuildMaster can automatically capture the licenses that third-party packages are using the `Composer::Capture-LicenseReport`. You can also add a manual approval so that a human must verify these licenses before deploying to production.

::: (INFO)
See it **live** in the `Build-Advanced` OtterScript plan in the template application
:::

## Using PHPUnit and BuildMaster 

BuildMaster supports [automatically running unit tests](/docs/buildmaster/builds-continuous-integration/automated-testing-verification/buildmaster-ci-cd-testing-and-verification-unit-tests) for your PHP applications.

Unit testing offers several benefits, including automatically detecting issues before human testing, which saves time and moves issues further to the left in your pipeline.

To test with BuildMaster, use the `PHP::Execute-PhpUnit` operation.

::: (INFO)
See it **live** in the `Build-Advanced` OtterScript plan in the template application
:::