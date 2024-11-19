---
title: "NuGet ODATA (v2) API"
order: 5
---

NuGet Feeds in ProGet implement an older called the ODATA (v2) API. 

:::(Error) (💀 Avoid Using the NuGet ODATA (v2) API 💀)
The ODATA (v2) API is complex, undocumented, and can be very resource-intensive. NuGet.org officially deprecated this API in 2021 and ProGet NuGet Feeds only support an undocumented subset API queries.
:::

If you are looking to programmaticaly query NuGet packages in ProGet, we strongly recommend using the [Packages API](/docs/proget/reference-api/proget-api-packages). NuGet feeds in ProGet do implement the NuGet Server API, but it's a lot harder to use.


## NuGet.org and the ODATA (v2) API

On March 9, 2021, NuGet.org [stopped supporting most custom ODATA (v2) API queries](https://devblogs.microsoft.com/nuget/custom-v2-odata-queries-will-be-deprecated-march-9-2021/). This meant that any tool making these queries to NuGet.org  -- including older versions of ProGet -- will receive an error instead of the expected response.

### ProGet 5.2 and Earlier

If you are using ProGet 5.2 or lower, you will notice that some features of the ProGet connectors no longer work:

* **Connector Filters**, which relied on custom ODATA queries, will no longer function and will cause the connector to essentially break; you'll need to disable connector filters if you wish to still use ProGet 5.2 or older
* **Connector Health Status**  may always show an error or display an an incorrect package count

It's likely that other queries will stop working on NuGet.org in the future, so we strongly recommend upgrading to a modern version of ProGet.

### ProGet 5.3 and Later

If you have already upgraded to ProGet 5.3+, you are not directly affected.  ProGet will always use the JSON-LD (v3) endpoint ( `https://api.nuget.org/v3/index.json`) if the connector host name is nuget.org. You can add an override token (`#v2`) at the end of a nuget.org URL, such as `http://nuget.org/api/v2#v2`, to force the ODATA (v2) protocol if you really want to break your NuGet feed.

## ProGet and the ODATA (v2) API

Unless you disable the ODATA (v2) API on your NuGet feed, ProGet will still respond to ODATA (v2) API requests. ProGet will forward these requests to connectors, including NuGet.org, which may result in errors.

When an unsupported query is used against NuGet.org, ProGet will log the error and display a special error message in the ProGet UI. Unsupported queries can be viewed in the Diagnostics Center under the "NuGet v2 Bad Requests" message category. This message can be turned off from the Manage Feed page of the respective feed.
