---
title: "Git Repository Monitors"
order: 1
---

Git repository monitors are a type of specialized [resource monitor](/docs/buildmaster/administration/buildmaster-resource-monitors) that monitor branches in [synchronized Git repositories](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control#synchronized-git-repositories) for new commits and perform actions like creating a new build or running a script.


## Monitoring Git Branches
 The easiest way to manage your application's Git repository monitors is on the Git branches page. You can navigate to this page within your application by going to Git > Branches.

![git-browse-branches](/resources/docs/git-browse-branches.png){width="50%"}

Next to each branch will be one or more symbol icons that you can click on:
| Symbol | Meaning |
| -- | -- |
| Greyed-out bolt  (<span style="opacity: 0.2">⚡</span>) | the branch isn't being monitored, and clicking on the symbol will open the New Git Repository Monitor page
| Single bolt (⚡) | the branch is being monitored, and clicking on the symbol will edit that monitor |
| Multiple bolts (⚡⚡) | the branch is being monitored by more than one Git repository monitor |

Because a single Git repository monitor can monitor multiple branches by using wildcard expressions (e.g. `features/*`), the same bolt-symbol icon may edit the same repository monitor.

### Adding a Git Repository Monitor
After clicking on a greyed-out bolt (<span style="opacity: 0.2">⚡</span>), you'll be prompted to select a branch:

![buildmaster-git-monitor-add1](/resources/docs/buildmaster-git-monitor-add.png){height="" width="50%"}

Then, an action:
![buildmaster-git-monitor-add2](/resources/docs/buildmaster-git-monitor-add2.png){height="" width="50%"}

These selections will be used to fill in the initial fields on the repository monitor editor page.

![buildmaster-git-monitor-edit](/resources/docs/buildmaster-git-monitor-edit.png){height="" width="50%"}

After adding a Git repository monitor, BuildMaster will start monitoring for changes and perform the selected action when a new commit is detected. See the [resource monitor documentation](/docs/buildmaster/administration/buildmaster-resource-monitors) to learn more about how these actions work.

### Viewing  Status and Branches

After adding a repository monitor, you'll be able to see the monitored branches and the last commit recorded by clicking on the repository monitor, then the view branches link.

![buildmaster-git-monitor-branchstate](/resources/docs/buildmaster-git-monitor-branchstate.png){height="" width="50%"}


The branches here will likely be different than what you see on the Git branches page. This page only shows the branches/commits that matched the filter the last time the repository was checked. Because resource monitors and the web-based browsing sync on different schedules, there may be a slight lag.

## Managing Git Repository Monitors
You can also manage Git Repository monitors from Application > Settings, or Administration > Resource Monitors. Unlike the Git branches page, which shows the branches that are being monitored, these pages show all of the Git repository monitors that are configured.

The Application Settings page will show all resource monitors and scheduled jobs within the application:

![buildmaster-git-monitor-settings](/resources/docs/buildmaster-git-monitor-settings.png){height="" width="50%"}

Likewise, the Administration Resource Monitor page will show all resource monitors and scheduled jobs across all applications. You will also be able to see the last run date and branch states on this page.

## Branch and Pull Request Filtering
Regardless of how you add or edit them, all Git repository monitors have a required Branch filter field. You can specify a specific branch name to monitor, or use `*` as a wild card character to work with all branches.   

Filter Examples:
| Filter | Description |
| --- | --- | 
| * | Matches all branches | 
| main | Exact branch match. Only matches branches named `main`. |
| release/6.* | Matches branches that start with `release\/.`, which are typically used for release branches. (examples `release/6.0`, `release/6.1`, `release/6.2`) |
| feature/* | Matches branches that start with `feature/`, which are typically used for feature branches. (examples `feature/add-active-directory`, `features/update-dotnet`, `features/kb1000-ebcac`) |

There is also a checkbox to "Only monitor branch when a pull request is open". This is primarily used for feature and hotfix branches when you only want automated builds to occur near the end of the development cycle, after opening a pull request on that branch.

## Action on Commit/Change Detection
Git repository monitors also have an Action field that determines what happens when a change (i.e. new commit or branch) is detected. These include:
- Create a build using a pipeline
- Create a build using a release
- Create a build using a script
- Execute a custom script

See the [resource monitor documentation](/docs/buildmaster/administration/buildmaster-resource-monitors) to learn more about how these actions work.

## Troubleshooting

### New commits are not triggering bulds

Behind the scenes, repository monitors check for new commits/revisions every minute. On-demand checks can be done by clicking the Refresh icon on the monitor's detail page. If you aren't seeing the latest commit/revision for a specific branch, check the following:

 - verify the BuildMaster service is running
 - attempt to trigger the `Repository Monitor` task runner on the *Manage Service* page and look for live error logs
 - verify there are no connection errors on the application's settings page

### Logs

While historical logs are not recorded for repository monitors since they run very frequently, you can visit the Diagnostic Center from the Administration page to view any errors that occurred during a recent repository monitor execution. If there are no recorded logs (or no errors generated), you may also visit the *Manage Service* page and run the `Repository Monitor` task runner directly to view any live log output.




