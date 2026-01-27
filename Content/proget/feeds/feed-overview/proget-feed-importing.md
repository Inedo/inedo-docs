---
title: "Importing from Other Feeds/Servers"
order: 4
---

Feed Importers, available in ProGet 2025.0+ (2025.13+ for docker), allow packages from a remote feed to be downloaded into a local ProGet feed. This feature is intended for migration scenarios and is not supported for public open-source repositories, which typically contain an extremely large number of packages. In addition, only local (hosted) packages on the source server can be imported. Proxied packages cannot be imported.

Importers use native APIs to retrieve a list of packages from the source server and then download those packages into ProGet. For Docker they will retrieve a list of images and then uses the Docker API to pull those images into ProGet. Importers can be run multiple times. Each run only adds new packages that do not already exist in the target feed.

You can access this feature by navigating to the dropdown and selecting "Import Packages" in a local feed (e.g. `internal-nuget`).

![Feed Import](/resources/docs/proget-importpackages.png){height="" width="50%"}

Then select "Download Package From Another Service" to connect to a remote feed.

![Remote Feed Connect](/resources/docs/proget-downloadpackage-azure.png){height="" width="50%"}

From here you can choose to import from ProGet, Azure Artifacts, JFrog Artifactory, or Sonatype Nexus.

## Supported Servers

ProGet can import packages from other ProGet instances, Azure Artifacts, JFrog Artifactory, or Sonatype Nexus. The package types that can be imported will depend on the source server type that you are importing your package from.

All servers can be configured to preserve the **Published Date** from the source server. This setting is optional; by default, ProGet assigns the current date when a package is imported.

If enabled, retention rules may be affected because usage statistics are not included in the import. Additionally, not all server types support retrieving the original published date, so this option may not apply in every scenario.

### ProGet Servers
To create a connection to another ProGet feed you will need the ProGet Server URL and an API Key or User credentials to authenticate to the feed. Note that The API Key or User requires permission to view and download packages.

![](/resources/docs/proget-importing-proget.png){height="" width="50%"}

Also, the remote feeds while show in a drop down. These feeds will only include hosted packages. They will not include proxied packages, which cannot be imported. 

![](/resources/docs/proget-importing-proget-feedselect.png){height="" width="50%"}

All Feed Types can be imported from other ProGet servers. ProGet uses Packages API for package importing. This allows the other servers to be ProGet 2023+.

:::(info)(Importing Docker Images)
Docker images can be imported as of ProGet 2025.13. For servers below 2025.13, you will need to remove any Docker connectors prior to importing. Imports are blocked if connectors are detected.
:::

### Azure Artifacts
To create a connection to an Azure Artifacts server you will need your Azure DevOps Name, Organization and Personal Access Token. Note that your Personal Access Token requires read privileges on the Packaging scope and Project and Team scope.

![](/resources/docs/proget-importing-azure.png){height="" width="50%"}

The following package types can be imported from Azure Artifacts servers:

|  |  |  |
|---|---|---|
| NuGet | Chocolatey | PowerShell |
| npm | Maven | PyPi |
| Cargo |

You can read more about importing from Azure Artifacts in in our [HOWTO: Migrate from Azure DevOps to ProGet](/docs/proget/installation/migrating-to-proget/proget-azure-devops-feed-migration) guide. 


### JFrog Artifactory
To create a connection to a JFrog Artifactory server you will need your JFrog URL and Access Token or Username and Password. Note that the Access Token or Username/Password needs read permissions to the Repositories API, File List API, AQL Queries, and Download Files API.

![](/resources/docs/proget-importing-jfrog.png){height="" width="50%"}

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
To create a connection to a JFrog Artifactory server you will need your Credentials Name, Nexus URL, Username, and Password. Note that this user requires read to the the Repositories API and the Components API and the ability to download assets.

![](/resources/docs/proget-importing-sonatype.png){height="" width="50%"}

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


## Importing from Other Server Types

All Other Server Types should use "Import/Copy Package Files on Disk" or scripted upload via [pgutil](/docs/proget/api/pgutil)

![](/resources/docs/proget-import-packages-disk.png){height="" width="50%"}

You can read more about importing from other servers in in our [HOWTO: Migrate from Another Repository to ProGet](/docs/proget/installation/migrating-to-proget/proget-other-feed-migration) guide. 

## Editing Connection Credentials

Once credentials are entered, they are saved and can be reused. Credentials can be entered by starting import again and selecting "manage your connections" to navigate to your connection settings. 

![](/resources/docs/proget-importing-manage-connections.png){height="" width="50%"}

From here, select the existing connection, where you will be able to re-enter your URL, API key, etc.

![](/resources/docs/proget-importing-edit.png){height="" width="50%"}

## ProGet 2024 and Below

Feed importing is also available in versions of ProGet prior to 2025, but the configuration process is slightly different. Refer to [Migrating ProGet Feeds Across Instances](https://github.com/Inedo/inedo-docs/blob/a73f0aedc0b71c30387f74a8648235f1a85a6b3c/Content/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed.md) for instructions on setting this up in those versions.