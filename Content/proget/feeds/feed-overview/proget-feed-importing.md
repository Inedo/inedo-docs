---
title: "Importing from Other Feeds/Servers"
order: 4
---

Feed Importers, available in ProGet 6.0.6, allow packages from a remote feed to be downloaded into a local ProGet feed. This feature is intended for migration scenarios and is not supported for public open-source repositories, which typically contain an extremely large number of packages. In addition, only local (hosted) packages on the source server can be imported. Proxied packages cannot be imported.

Importers use native APIs to retrieve a list of packages from the source server and then download those packages into ProGet. For Docker they will retrieve a list of images and then uses the Docker API to pull those images into ProGet. Importers can be run multiple times. Each run only adds new packages that do not already exist in the target feed.

You can access this feature by navigating to the dropdown and selecting "Import Packages" in a local feed (e.g. `internal-nuget`).

![Feed Import](/resources/docs/proget-importpackages.png){height="" width="50%"}

Then select "Download Package From Another Service" to connect to a remote feed.

![Remote Feed Connect](/resources/docs/proget-downloadpackage-azure.png){height="" width="50%"}

## Creating a Connection

To create a connection to another feed or server you will need the repository/feed URL and authentication credentials such as an API key:

![](/resources/docs/proget-importing-manage-connections.png){height="" width="50%"}

Note that once credentials are entered, they are saved and can be reused. Credentials can be entered by starting import again and selecting "manage your connections" to navigate to your connection settings. 

![](/resources/docs/proget-importing-manage-connections.png){height="" width="50%"}

From here, select the existing connection, where you will be able to re-enter your URL, API key, etc.

## Supported Servers

The package types that can be imported will depend on the source server type that you are importing your package from.

All servers can be configured to preserve the **Published Date** from the source server. This setting is optional; by default, ProGet assigns the current date when a package is imported.

If enabled, retention rules may be affected because usage statistics are not included in the import. Additionally, not all server types support retrieving the original published date, so this option may not apply in every scenario.

### ProGet Servers
All Feed Types can be imported from other ProGet servers.

:::(info)(Importing Docker Images)
Docker images can be imported as of ProGet 2025.13. For servers below 2025.13, you will need to remove any Docker connectors prior to importing. Imports are blocked if connectors are detected.
:::

### JFrog Artifactory
The following feed types can be imported from JFrog Artifactory servers:

|  |  |  |
|---|---|---|
| Alpine (APK) | NuGet | Chocolatey |
| PowerShell | npm | Maven |
| PyPi | Cargo | Composer |
| Debian | Ruby Gems | Helm |
| Dart/Flutter (Pub) | RPM | Conda |
| Cran | Docker**  |  |

**\* as of ProGet 2025.13*

You can read more about importing from JFrog Artifactory in our [HOWTO: Migrate from JFrog Artifactory to ProGet](/docs/proget/installation/migrating-to-proget/proget-jfrog-artifactory-feed-migration) guide. 

### Sonatype Nexus
The following feed types can be imported from Sonatype Nexus servers:

|  |  |  |
|---|---|---|
| NuGet | Chocolatey | PowerShell |
| npm | Maven | PyPi |
| Cargo | Debian | RubyGems |
| Helm | RPM | Cran |
| Docker** |

**\* as of ProGet 2025.13*

:::(info)(Importing Docker Images from Sonatype Nexus)
When importing Docker images from Sonatype Nexus, by default, ProGet builds the Docker API URL based on the registry settings reported by Nexus. This override is primarily used when a reverse proxy is configured in front of the Nexus server and the reported URL is not directly accessible. By setting this value, ProGet will use the specified Docker API endpoint instead of the one taken from the Nexus configuration.

You can configure settings on the advanced tab to specify a custom Docker API URL (for example, https://nexus.kramerica.com/v2/my-registry). 
:::

You can read more about importing from Sonatype Nexus in our [HOWTO: Migrate from Sonatype Nexus to ProGet](/docs/proget/installation/migrating-to-proget/proget-sonatype-nexus-feed-migration) guide. 

### Azure DevOps
The following feed types can be imported from Azure DevOps servers:

|  |  |  |
|---|---|---|
| NuGet | Chocolatey | PowerShell |
| npm | Maven | PyPi |
| Cargo |

You can read more about importing from Azure DevOps in in our [HOWTO: Migrate from Azure DevOps to ProGet](/docs/proget/installation/migrating-to-proget/proget-azure-devops-feed-migration) guide. 

### Other Servers

All Other Server Types should use "Import/Copy Package Files on Disk" or scripted upload via [pgutil](/docs/proget/api/pgutil)

![](/resources/docs/proget-import-packages-disk.png){height="" width="50%"}

You can read more about importing from other servers in in our [HOWTO: Migrate from Another Repository to ProGet](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration) guide. 