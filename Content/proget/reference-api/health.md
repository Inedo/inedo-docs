---
title: "ProGet Health"
order: 4
---

ProGet Health is available as both a `pgutil` command and an HTTP Request, and allows you to quickly obtain information about the health and status of a ProGet instance.

:::(Info) (🚀 Quick Example: Get the Health of a ProGet Instance with pgutil)

```
pgutil health --source=http://progets.corp.local/
```
:::

An API key is not required for ProGet Health.

## Command Specification (CLI)
The `health` command has a single optional argument (`--source`), which can be a URL or the name of a source. See [Working with Sources](/docs/proget/reference-api/proget-pgutil#working-with-sources) to learn more.

```
$> pgutil health --source=http://progets.corp.local/
Version: 2022.28 (Build 4)

Database: OK
License:  OK
Service:  Error

Replication (Server): OK
Replication (Client): OK
```

If any of the displayed statuses are not OK, a nonzero exit code will be reported.


## HTTP Request Specification

To create an API key, simply perform a `GET` request to `/health`.

A response body will contain a `ProGetHealthInfo` JSON object (see [ProGetHealthInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetHealthInfo.cs)).

## HTTP Response Specification

| Response | Details |
| --- | --- | 
| **200 (Success)** | body will contain a `ProGetHealthInfo` JSON object
| **500 (Health Problem)** | indicates a health problem (such as service not running); the body will contain a `ProGetHealthInfo` object
| **500 (Server Error)** | indicates an unexpected error; the body will contain an error message and stack trace

## ProGet 2023 and Earlier

ProGet 2023 and Earlier included a [Connector Health API (archive.org)](https://web.archive.org/web/20230521045843/https://docs.inedo.com/docs/proget-reference-api-connector-health). It did not really provide any useful or timely information, and was removed in ProGet 2024 as part of a larger connector refactoring.