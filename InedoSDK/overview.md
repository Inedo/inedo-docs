---
title: Inedo SDK Overview
subtitle: What is the Inedo SDK?
sequence: 10
keywords: inedo, inedo sdk
---

A lot of Inedo's product functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities as well as integrations with third-party tools. With the Inedo SDK, you can add or modify functionality and capabilities to the product by [creating an extension](/support/documentation/inedosdk/extending/creating).

Many of the same components can be used different products, which means you can write a component once and introduce the same functionality across all of our products. Here's what you can extend:

|                        | PG        | HH        | OT        | BM        |        |
| ---------------------- |:---------:|:---------:|:---------:|:---------:|--------|
|**Configuration**       | &#10007;  | &#10004;* | &#10004;  | &#10004;* | A representation of a specific type of server configuration (such as the settings for an IIS Application Pool, or a directory on disk) that is used to store information collected from a server (Otter only) or as a template for desired configuration. |
|**Credential**          | &#10004;* | &#10004;  | &#10004;  | &#10004;  | A secret with secure fields that is stored in an encrypted manner. In ProGet, these are currently only used by the AD User Directory. |
|**File System**         | &#10004;  | &#10007;  | &#10007;  | &#10007;  | Used by ProGet to access the underlying storage medium for package data. The default implementation uses a directory on disk. |
|**Issue Source**        | &#10007;  | &#10007;  | &#10007;  | &#10007;  | Provides a list of issues from an external source, such as Jira. |
|**List Variable Source**| &#10007;  | &#10004;  | &#10007;  | &#10004;  | Provides a dynamic list for the user interface; currently used in Release templates. |
|**Operation**           | &#10007;  | &#10004;  | &#10004;  | &#10004;  | A discrete task that is executed by the OtterScript runtime. |
|**Package Access Rule** | &#10004;  | &#10007;  | &#10007;  | &#10007;  | Allows for rules to block downloads of packages based on additional logic. |
|**Package Filter**      | &#10004;  | &#10007;  | &#10007;  | &#10007;  | Allows all locally-stored ProGet packages to be filtered from being shown in feeds by additional logic. |
|**Raft Repository**     | &#10007;  | &#10004;  | &#10004;  | &#10004;  | A self-contained abstract file system that is used as a backing store for plans, assets, and other files. |
|**User Directory**      | &#10004;  | &#10004;  | &#10004;  | &#10004;  | Responsible for authenticating users and groups. The default implementation uses users and groups stored in the product's database. |
|**Variable Function**   | &#10004;  | &#10004;  | &#10004;  | &#10004;  | Used in plans to provide or manipulate values. |
|**Vulnerability Source**| &#10004;  | &#10007;  | &#10007;  | &#10007;  | Pulls a list of known security vulnerabilities for packages from a third-party source. |
