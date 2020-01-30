---
title: Configuration Variables
subtitle: Legacy Configuration and Release Variables
keywords: buildmaster, legacy, variables
sequence: 300
show-headings-in-nav: true
---

In BuildMaster 5.3, we introduced two new features that expanded upon and replaced existing features:

{.docs}
 - **Release Templates** - a template for an entire release definition (including variables, among other things), these replace "template variables", which instead applied to every new release, package, or deployment created within an application
 - **Configuration Variables** – effectively a redux of the existing feature, but allowing for multiple scopes as well as list and map variables

Because variables are a key component of many deployment plans, we decided to minimize the risk of change by implementing the new release templates and configuration variables in a "side-by-side" manner.

Prior to BuildMaster 6.2, all Configuration Variables you set-up prior to 5.3 will work the same, but will be called *Legacy Configuration* and *Legacy Template Variables* in the web application.

## Evaluation and Precedence {#precedence data-title="Evaluation and Precedence"}

The scoping rules for both legacy and non-legacy variables are the same (although, non-legacy variables may be multi-scoped), but a non-legacy variable will always override a legacy variable.

For example, if you have a both a system- and application-level legacy variable named `AppPath`, then the application-level legacy variable value will take precedence. However, if you add a non-legacy system variable named `AppPath`, then that will always take precedence over the application-level legacy variable.

## Migrating Away from Legacy Variables {#migration data-title="Migration"}

Legacy variables are supported throughthe latest BuildMaster 6.1 maintenance release, and officially removed in 6.2. However, even in supported versions, having two sets of variables to maintain in your BuildMaster instance can be confusing, and more importantly newer non-legacy features will not make use of legacy variables.

### Configuration File Migration 

The legacy configuration migration tool has two options (*Copy Legacy Variables* and *Delete Legacy Variables*), as well as a simulation mode.

Unless you have a tremendous number of complex variables configured, you should be fine running a migration with both the Copy and Delete options. This will ensure a clean migration, and all of the variables deleted (as well as values) will be logged.