---
title: "Getting Started with HTTP Endpoints"
order: 2
---

Although we recommend using [pgutil](/docs/proget/api/pgutil) for programmatic access to ProGet, you can also use the HTTP Endpoints that `pgutil` uses for your own scripts and programs. This may be particularly helpful if you need to work with structured data or wish to create a more advanced integration.

## Authenticating to HTTP Endpoints

Before using an HTTP endpoint, you should first [Create an API Key](/docs/proget/api/apikeys/create) with the appropriate permissions.

::: (Warning)
Although most HTTP Endpoints in ProGet can *technically* be used without authentication, this requires giving the `Anonymous` user access to perform those actions. While this may be fine for initial configuration, it's not appropriate for production use.
:::

The best way to authenticate with an API Key is to specify it in the `X-ApiKey` HTTP header. For example:

```bash
curl --header "X-ApiKey: 70f875c2593ef93d0b21400d5f94ea1d8ed519d0" https://proget.corp.local/api/promotions/list?package=Newtonsoft.Json&version=13.0.2
```

However, this is not the only way to use an API Key. An API key may be passed to any endpoint in one of four ways, depending on the content type of the expected request:

| Method | Name or Key | Details |
|----|----|----|
|Request header| `X-ApiKey` | *all content types* |
|Querystring value| `key` | *all content types* |
|Form value| `key` | only `applications/x-www-form-urlencoded` content type |
|JSON property | `API_Key` | on root object, only `application/json` content type |

Note that [Feed Endpoints](#feed-endpoints) do not use this convention. 

## Feed Endpoints

ProGet implements a number of third-party APIs called *feed endpoints*. These are used by third-party tools like Visual Studio, `pip`, `npm`, `docker`, etc., and their URL is listed on the feed page.

:::(Info) (⭐Best Practice: Avoid Feed Endpoints)
We did not design the Feed Endpoint APIs. We implemented specifications when available and reverse-engineered client tools. As such, they are only been tested with official client tools and they may not work with your scripts. You should instead use `pgutil` or the preferred endpoints.
:::

Because we did not design the Feed Endpoint APIs, we do not provide documentation on how to interact with them. If you need to use these endpoints, you can usually find the basics by searching for specific things you want to do with the API, such as "how to search for packages using the NuGet API" or "how to publish an npm package using the API".

## Feature Requests for new HTTP Endpoints

Prior to creating the `pgutil` command-line tool, we created HTTP Endpoints based around a particular feature (such as feed configuration or asset management) that users would want to automate using a script or program. Many of the endpoints were developed in collaboration with users.

Starting in ProGet 2024, we are aligning our HTTP Endpoints and developing new ones using a *`pgutil`-first* approach. This means we will prioritize the CLI experience by creating intuitive and self-documenting commands. Then we will use existing HTTP Endpoints and develop new ones to fit those commands.

We're still very open to developing and enhancing our HTTP endpoints, and working closely with users to do that. You're welcome to simply post a request to the [Inedo Forums](https://forums.inedo.com/), but may also be able to show us exactly how you'd like an HTTP Endpoint to work by prototyping something in the open-source `pgutil`.

##  Native API Endpoints

The Native API is a wrapper for some of the stored procedures in the SQL Server database. These methods are used extensively by ProGet itself and are subject to change between releases (even in maintenance releases).

We do not document changes to these methods nor do we provide details on how to use them. Instead, you can see a listing and some basic, auto-generated guidance by navigating to Administration > Reference > API Methods, or directly visiting `http://your-proget-installation/reference/api` in your installation.
  
:::(Info) (⭐Best Practice: Avoid Native API)
We don't recommend using the Native API unless absolutely necessary; we do not document usage and the methods are subject to removal or change, even in maintenance versions.
:::

All of these methods require that an API Key with Native API Access is passed into each request; see [API Keys & Access](/docs/proget/api/apikeys) for more information.

```plaintext
URL:
http://feeds.fd.local:2000/api/json/Feeds_CreateFeed

Header:
Content-Type: application/json

Body:
{
"Feed_Name" : "MyNewNuGetFeed",
"FeedType_Name" : "nuget",
"API_Key" : "«api-key»"
}
```

You can also use querystrings with the JSON-based API. To delete a license and all associated rules, we need to perform two requests:

```plaintext
GET or POST /api/json/Licenses_GetLicenses?key=«api-key»
```

The first request gives us a JSON array containing JSON objects with properties of licenses in ProGet. Each license is uniquely identified with a `License_Id`, which we will feed into the next request:

```plaintext
POST /api/json/Licenses_DeleteLicense?key=«api-key»&License_Id=«application-id»
```

The response contains no data, as it's just a delete, and the built-in reference documentation indicates no return.

### Invoking Stored Procedures Directly

Those familiar with SQL Server and database coding may find it simpler to invoke the stored procedures directly instead of going through the Native API. This is particularly useful for one-off scripts and it can be used in conjunction with `SELECT` statements against other tables in the database.

For example:
```sql
DECLARE @License_Id INT

SELECT @License_Id = [License_Id] FROM [Licenses] WHERE [External_Id] = '«MIT, etc.»'

EXEC [Licenses_DeleteLicense] @License_Id = @License_Id
```

Just make sure to stick to the methods listed under `/reference/api`. There are many other stored procedures in the ProGet database that are not intended for execution by anything except the ProGet software. Note we also do not recommend or support other modifications except when explicitly documented (like purging download records).