---
title: "Legacy Configuration and Release Variables"
order: 3
---

In BuildMaster 5.3, we introduced two new features that enhanced and replaced existing features:

 - **Release Templates** - a template for an entire release definition (including variables, among other things), these replace "template variables" that were instead created for each new release, package or deployment within an application
 - **Configuration Variables** - effectively a rewrite of the existing feature, but allowing for multiple scopes as well as list and mapping variables

Since variables are a key component of many deployment plans, we decided to minimize the risk of change by implementing the new release templates and configuration variables "side by side".

Prior to BuildMaster 6.2, all Configuration Variables you set up prior to 5.3 will work the same, but will be referred to in the web application as *Legacy Configuration* and *Legacy Template Variables*.

## Evaluation and Precedence 
The rules for scoping legacy and non-legacy variables are the same (although non-legacy variables may be scoped multiple times), but a non-legacy variable always takes precedence over a legacy variable.

For example, if you have both a system-level and application-level  legacy variable named `AppPath`, then the value of the application-level legacy variable takes precedence. However, if you add a non-legacy system variable named `AppPath`, it always takes precedence over the application-level legacy variable.

## Migrating Away from Legacy Variables

Legacy variables are supported until the latest BuildMaster 6.1 maintenance release and officially removed in 6.2. But even in supported versions, it can be confusing to manage two sets of variables in the BuildMaster instance. More importantly, newer functions that do not use legacy variables cannot use legacy variables.

### Configuration File Migration 

The legacy configuration migration tool has two options (*Copy Legacy Variables* and *Delete Legacy Variables*), as well as a simulation mode.

Unless you have a tremendous number of complex variables configured, you should perform a migration using the Copy and Delete options. This ensures a clean migration, and all deleted variables (as well as the values) are logged.