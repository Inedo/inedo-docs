---
title: Getting Source Code From TFS or Azure DevOps
Docs URL: /builds/continuous-integration/azure-devops-tfs
Pre-requisites: Git extension, TFS extension
---

BuildMaster can build any type of project, whether greenfield, legacy applications, microservices, etc. It doesn’t matter where your project’s source code is hosted.

Both Azure DevOps Services (hosted by Microsoft) and Azure DevOps Server (formerly Team Foundation Server, hosted on premise) offers [two types of source control](https://docs.microsoft.com/en-us/azure/devops/user-guide/source-control?view=azure-devops): Git (distributed) and Team Foundation Version Control (TFVC). 

Your organization may have projects spread across a variety of hosted accounts and on-premise servers, both in Git and TFVC repositories, and BuildMaster will help you can centralize all of these connections in one place, so you can build and deploy consistently, and migrate from one format to another with minimal changes to your plans.

### How to Build from Azure DevOps Services or Server Using Git Source Control

1. Browse to your solutions repository page in __Azuze DevOps Server__.
2. Click _Clone_.
3. Copy the HTTPS repository Url.
4. Click _Generate Git Credentials_ to open the Git credential dialog section.
5. Fill in the username and password.
6. Click _Save Git Credentials_.
7. Go to Resource Credentials in the BuildMaster Administration area (_/administration/credentials_) and create a new _Git Resource Credential_ named AzureDevOps (or whatever makes the most sense), by using the repository url, username and password you just entered.
8. Add the "Get Source from Git Repository" operation to your Build Plan and enter your information. 

Sample Plan:
```
Git::Get-Source
(
    Credentials: AzureDevOps
);
```

_Project URLs have changed with the release of Azure DevOps Services and now have the format dev.azure.com/{your organization}/{your project}, but you can still use the existing visualstudio.com format._

#### Additional Options
You can further customize this operation by specifying values for these additional options:

- Export to directory: _Allows you to choose a path in your target environment where the source will be saved_
- Branch name: _The Specific branch you want to retrieve i.e. Master/Development_
- Reference: _A reference such as a tag name or a commit hash_  
- Commit hash: _The full SHA1 hash of the fetched commit will be stored in this variable._
- Copy internal: _When exporting the repository, also export .git* files._
- Git executable path : _Path to the git executable file for running command line operations_
- Recurse submodules : _When true, this will recurse through all submodules whenr fetching source code_
- Workspace disk path _If not set, a workspace name will be automatically generated and persisted based on the Repository URL or other host-specific information (e.g. GitHub's repository name)._
- Clean workspace: _If set to true, the workspace directory will be cleared before any Git-based operations are performed._


### How to Build from Team Foundation Version Control (TFVC)
BuildMaster supports source code that is managed by TFVC as well. However, getting your source code from Team Foundation Version Control will differ slightly from the Azure DevOps operation. 

1. Install the TFS extention if its not currently installed. Extensions are found in the administration section under extenstions  (_/administration/extensions_)
2. In the BuildMaster Administration area under Resource Credentials (_/administration/credentials_), create a new _TFS Resource Credential_ named TFS (or whatever makes the most sense) and use your TFVC repository url, username and password to complete the dialogue.
3. Add the "TFS Get Source" operation (The TFS extension will need to be installed first (_/administration/extensions_) to your Build Plan and enter your information. 
4. Your Source Path field is mandatory and will need to be in this format:
    ```
    `$/<projectName>/<RepositoryName>
    ```

Sample Plan:
```
TFS::Tfs-GetSource
(
    Credentials: TFS,
    SourcePath: `$/<projectName>/<RepositoryName>
);
```

#### Additional Options
You can further customize this operation by specifying values for these additional options:
- Export to directory :  _Allows you to choose a path in your target environment where the source will be saved_
- Label: _This gives you some flexibility regarding the exact version of the code you want to acquire_
- Workspace Name : _Additional customization_ 
- Workspace Disk Path: _Path where temporary work will occur_

You may want to bypass creating credentials and simply utilize the following options within the TFS-GetSource operation.

- Project collection URL:  _Url to TFVC source code_
- User name
- Password / token
- Domain name 

