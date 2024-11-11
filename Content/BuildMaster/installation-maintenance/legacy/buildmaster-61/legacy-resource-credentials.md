---
title: "Legacy Resource Credentials"
order: 5
url-slug: "buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features/legacy-resource-credentials"
---

## Legacy Resource Credentials (BuildMaster 6.1)
Prior to BuildMaster 6.2, a single feature called Resource Credentials was used instead of Secure Credentials and Secure Resources. These are displayed with a warning icon on the secure credentials page.

Even though Resource Credentials are considered a Legacy Feature, you can still create and edit them. In some cases, such as custom/community extensions, they may be the only option until the extension is adapted to the new features.

Many operations that use a Secure Resource will attempt to search for a Resource Credential if the specified Secure Resource was not found. This allows upgrades without breaking previous OtterScripts plans and modules.

### Converting to Secure Credentials & Resources
You can convert a Resource Credential into a Secure Credentials and/or Secure Resources from the Manage Secure Credentials page. This is a one-way conversion, so be sure to save important information (such as the password) in case you need to re-enter it.

For example, a GitHub Resource Credential would be converted to a GitHub Account (Secure Credential) and a GitHub Project (Secure Resource), unless the user name was blank, in which case it will only be converted to a Secure Resource.

### Legacy Resource Credential Cascading Rules

BuildMaster v6.1.10 introduced cascading resource credentials that allowed credentials to be created at the application group level. A resource credential can also inherit from another resource credential, overriding certain properties.

For example, a single application's resource credentials could specify a system-level parent instance that stores username/password information for an entire system, and then override only the repository URL. This way you can specify the secret value in one place (with elevated privileges required for access) and connect to different repositories consistently across all applications.

For security reasons, the environment of the parent credential must exactly match that of the inheritor, or the parent environment must not be specified (which means that it applies to all environments)

When resolving a resource credential at execution time, candidates are selected by name in order of matching properties: Application + Environment, Application, Application Group, Predecessor Application Group, Environment, System.