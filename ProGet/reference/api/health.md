---
title: ProGet Health API
subtitle: Using the the ProGet Health API
keywords: proget, api, health
sequence: 900
---

Available starting in ProGet 5.2.9, the Health API allows users to quickly get information about the health and status of a specific instance of ProGet.

Usage of this endpoint does not require an api key and can be accessed by simply adding the `/health` to your ProGet url (e.g. `http://proget.kramerica.corp/health`).

Sample output from the `/health` endpoint:

```
{
  "applicationName": "ProGet",
  "releaseNumber": "5.2.9",
  "versionNumber": "5.2.9.10",
  "extensionsInstalled": [
    "Inedo SDK 1.3.1",
    "InedoCore 1.0.15"
  ],
  "serviceStatus": "OK",
  "serviceStatusDetail": null,
  "licenseStatus": "OK",
  "licenseStatusDetail": null,
  {
  	  "serverStatus": "OK",
	  "serverError": null,
	  "clientStatus": "OK",
	  "clientError": null
  }
}
```
