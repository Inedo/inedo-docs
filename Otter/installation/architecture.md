---
title: Architecture & Components
keywords: otter
sequence: 300
show-headings-in-nav: true
---

Otter is comprised of several different components, which may be installed on different servers. See the installation guides for more details.

![](/resources/documentation/otter/a-c-diagram.png)

## Web Application {#web-application data-title="Web Application"}

This is how most users will interact with Otter on a day-to-day basis, and manages the servers, plans, security, users, etc., and also hosts the API.

It is a standard ASP.NET application, and administrators familiar with IIS may tweak the application pool settings as needed. Note that modifying the `web.config` file for any purpose other than to change the database connection string or encryption key is not at all supported.

## Service {#service data-title="Service"}

This runs in the background and actually runs your [configuration and orchestration plans](../../core-concepts/plans) using the [execution engine](../execution-engine). It's a standard [Windows Service Application](https://msdn.microsoft.com/en-us/library/windows/desktop/ms685141(v=vs.85).aspx), and may be managed and [configured](configuration-options) using the Windows Service Manager or `sc.exe` as you see fit.

Note that modifying the `Otter.Service.config` file for any purpose other than to change the database connection string or encryption key is not at all supported.

The [Service](architecture/service) page details how to administer this further.

## Database {#database data-title="Database"}

This persists all the configuration data, execution logs, job schedules, users, etc.

It is a standard SQL Server database, and administrators familiar with SQL Server may tweak the database settings as needed.

### Encryption

Sensitive data, most notably [resource credentials](../global-components/resource-credentials), are encrypted before being stored. Obviously, the encryption key is *not* stored in the database, but in both the `web.config` file (used by the [web application](#web-application)) and the `Otter.Service.config` file (used by the [service](#service)).

### Reporting

Experienced users are welcome to query data from the various tables, and sensitive information [resource credentials](../global-components/resource-credentials) are stored encrypted, and can only be decrypted by the service account. The tables/columns should be fairly obvious once you're familiar with the software, and [we can answer](/support/ticket) any questions you may have about the data or relations.

We do not support... nay, we *strongly discourage* trying to modify any of the data in the tables directly. Instead, use the API or the non-internal Stored Procedures.

## Agents {#agents data-title="Agents"}

Agents are a lightweight service that are installed on remote servers that you want to configure and orchestrate. They use a highly-optimized and resilient protocol, and are secured (by default) using a FIPS-compliant symmetric AES encryption scheme.

Agents may also be configured to use SSL instead of built-in AES, see [KB#1040](/support/kb/1040) for those instructions.
