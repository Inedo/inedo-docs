---
title: "Assess Vulnerabilities"
order: 2
---

*Assess Vulnerabilities* is available as both a `pgutil` command and an HTTP Request, and will assess a vulnerability according to the specified query arguments.

:::(Info) (🚀 Quick Example: Auditing vulnerabilities in a project with pgutil)
This example will assess the vulnerability `PGV-1234567` as `blocked`:

```bash
pgutil vulns assess --id=PGV-1234567 --type=blocked  
```
:::

## Command Specification (CLI)
The `vulns assess` command is used to assess a vulnerability.

The `--id` and `--type` options are always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Assessing a vulnerability** requires the vulnerability id (e.g. `PGV-1234567`) and assessment type (e.g. `blocked`):

```bash
pgutil vulns assess --id=PGV-1234567 --type=blocked --comment="Package non-compliant" --policy=myPolicy
```

## HTTP Request Specification

:::(Info) (🔑 Authentication)
If you disallow anonymous access to your feed, you will need to create an [API Key](/docs/proget/api/apikeys) that grants access to the Feed API.  You will need to include that key as a header named `X-ApiKey` and set its value to that API key.

For example, to authenticate with the API key `abc12345`  to this endpoint, you could specify the API key as follows:

```bash
curl -X POST -H "X-ApiKey: abc12345" https://proget.corp.local/api/sca/assess
```
:::

To assess a vulnerability, simply `POST` to the URL with a `vulnerability id`, `assessment type`, `comment` and `policy` parameters, and an appropriate API Key with `Manage SCA (Projects & Builds)` or `Manage Projects` permissions. 

```
POST api/sca/assess[?id=«vulnerability-id»][&type=«assessment-type»][&comment=«comment»][&policy=«policy-name»]
```
Note that the `comment` and `policy` parameters are optional.

## HTTP Response Specification
The vulnerability will be assessed on a successful `200` response. A `403` response indicates a missing, unknown, or unauthorized [API Key](/docs/proget/api/apikeys).
