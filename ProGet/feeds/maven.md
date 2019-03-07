---
title: Maven (Java)
subtitle: Maven Feeds in ProGet
sequence: 200
keywords: proget, feeds, maven

---

Maven feeds contain a maven2-compatible Maven artifact repository. Maven works a little differently than the other feed types.

## Maven Specifics {#maven-spefics data-title="Maven Specifics"}

Maven artifacts are not self-contained archives; instead, an artifact is more of a grouping of files with some associated metadata. Because of this distinction, Maven feeds do not support drop path imports.

Connectors are also slightly different in Maven than in most other feed types. As there is no universal standard for searching or listing from a remote Maven repository, ProGet uses a rather dated API that provides an index of *all* artifacts. A task in the ProGet service will periodically download that index and unpack it to a local Lucene database, and use that for searching the remote repository. For the public apache Maven repository, this can be a very resource-intensive operation, and if you do not need this searching capability, we recommend disabling this task in ProGet. Artifacts accessed directly by name do *not* need this index.

As of ProGet 5.1.0, Maven feeds support alternate package stores (AWS, Azure).

### Authenticating to Maven Feeds {#authenticating data-title="Authenticating to Maven Feeds"}

Authentication settings for the maven client is configured in your [settings.xlm](https://maven.apache.org/settings.html) file. You can use the standard &lt;username> and &lt;password> elements to store a username/password for ProGet, or you can specify `api` as the username, and an [API Keys](/support/documentation/proget/administration/security/api-keys) as a password.
