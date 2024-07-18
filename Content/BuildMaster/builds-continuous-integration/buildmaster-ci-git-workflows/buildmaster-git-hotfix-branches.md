---
title: "Hotfix Branches"
order: 3
---

Hotfix branches allow developers to quickly fix critical issues in a production environment by working with the exact code deployed in production. Once the code is fixed, it can be quickly tested and deployed, and later merged into the main code. Like [feature branches](/docs/buildmaster/builds-continuous-integration/buildmaster-ci-git-workflows/buildmaster-git-feature-branches), hotfix branches prevent interfering with ongoing development. 

There are a few options for integrating hotfix branches into your CI/CD pipeline. In this article, we'll discuss:
 * [Creating hotfix branches in Git that work best with automation](#using-hotfix-branches-in-git)
 * [Using hotfix branch pipelines in BuildMaster to accommodate these branches](#creating-a-hotfix-branch-pipeline-in-buildmaster)
 * [Monitoring existing and new feature branches to create builds](#monitoring-git-for-new-hotfix-branches-and-commits)

## Using Hotfix Branches in Git

A hotfix branch should be created from the commit that's currently deployed to production. Generally, this commit will be tagged with a version number to make it easier to find.

For example, if you needed to fix SAML-based logins, you might do the following:

1. Identify the production tag or commit (e.g. `4.2.1` or `f839162`)
2. Create a new branch from that point (`hotfix/4.2.1-saml-login-fix`)
3. Write code and commit the changes to this branch 
4. Push the branch to your Git server 
5. Collaborate with other developers and testers to verify the fix
6. Deploy the hotfix code to production
7. Tag the commit with the new version number (e.g. `4.2.2`)
8. Optionally, merge into a main branch
9. Delete the hotfix branch

In some cases, merging the code back into a `main` branch will be unnecessary. The issue may have already been addressed in the in-development release, or a new feature would have made the fix impractical to merge.


## Using Hotfix Branches in a CI/CD Pipeline

To test hotfix changes, production-ready builds should be created in hotfix branches. These **hotfix branch builds should be deployed to production**. Unlike builds created from the main or development branch, they are guaranteed to have minimal changes from production code.

### Creating a Hotfix Branch Pipeline in BuildMaster
BuildMaster includes a Hotfix Branch Pipeline template to help you get started. You can customize any aspect of the pipeline you need, or even create the same pipeline from scratch.

The Hotfix Branch Pipeline template includes the four default stages:

![buildmaster-git-hotfix-pipeline](/resources/docs/buildmaster-git-hotfix-pipeline.png){height="" width=""}

The stages work mostly like they would in other pipelines: the Build stage will run a `Build` script, and the Integration, Testing, and Production stages run a `Deploy` script.

:::(info) (💡BEST PRACTICE: Hotfix Branch Pipelines)
Builds created from hotfix branches should follow the same build and deployment process as other builds, often using the same scripts. For some teams, a dedicated hotfix pipeline is not required: you can simply create builds from your regular pipeline and override/force settings that would otherwise prevent deployment from a non-main branch.
:::


Unlike most pipelines, in this template the Deployment Eligibility option is set to *not* enforce pipeline the stage order for deployments. This setting means that you can deploy to any stage without having successfully deployed to a previous stage, which is often desirable for hotfixes. For example, a build that uses this pipeline could skip Integration, and go from Build to Testing to Production. 

![buildmaster-git-hotfix-pipeline-progress](/resources/docs/buildmaster-git-hotfix-pipeline-progress.png){height="" width=""}

To help mitigate an accidental deployment, deploying into Production requires an approval.

![buildmaster-git-hotfix-deployprod](/resources/docs/buildmaster-git-hotfixbranch-deployprod.png){height="" width="50%"}

After a build is deployed to Production, you must manually set it as deployed.

## Monitoring Git for New Hotfix Branches and Commits
You can configure a [Git Repository Monitor](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control/buildmaster-git-repository-monitors) to automatically create new builds when a new hotfix branch is created or a commit is detected on a hotfix branch.

To do this, navigate to Settings > Resource Monitors in your application and click Add Monitor. After selecting Git Repository Monitor from the list, you can specify a branch filter that uses a wildcard expression such as hotfix/*`:

![buildmaster-git-hotfixbranch-repomon](/resources/docs/buildmaster-git-hotfixbranch-repomon.png){height="" width="50%"}

The expression `hotfix/*` will create a build when a new branch that starts with hotfix/` is detected, or if there are any new commits to such a branch.

If you need to monitor multiple branch patterns, just create additional repository monitors.
