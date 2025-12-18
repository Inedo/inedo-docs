---
title: "Audit Vulnerabilties"
order: 1
---

*Audit Vulnerabilities* is available as both a `pgutil` command and an HTTP Request, and will audit packages individually or associated with a project file.

:::(Info) (🚀 Quick Example: Auditing vulnerabilities in a project with pgutil)
This example will audit vulnerabilities in the project `MyProject.csproj` located locally in `c:\projects\`:

```plaintext
pgutil vulns audit --project=c:\projects\MyProject.csproj
```
:::

Note that vulnerability assessments are not available in ProGet Free edition and this API is subject to API limits in ProGet Basic edition. See [License Restrictions](/docs/proget/administration/license) to learn more.


## Command Specification (CLI)
The `vulns audit` command is used audit a project file. This command is intended to audit packages used as part of a build process. It should be run after building the project because it requires the presence of specific build tool output (i.e. a lockfile or similar artifact) to generate accurate data.

The  `--project` option is always required. The `--type` option is optional and will filter by package type.

**Auditing project vulnerabilities** requires the project file name (e.g. `myProject`).

```plaintext
pgutil vulns audit --project=c:\projects\MyProject.csproj
```

Running this command will asses the specified project and output the results:

```plaintext
Scanning for dependencies in projects/MyProject.csproj...
Found 2 package dependencies. Performing audit...
1 vulnerable package detected.

PGV-2245804: 7.5 (High) - Improper Handling of Exceptional Conditions in Newtonsoft.Json
 * Packages: Newtonsoft.Json 12.0.3
 * Projects: MyProject
```

## HTTP Request Specification

:::(Info) (🔑 Authentication)
If you disallow anonymous access to your feed, you will need to create an [API Key](/docs/proget/api/apikeys) that grants access to the Feed API.  You will need to include that key as a header named `X-ApiKey` and set its value to that API key.

For example, to authenticate with the API key `abc12345` to this endpoint, you could specify the API key as follows:

```bash
curl -X POST -H "X-ApiKey: abc12345" https://proget.corp.local/api/sca/audit-package-vulns
```
:::

To audit packages, simply `POST` to the URL with an appropriate API Key and an array of `PackageVersionIdentifier` (see [PackageVersionIdentifier.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/PackageVersionIdentifier.cs)) objects as the request body.

```bash
POST /api/sca/audit-package-vulns
```

## HTTP Response Specification
An array of `VulnerabilityInfo` (see [VulnerabilityInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/VulnerabilityInfo.cs)) objects will be returned on a successful `200` response. A `403` response indicates a missing, unknown, or unauthorized [API Key](/docs/proget/api/apikeys).
