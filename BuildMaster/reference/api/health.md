---
title: BuildMaster Health API
subtitle: Using the the BuildMaster Health API
keywords: buildmaster, api, health
sequence: 900
---

There are various API endpoints you can use to programmatically query or configure BuildMaster instances. The Health API allows users to quickly get information about the health and status of a specific instance of BuildMaster. Usage of this endpoint does not require an api key and can be accessed by simply adding the /health to your BuildMaster url `http://BuildMaster/health`.

Here are sample values returned from the /health endpoint. 

```
{
  "applicationName": "BuildMaster",
  "instanceName": null,
  "releaseNumber": "6.2.1",
  "versionNumber": "DEV BUILD",
  "extensionsInstalled": [
    "BuildMaster Core Library 6.2.1",
    "BuildMaster Core Extensions 6.2.1",
    "Inedo.BuildMaster 6.2.1",
    "Inedo SDK 1.3.1",
    "GitLab 1.3.0",
    "InedoCore 1.0.15",
    "Subversion 1.1.0",
    "Git 1.3.0"
  ],
  "serviceStatus": "OK",
  "serviceStatusDetail": null,
  "databaseStatus": "OK",
  "databaseStatusDetails": null,
  "licenseStatus": "OK",
  "licenseStatusDetail": null
}
```

