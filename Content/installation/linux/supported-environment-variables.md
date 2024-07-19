---
title: "Supported Environment Variables"
order: 4
---

Environment variables can be used to specify certain values that are normally stored in your Inedo product's configuration file. Environment variables are intended for use with Docker, but may be used on any platform.

#### BuildMaster
| Environment Variable | Description 
| ----------------------- | --------
| `BUILDMASTER_SQL_CONNECTION_STRING` | The connection string for your SQL Server. The value specified in the example here assumes you are using the `buildmaster-sql` container. To connect to a different instance, just change this connection string to the appropriate value. Enclosed in single quotes. <br/><br/> _Example:_ `-e BUILDMASTER_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=BuildMaster; User ID=sa; Password=«YourStrong!Passw0rd»'`
| `BUILDMASTER_SQL_CONNECTION_STRING_FILE` | Can be used instead of the connection string above to read the connection string from file location within the container. This facilitates the use of Docker secrets to prevent passing clear text passwords in a Docker stack file.  A Docker secret must be set first and 'called' within the stack file. <br/><br/> _Example:_ `-e BUILDMASTER_SQL_CONNECTION_STRING_FILE='/home/buildmaster/secrets/buildmaster_connection_string'`
| `TZ` | Used to specify the timezone of the container. Can be set to any [TZ database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) <br/><br/> _Example:_ `-e TZ='America/Denver'`
| `BUILDMASTER_ENCRYPTION_KEY` | This specifies the 32-character hex encryption key BuildMaster uses to store some secrets.<br/><br/> _Example:_ `-e BUILDMASTER_ENCRYPTION_KEY='37D27A670394F7D82CE57F1F07D69747'`
| `BUILDMASTER_ENCRYPTION_KEY_FILE` | This specifies the full path of a text file that contains the encryption key described above. <br/><br/> _Example:_ `-e BUILDMASTER_ENCRYPTION_KEY_FILE='/home/buildmaster/secrets/buildmaster_encryption_key'` 
| `ASPNETCORE_URLS` | This specifies the host and port Docker uses for HTTP connections.<br/><br/> _Example_: `-e ASPNETCORE_URLS='http://*:8181'` |

#### ProGet
| Environment Variable | Description  
| ----------------------- | --------
| `PROGET_SQL_CONNECTION_STRING` | The connection string for your SQL Server. The value specified in the example here assumes you are using the `proget-sql` container. To connect to a different instance, just change this connection string to the appropriate value. Enclosed in single quotes. <br/><br/> _Example:_ `-e PROGET_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=ProGet; User ID=sa; Password=«YourStrong!Passw0rd»'`
| `PROGET_SQL_CONNECTION_STRING_FILE` | Can be used instead of the connection string above to read the connection string from file location within the container. This facilitates the use of Docker secrets to prevent passing clear text passwords in a Docker stack file.  A Docker secret must be set first and 'called' within the stack file. <br/><br/> _Example:_ `-e PROGET_SQL_CONNECTION_STRING_FILE='/home/proget/secrets/proget_connection_string'`
| `TZ` | Used to specify the timezone of the container. Can be set to any [TZ database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) <br/><br/> _Example:_ `-e TZ='America/Denver'`
| `PROGET_ENCRYPTION_KEY` | This specifies the 32-character hex encryption key ProGet uses to store some secrets.<br/><br/> _Example:_ `-e PROGET_ENCRYPTION_KEY='37D27A670394F7D82CE57F1F07D69747'`
| `PROGET_ENCRYPTION_KEY_FILE` | This specifies the full path of a text file that contains the encryption key described above. <br/><br/> _Example:_ `-e PROGET_ENCRYPTION_KEY_FILE='/home/proget/secrets/proget_encryption_key'`
| `ASPNETCORE_URLS` | This specifies the host and port Docker uses for HTTP and HTTPS connections.<br/><br/> _Example HTTP_: `-e ASPNETCORE_URLS='http://*:8181'`<br/>_Example HTTPS:_ **ProGet v2022.18+ ONLY** `-e ASPNETCORE_URLS='http://*:80;https://*:443'`|
| `SSL_CERT_FILE` | **ProGet v2022.18+ ONLY** Specifies the name of the ".pem" certificate file in the `/var/proger/ssl/` directory. |
| `SSL_KEY_FILE` | **ProGet v2022.18+ ONLY** Specifies the name of the certificate kye file in the `/var/proger/ssl/` directory. |

::: (INFO)
**For ProGet 5.3 and earlier**, use `SQL_CONNECTION_STRING` instead of `PROGET_SQL_CONNECTION_STRING`, and also add `-e PROGET_DB_TYPE=SqlServer`.
:::

#### Otter
| Environment Variable | Description
| ----------------------- | --------
| `OTTER_SQL_CONNECTION_STRING` | The connection string for your SQL Server. The value specified in the example here assumes you are using the `otter-sql` container. To connect to a different instance, just change this connection string to the appropriate value. Enclosed in single quotes. <br/><br/> _Example:_ `-e OTTER_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=Otter; User ID=sa; Password=«YourStrong!Passw0rd»'`
| `OTTER_SQL_CONNECTION_STRING_FILE` | Can be used instead of the connection string above to read the connection string from file location within the container. This facilitates the use of Docker secrets to prevent passing clear text passwords in a docker stack file.  A docker secret must be set first and 'called' within the stack file. <br/><br/> _Example:_ `-e OTTER_SQL_CONNECTION_STRING_FILE='/home/otter/secrets/otter_connection_string'`
| `TZ` | Used to specify the timezone of the container. Can be set to any [TZ database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) <br/><br/> _Example:_ `-e TZ='America/Denver'`
| `OTTER_ENCRYPTION_KEY` | This specifies the 32-character hex encryption key Otter uses to store some secrets.<br/><br/> _Example:_ `-e OTTER_ENCRYPTION_KEY='37D27A670394F7D82CE57F1F07D69747'`
| `OTTER_ENCRYPTION_KEY_FILE` | This specifies the full path of a text file that contains the encryption key described above. <br/><br/> _Example:_ `-e OTTER_ENCRYPTION_KEY_FILE='/home/otter/secrets/otter_encryption_key'`
| `ASPNETCORE_URLS` | This specifies the host and port Docker uses for HTTP connections.<br/><br/> _Example_: `-e ASPNETCORE_URLS='http://*:8181'` |