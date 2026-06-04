---
title: "ProGet Health"
order: 4
---

ProGet Health is available as both a `pgutil` command and an HTTP Request, and allows you to quickly obtain information about the health and status of a ProGet instance.

:::(Info) (🚀 Quick Example: Get the Health of a ProGet Instance with pgutil)

```bash
pgutil health --source=http://progets.corp.local/
```
:::

An API key is not required for ProGet Health.

## Command Specification (CLI)
The `health` command has a single optional argument (`--source`), which can be a URL or the name of a source. See [Working with Sources](/docs/proget/api/pgutil#working-with-sources) to learn more.

```bash
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

To perform a health check, simply perform a `GET` request to `/health`.

```plaintext
GET /health
```

For a JSON-based response, you can perform a `GET` request to either `/health/json` or `/health?format=json`.

## HTTP Response Specification

A successful response will simply be a `200` status with a `OK` body. A `500` will include the problem in plain-text format.

When JSON format is specified, a response body will contain a `ProGetHealthInfo` JSON object (see [ProGetHealthInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/ProGetHealthInfo.cs)). A `500` response indicates a health problem (such as service not running); the body will contain a `ProGetHealthInfo` object.

## ProGet 2025 and Earlier
ProGet 2025 and earlier always responded with a JSON-based response.

ProGet 2023 and earlier included a [Connector Health API (archive.org)](https://web.archive.org/web/20230521045843/https://docs.inedo.com/docs/proget-reference-api-connector-health). It did not really provide any useful or timely information, and was removed in ProGet 2024 as part of a larger connector refactoring.
