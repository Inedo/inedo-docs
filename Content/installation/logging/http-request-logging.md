---
title: "HTTP Request Logging"
order: 1
---

Beginning with ProGet 2025.10, the Integrated Web Server now supports W3C HTTP Request logging. By enabling HTTP Request logging, ProGet will log all HTTP request information to a log file in the W3C Extended Log File Format. This log file can be used for auditing, monitoring, and troubleshooting purposes.

## Enabling HTTP Request Logging

To enable HTTP Request logging in ProGet, navigate to **Administration -> HTTP/S & Certificate Settings** and then click the "edit" button in the "Web Server Configuration" section.  In the dialog that appears, check the "Enable HTTP Request Logging" checkbox and then click "Save".  By default, the log files will be stored in `C:\ProgramData\ProGet\Logs` on Windows and `/var/proget/logs` on Linux, but you can change this by modifying the HTTP Log File Path field.

![](/resources/docs/httprequestlogging/edit-web-server-settings.png){height="" width="50%"}

::: (Warn)
It is not recommended to change the log file path when running on Linux.  The Linux folder path is configured as a volume in the docker image, and changing the path may result in loss of log data when the container is restarted.
:::

## Log File Management

Log files will grow to a maximum size of 512MB and ProGet will retain up to 6 log files.  When the maximum size is reached, ProGet will create a new log file and delete the oldest log file if there are more than 6 log files.

## Log File Format
The log files are stored in the W3C Extended Log File Format, which is a standard format for logging HTTP requests. Each log file contains a header section that describes the fields in the log file, followed by a series of log entries, one per line. Each log entry contains the following fields.  If a value is not available, a hyphen (-) will be used.

* **date**: The date when the request was received.
* **time**: The time when the request was received.
* **c-ip**: The IP address of the client making the request.
* **cs-username**: The username of the authenticated user making the request.
* **s-computername**: The name of the server handling the request.
* **s-ip**: The IP address of the server handling the request.
* **s-port**: The port number of the server handling the request.
* **cs-method**: The HTTP method used for the request (e.g., GET, POST).
* **cs-uri-stem**: The URI stem of the requested resource.
* **cs-uri-query**: The query string of the requested resource.
* **sc-status**: The HTTP status code returned to the client.
* **time-taken**: The time taken to process the request, in milliseconds.
* **cs-version**: The version of the HTTP protocol used for the request.
* **cs-host**: The host header sent by the client.
* **cs(User-Agent)**: The user agent string sent by the client.
* **cs(Referer)**: The referer header sent by the client.
* **cs(x-client-ssl-protocol)**: The SSL protocol used by the client.
* **cs(x-forwarded-for)**: The original IP address of the client, if the request was forwarded.
* **cs(X-forwarded-host)**: The original host requested by the client, if the request was forwarded.
* **cs(X-forwarded-port)**: The original port used by the client, if the request was forwarded.
* **cs(X-forwarded-proto)**: The original protocol used by the client, if the request was forwarded.