---
title: "Audit Package"
order: 6
---

*Audit Package* is available as both a `pgutil` command and an HTTP Request, and will analyze a package for compliance issues. 

:::(Info) (🚀 Quick Example: Auditing a NuGet package with pgutil)
This example analyzes version `13.0.4` of the package `Newtonsoft.Json` in the feed `myNugetFeed`:

```bash
pgutil packages audit --feed=myNugetFeed --package=Newtonsoft.Json --version=13.0.4
```
:::

This API requires ProGet 2023.0 or later.

## Command Specification (CLI)
The `packages audit` command is used to analyze a package for compliance issues.

The `--package` and `--version` options are always required. The `--feed` option is required if a default feed is not configured.

Additional options may be required depending on the package type. If a required option is missing, an error will be returned indicating the missing option.

**Auditing a NuGet Package** requires the name (e.g. `Newtonsoft.Json`) and version (e.g. `13.0.4`):

```bash
pgutil packages audit --feed=MyNuGetFeed --package=Newtonsoft.Json --version=13.0.4
```

**Auditing an npm Package** requires the scope (e.g. `@babel`), name (e.g. `runtime`) and version (e.g. `7.25.0`):

```bash
pgutil packages audit --feed=public-npm --package=@babel/runtime --version=7.25.0
```

Output Examples:

```plaintext
Checking Newtonsoft.Json 13.0.4...
Compliant
```

```plaintext
Checking Newtonsoft.Json 12.0.3...
Noncompliant
```

Note source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To audit a package, simply `POST` to the URL with a feed name, [package identifiers](/docs/proget/api/packages#using-multiple-parameters), and an [appropriate API Key](/docs/proget/api/packages#authentication).

```plaintext
POST /api/packages/«feed-name»/audit?«package-identifiers»
```

Unless you use a `purl`, the parameters required will vary by feedtype. 

## HTTP Response Specification
A `AuditPackageResults` (see [AuditPackageResults.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/AuditPackageResults.cs)) object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/packages#authentication).

## Sample Usage Scripts

### List Compliance Status of All Packages (PowerShell)
The following script will list all packages in the `unapproved-nuget` feed and display their compliance status. 

```powershell
$BaseUrl = "https://proget.corp.local"
$ApiKey  = "abc12345"
$Feed    = "unapproved-nuget"

$Headers = @{"X-ApiKey" = $ApiKey}

$PackagesUrl = "$BaseUrl/api/packages/$Feed/latest"
try {
    $Packages = Invoke-RestMethod -Method GET -Uri $PackagesUrl -Headers $Headers
} catch {
    Write-Host "ERROR: Failed to retrieve package list." -ForegroundColor Red
    Write-Host $_.Exception.Message
    exit 1
}

$Results = New-Object System.Collections.ArrayList

foreach ($Pkg in $Packages) {
    $PackageName = $Pkg.name
    $VersionsUrl = "$BaseUrl/api/packages/$Feed/versions?name=$PackageName"

    try {
        $Versions = Invoke-RestMethod -Method GET -Uri $VersionsUrl -Headers $Headers
    } catch {
        continue
    }

    if (-not $Versions) { continue }

    foreach ($VersionObj in $Versions) {
        $Version = $VersionObj.version
        $AuditUrl = "$BaseUrl/api/packages/$Feed/audit?name=$PackageName&version=$Version"

        try {
            $AuditResult = Invoke-RestMethod -Method POST -Uri $AuditUrl -Headers $Headers
        } catch {
            continue
        }

        $StatusText = $AuditResult.statusText
        $Results.Add([PSCustomObject]@{
            Package = $PackageName
            Version = $Version
            ComplianceStatus = $StatusText
        }) | Out-Null
    }
}

$Results | Format-Table -AutoSize
```

Example Output:

```plaintext
Package         Version ComplianceStatus
-------         ------- ----------------
Newtonsoft.Json 12.0.3  Noncompliant
Newtonsoft.Json 13.0.4  Compliant

```

### List Compliance Status of All Packages (Python)
The following script will list all packages in the unapproved-nuget feed and display their compliance status. 

```python
import requests

BASE_URL = "https://pg02.phs.inedo.com"
API_KEY = "proget"
FEED = "public-nuget"

headers = {
    "X-ApiKey": API_KEY
}

packages_url = f"{BASE_URL}/api/packages/{FEED}/latest"
try:
    packages = requests.get(packages_url, headers=headers).json()
except Exception as e:
    print("ERROR: Failed to retrieve package list:", e)
    exit(1)

results = []

for pkg in packages:
    package_name = pkg.get("name")
    if not package_name:
        continue

    versions_url = f"{BASE_URL}/api/packages/{FEED}/versions?name={package_name}"

    try:
        versions = requests.get(versions_url, headers=headers).json()
    except Exception:
        continue

    if not versions:
        continue

    for version_obj in versions:
        version = version_obj.get("version")
        if not version:
            continue

        audit_url = f"{BASE_URL}/api/packages/{FEED}/audit?name={package_name}&version={version}"

        try:
            audit_result = requests.post(audit_url, headers=headers).json()
        except Exception:
            continue

        status_text = audit_result.get("statusText", "Unknown")

        results.append({
            "Package": package_name,
            "Version": version,
            "ComplianceStatus": status_text
        })

print("\nPackage Compliance Results:")
print("-" * 70)

print(f"{'Package':<40} {'Version':<15} {'Status'}")
print("-" * 70)

for r in results:
    print(f"{r['Package']:<40} {r['Version']:<15} {r['ComplianceStatus']}")
```

Example Output:

```plaintext
Package         Version ComplianceStatus
-------         ------- ----------------
Newtonsoft.Json 12.0.3  Noncompliant
Newtonsoft.Json 13.0.4  Compliant
```
