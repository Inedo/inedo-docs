---
title: "Feature Branches"
order: 1
---

Feature Branches are one of Git's most powerful features, allowing developers to independently code new features without interfering with other development. Once the new feature's code is ready, it's reviewed and then merged into the main code using a "pull request".

Integrating feature branches into your CI/CD pipeline can be challenging, and in this article we'll discuss:
 * [Creating feature branches in Git that work best with automation](#using-feature-branches-in-git)
 * [Using feature branch pipelines in BuildMaster to accommodate these branches](#creating-a-feature-branch-pipeline-in-buildmaster)
 * [Monitoring existing and new feature branches to create builds](#monitoring-git-for-new-feature-branches-and-commits)
 * [Cleaning up old builds with retention policies](#cleaning-up-feature-branch-builds-with-retention-rules)

## Using Feature Branches in Git

A "feature branch" is simply a branch that you want to use during the development of a new feature and merge with another branch when completed. For example, if you are creating a feature to allow SAML-based logins, you might do the following.

1. Create a new branch (`feature/saml-user-login`)
2. Write code and commit the changes to this branch 
3. Push the branch to your Git server 
4. Collaborate with other developers and testers to finalize the code 
5. Create a "pull request" to merge the code in `main`
6. Review the code and merge the pull request together

For many teams, build automation (continuous integration) is an important part of step 4, and the feature is often tested with a build automatically created from the feature branch.

:::(info) (💡BEST PRACTICE: Feature Branch Naming)
A feature branch is generally named something like `feature/«feature-name»`, but that's not a requirement. You may find that using multiple prefixes (such as `prototypes/6.4/«feature-name»` or `experimental/«feature-name»`) will align with your development processes better than a single prefix. In any case, create flexible standards that your entire team can understand.
:::

## Using Feature Branches in a CI/CD Pipeline
To test the in-development feature, builds are often created from the feature branch as part of the continuous integration process. However, since **feature branch builds should never be deployed to production**, they're rarely integrated into a CI/CD pipeline.

With BuildMaster, you can safely automate building and deployment of feature branches without worrying about accidental deployment to production. You can even automatically create pull requests once a feature branch build has been properly tested.

### Creating a Feature Branch Pipeline in BuildMaster
BuildMaster includes a Feature Branch Pipeline template that will help you get started. You can customize any aspect of the pipeline you need, or even create the same pipeline from scratch.

The Feature Branch Pipeline template includes three stages:

![buildmaster-git-featurebranch-pipeline](/resources/docs/buildmaster-git-featurebranch-pipeline.png){height="" width=""}

The first stage (Build) works like most build stages in other pipelines. When you create a new build, you select the feature branch you want, and the pipeline runs your [build script](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts). 

![buildmaster-git-featurebranch-build](/resources/docs/buildmaster-git-featurebranch-build.png){height="" width="50%"}

The next stage (Integration) works a little differently: it will [prompt the user](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variable-prompts) to select a target server for deployment:

![buildmaster-git-featurebranch-deployint](/resources/docs/buildmaster-git-featurebranch-deployint.png){height="" width="50%"}

The final stage (Pull Request) will run a `Create-Pull-Request` script. If your application doesn't already have that script, the following default script will be created:

```
Git::Create-PullRequest
(
    Target: master,
    Source: $Branch,
    Title: $PullRequestTitle
);
```

The `$Branch` variable refers to the branch of the current build, and the user will be prompted for `$PullRequestTitle`:

![buildmaster-git-featurebranch-deployprod](/resources/docs/buildmaster-git-featurebranch-deployprod.png){height="" width="50%"}

After the build is deployed to the final stage, a [pipeline event listener](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines/buildmaster-pipeline-event-listeners) will sets the build status to Rejected, so that it's not accidentally deployed later.

     
## Monitoring Git for New Feature Branches and Commits
You can configure a [Git Repository Monitor](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control/buildmaster-git-repository-monitors) to automatically create new builds when a new feature branch is created or a commit is detected on a feature branch.

To do this, navigate to Settings > Resource Monitors in your application, then click Add Monitor. After selecting Git Repository Monitor from the list, you can specify a branch filter that uses a wildcard expression such as `feature/*`:

![buildmaster-git-featurebranch-repomon](/resources/docs/buildmaster-git-featurebranch-repomon.png){height="" width="50%"}

The expression `feature/*` will create a build when a new branch that starts with `feature/` is detected, or if there are any new commits to such a branch.

If you need to monitor multiple feature branch patterns, just create additional repository monitors.

## Cleaning up Feature Branch Builds with Retention Rules
:::(Internal) (Releaseless Builds May Not Work)
This hasn't been tested, and retention policies are 💩.
:::

Builds created from feature branches can be purged shortly after they've been rejected. 

You can automate purging these builds using a [retention policy](/docs/buildmaster/administration/retention-policies) that's configured like this:

| Retention Policy Field | Recommended Value |
| --- | --- |
| Purge item type: | Build |
| Application: | «your-application» |
| Pipeline: | «feature-pipeline» |
| Purge options: |  `[ ]` Only purge builds in deployed releases<br/>`[X]` Only purge rejected builds |
| Keep builds for: | 14 Days |
| Retention options: | `[X]` This retention policy is active |

:::(Internal) (Old Content That Might Be Salvable)
Branches are one of the most powerful features offered by most source control systems. Although they take some getting used to, they can ultimately save a lot of development resources and allow teams to deliver better and faster software versions because:

- **Releases can be developed in parallel.** Even though two-week sprints are becoming the norm, it doesn't mean that everyone needs to be sprinting all the time and focusing only on the next release. Release branches allow teams to think about and work on releases that are weeks or months away without stressing about the present
- **Features can be independently developed and tested**. By using feature branches to develop and test changes to an application, developers can be certain that code changes from *other* features won't interfere with their work, and that their work won't interfere with others
- **Experiment with innovative changes.** Feature branches also enable business stakeholders to sponsor proofs of concept without worrying about experimental code being released into production

Despite these benefits, many development teams avoid using branches to their full potential due to the complexity of setting up and maintaining automated builds and deployments (CI/CD). Some of the commonly cited concerns are:

- **Continuous Integration servers are not designed with branching in mind**. Tools like Jenkins and TeamCity have only recently introduced branching support as a feature, and many have reported that it feels like an afterthought. It's complex to use, and you often have to design a process around what the tool supports
- **Deploying feature branches is very risky**. Introducing builds that were created from feature branches into your software delivery pipeline means that it could not only interrupt testing for planned releases but also that it might make its way to production
- **Testing feature branches is very difficult.** When different teams are working on dozens of different features, it's nearly impossible to keep track of which build was deployed to which environment, and by the time testing teams find that out, a different build is often deployed on top of it
- **Managing parallel releases is complicated.** Software delivery pipelines are designed for a repeatable process, but many tools are not flexible enough to deal with hotfixes or parallel releases

BuildMaster simplifies this complexity and makes it safe to introduce branches, thus easily enabling the true benefits of branching.
:::