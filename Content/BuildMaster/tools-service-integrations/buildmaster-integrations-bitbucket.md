---
title: "Bitbucket"
order: 11
---

[Bitbucket](https://bitbucket.org) is a Git based code hosting and collaboration tool, built for teams. Bitbucket offers both on-premise and cloud editions. While BitBucket does have its own version of a CI/CD platform, it requires an organization's projects to be migrated to the Bitbucket's platform in order to use it.

BuildMaster is designed to work *alongside* Bitbucket to provide a self-managed CI/CD solution using BuildMaster's [Git Integration](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control).

## Connecting to Bitbucket
BuildMaster's [Git Integration](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control) allows you to connect to Bitbucket at application creation or through the application's settings page.  To connect BuildMaster to Bitbucket:

::: (Internal) (TODO)
Update screen shots to be bitbucket
:::

1. Either create a new application or add a new connection on the application settings page, then select Git Repository
![select-connection-type](/resources/docs/select-connection-type.png){width="50%"}
2. Select the BitBucket connection type
![git-connect-to-service](/resources/docs/git-connect-to-service.png){width="50%"}
3. Enter your BitBucket user name and [http access token](#Generating-a-Bitbucket-HTTP-Access-Token) and click Select Bitbucket Repository
![bitbucket-connect-to](/resources/docs/bitbucket-connect-to.png){width="50%"}
4. Select your Bitbucket namespace and enter or select your repository and click Save Repository
![bitbucket-connect-repository](/resources/docs/bitbucket-connect-repository.png){width="50%"}

The Bitbucket repository will then be [synchronized to your server](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#synchronized-git-repositories) and you will see a successful synced message.
![bitbucket-successfully-synced](/resources/docs/bitbucket-successfully-synced.png){width="50%"}

### Editing a BitBucket Connection 

You can edit these items directly to enable advanced scenarios (such as a shared repository) by clicking view all on the Settings page, or by going to Admin > Credentials & Resources.  See [Credentials & Resources](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials) to learn more.

## Generating a Bitbucket HTTP Access Token

When connecting BuildMaster to Bitbucket, you need to use a [Bitbucket HTTP Access Token](https://confluence.atlassian.com/bitbucketserver/http-access-tokens-939515499.html). To generate this token:
1. Navigate to your Bitbucket project or repository
2. In the upper right corner, click your user icon, and then click _Manage account_
3. Navigate to _HTTP access tokens_
4. Click Create token
5. Enter a token name select full control for the project and repository permissions
6. Click Create

## Browsing Bitbucket Repositories

After adding a synchronized Bitbucket repository to an application, you'll be able to browse that repository's branches, source code, and commits—as well as the changes between builds/releases—directly from BuildMaster's web UI.  See our [synchronized Git repositories documentation](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#synchronized-git-repositories) to learn more.

::: (Info) (Note:)
When viewing branch, tag, or commit details, you can also click the "browse on Bitbucket" link to view details in Bitbucket directly.
:::

## Bitbucket and Scripting
When your application uses a synchronized Bitbucket repository, you'll be able to create a build from any commit in the repository. When creating a build in this manner, the selected commit - along with its branch and repository - will be associated with that build and displayed throughout the UI. You can access those values in scripts with the `$Commit`, `$Branch`, and `$Repository` variable functions.  See [Git Build Scripts & Automations](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#build-scripts-operations) for more information.

### Bitbucket Git Operations

The Bitbucket integration supports most of the standard [BuildMaster Git operations](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#build-scripts-operations), but there are a couple things to keep in mind when using these operations.  One operation that is not supported is the `Git::Set-CommitStatus` operation.  Bitbucket does not support Git commit statuses at this time.


#### Using Other Git Repositories
By default, `Git::Checkout-Code` will use your Git Connection to connect to your repository.  If you would like to select a repository that is different than what your Git connection specifies; navigate to your repository in Bitbucket, click the "Clone" button and copy the URL under Clone with HTTPS, then set the `RepositoryUrl` parameter to it in the`Git::Checkout-Code` operation.

In cases like this, you can specify input parameters like this.
```
Git::Checkout-Code
(
    RepositoryUrl: https://bitbucket.org//inedo/ProfitCalcNet.git,
    BranchOrCommit: master,
    To: c:\build\space\src,
    CommitHash => $CommitId
);
Log-Information Checked out at $CommitId from my-repo;
```

#### Git::Set-CommitStatus Operation

::: (Warning) (Unsupported)
Git commit statuses are currently not supported in Bitbucket.
:::
 
 #### Git::Create-PullRequest and Git::Merge-PullRequest

Just like `Git::Checkout-Code`, the `Git::Create-PullRequest` operation will use your Git Repository connection to create a pull request in Bitbucket.  Here is an example of using a repository that is different than what your Git Repository connection specifies:

```
Git::Create-PullRequest
(
    Title: My New Thing,
    Target: main,
    Repository: https://bitbucket.org//inedo/ProfitCalcNet.git
);
```

This operation will also record the ID of the Pull Request on the Build, so that you can later access it with `$PullRequestId`.

:::(Info) 
The [Feature Branch Workflow Pipeline](/docs/buildmaster/builds-continuous-integration/buildmaster-ci-git-workflows/buildmaster-git-feature-branches) will create a script with the `Git::Create-PullRequest` operation. Try it out to see this in action.
:::

You may want to extend your Feature Branch workflow and let BuildMaster do the merging.

To complete a merge request, you can run the operation without parameters:
```
Git::Merge-PullRequest;
```

This will use the `$PullRequestId` if you don't specify it as a parameter.

## Installing the Bitbucket Extension 

The Bitbucket extension is installed by default when you install BuildMaster. You can upgrade the extension by navigating to the *Admin > Extensions* page in your instance of BuildMaster and clicking the update link to the right of the Bitbucket extension. Once the Bitbucket extension has been upgraded, it can be reverted back to the built-in version by navigating to *Admin > Extensions > Bitbucket* and then selecting the Change Version button in the upper right corner.

If your instance doesn't have Internet access, you can upgrade/downgrade it by [manually installing the Bitbucket extension](/docs/buildmaster/reference/extensions#manual-install) after downloading the [Bitbucket Extension Package](https://proget.inedo.com/feeds/Extensions/inedox/Bitbucket).

:::(Info) (NOTE:)
Built-in extensions cannot be deleted or downgraded past the included version.
:::