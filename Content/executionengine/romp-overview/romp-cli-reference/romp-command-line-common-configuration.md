---
title: "Common Configuration"
order: 3
---

Any of the values specified in [Romp configuration reference](/docs/executionengine/romp-overview/installing-configuring-and-maintaining/romp-installation-configuration) may be specified as an argument.

### Common Package Source Arguments

Many commands have the same arguments used to specify a package source. The names and descriptions of these arguments are documented here for consistency.


|  |  |  |
| --- | --- | --- |
| `source` | value | In package source mode, this refers to either the package source name or a URL to a Universal Feed API endpoint. If the argument begins with `http://` or `https://`, it will be treated as a URL. |
| `authentication` | value | Either the API Key, or a username/password to use for authentication to Universal API Feeds. <p><p> If this argument contains exactly one colon, it is treated as a username:password pair. Otherwise, it’s treated as an API key.  <p><p>Specifying this argument without a source URL will result in an error. |











