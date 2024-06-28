---
title: "Native API Methods"
order: 3
---

::: (ERROR)
The other endpoints should be used instead of the Native API Methods if possible, as they are much easier to use and are unlikely to change from version to version.
:::

## API Usage

All of these methods require that an API key with Native API Access is passed into each request; see _Using API Keys_ for more information.

## Method Listing

​Native API Methods are automatically documented on a special  page in the software. To see the API Methods for your specific version, go to Administration > Reference > API Methods, or directly visit `http://your-buildmaster-installation/reference/api` in your installation.

## Using the Native API

There are three interfaces for the native API: JSON, SOAP, and SQL. The native API interfaces take the same parameters and return the same data, so you should use the interface best suited for your programming environment.

### JSON-based Access

The JSON interface for the native API can be used by any program that can make HTTP requests and decode JSON. To retrieve a list of database change scripts for an application, we need to perform two requests:

GET or POST /api/json/**Applications\_GetApplications**?key=«api-key»

The first request gives us a JSON array containing JSON objects with properties of applications in BuildMaster. There are many properties, but for now, we're only interested in `Application_Name` and `Application_Id`. Find the `Application_Id` of the application you want and feed it in the next request:

GET or POST /api/json/**DatabaseChangeScripts\_GetChangeScripts**?key=«api-key»&Application\_Id=«application-id»

The response contains a JSON array of JSON objects with properties about the application, release, deployable, and script, as well as the usernames and timestamps of the creation and last modification the script.

### SOAP-based Access

The SOAP interface for the native API is useful for .NET programmers. Visual Studio can automatically generate code to connect to a SOAP API. To do this, add a **Service Reference** to your project with the URL `http://your-buildmaster-installation/api/soap`.

![soap-reference.png](/resources/docs/soap-reference.png){height="" width=""}

Here is an example of code that interacts with the SOAP API:

```
using System;
using System.Data;
using System.Linq;

namespace SoapApiExample
{
static class Program
{
    const string APIKey = "«api-key»";
    const string ApplicationName = "«application-name»";

    static void Main(string\[\] args)
    {
        using (var client = new buildmaster.ApiServiceSoapClient("ApiServiceSoap"))
        {
            var applicationId = (from app in client.Applications\_GetApplications(APIKey, null, null).AsEnumerable()
                                 where string.Equals(app.Field<string>("Application\_Name"), ApplicationName, StringComparison.OrdinalIgnoreCase)
                                 select app.Field<int>("Application\_Id")).First();

            var releases = from script in client.DatabaseChangeScripts\_GetChangeScripts(APIKey, applicationId, null, null, null).AsEnumerable()
                           let s = new
                           {
                               Release\_Number = script.Field<string>("Release\_Number"),
                               Script\_Name = script.Field<string>("Script\_Name")
                           }
                           group s by s.Release\_Number;

            foreach (var scripts in releases)
            {
                Console.WriteLine($"Release {scripts.Key}");
                foreach (var s in scripts)
                {
                    Console.WriteLine(s.Script\_Name);
                }
                Console.WriteLine();
            }
        }
    }
}
}
```
### SQL-based Access

The SQL interface for the native API is useful for one-off applications because the code can run directly in the database, saving you the trouble of writing a full program.

The SQL interface also has access to some procedures that are not available in the JSON or SOAP interfaces. These internal procedures are not supported, but can be useful when writing a script. Also, the SQL interface is not limited to procedures, as it works directly with the database. In some cases, it may be easier to write one large query instead of many smaller queries.

Despite the greater power (and thus danger) of the SQL interface, it can be used in the same way as the JSON or SOAP interfaces:
```
DECLARE @Application\_Id INT

SELECT @Application\_Id = \[Application\_Id\] FROM \[Applications\] WHERE \[Application\_Name\] = '«application-name»'

EXEC \[DatabaseChangeScripts\_GetChangeScripts\]
@Application\_Id = @Application\_Id
```