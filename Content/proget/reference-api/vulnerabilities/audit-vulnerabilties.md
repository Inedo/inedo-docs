---
title: "Audit Vulnerabilties"
order: 1
hidden: true
---

*Audit Vulnerabilities* is available as both a `pgutil` command and an HTTP Request, and will create a license, with the properties defined by a [PackageVersionIdentifier]() object in the body of the request.

:::(Info) (🚀 Quick Example: Auditing vulnerabiliites in a project with pgutil)
This example will audit vulnerabilities in version `1.2.3` of the package `MyPackage` in the project `myProject`
```
pgutil vulns audit --input=myProject --package=myPackage  --type=nuget --version=1.2.3
```
:::

## Command Specification (CLI)
The `vulns audit` command set is used audit a package or project.

The  `--package`, `--type=nuget`and `--version` options are always required. Auditing a project requires the `--input` option

**Auditing package vulnerabilities** requires the package name (e.g. `myPackage`), type (e.g. `nuget`) and version (e.g. `1.2.3`):
```
pgutil vulns audit --input=myProject --package=myPackage  --type=nuget --version=1.2.3
```

## HTTP Request Specification

:::(Info) (🔑 Authentication)
If you disallow anonymous access to your feed, you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys) that grants access to the Feed API.  You will need to include that key as a header named `X-ApiKey` and set its value to that API key.

For example, to authenticate with the API key `abc12345`  to this endpoint, you could specify the API key as follows:
````
curl -X POST -H "X-ApiKey: abc12345" https://proget.corp.local/api/sca/audit-package-vulns
````
:::

To audit vulnerabilities, simply `POST` to the URL with an appropriate API Key and an array of [PackageVersionIdentifier]() objects as the request body.
```
POST /api/sca/audit-package-vulns
[ 
    {
        "name": myPackage
        "version": 1.2.3
        "type": "nuget"
    },
    {
        "name": myPackage
        "version": 1.2.3
        "type": "nuget"
    }
]
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of [VulnerabilityInfo](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/VulnerabilityInfo.cs) objects. For example, auditing version `1.2.3` of the package `myPackage` returns:

```
[
    {
        "id": "PGV-12345678",
        "affectedPackages": [0],
        "summary": "Improper Handling of Exceptional Conditions",
        "published": "2022-06-22T15:08:47Z",
        "details": "This is a vulnerability",
        "cve": "CVE-1234-56789",
        "cvss": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H",
        "numericCvss": 7.5,
        "severity": "High"
    },
    {
        "id": "PGV-8765432",
        "affectedPackages": [0,1],
        "summary": "Improper Input Validation",
        "published": "2022-06-22T15:08:47Z",
        "details": "This is another vulnerability",
        "cve": "CVE-9876-5432",
        "cvss": "CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:H",
        "numericCvss": 7.5,
        "severity": "High"
    }
]
```

| Response | Details |
|---|---|
| **200 (Success)** | the body will contain an array of [VulnerabilityInfo](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/VulnerabilityInfo.cs) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a missing, unknown, or unauthorized API Key; the body will be empty |