---
title: "How To: Set Up Manual Approval in BuildMaster"
order: 5
hidden: true
---

When working with a deployment [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines), adding manual approval to stages in the pipeline allows for greater reliability by involving human review and authorization.

:::(Info) (⚙️ Manual Approval in Action: GlobalFinance)
*This article uses a scenario to help illustrate the feature.*

We are GlobalFinance, a financial organization, deploying a fix for a critical security vulnerability found in our software. A Smoke Test performed by the Chief Information Security Officer (CISO) will be required, so manual approval will be added at the testing stage to confirm that this has been performed before deployment progresses.
:::

## Step 1: Add Manual Approval

We'll start by navigating to our "Pipelines" page by selecting "Settings" > "Pipelines", and selecting "edit".

![Pipeline Edit](/resources/docs/buildmaster-pipeline-edit-3.png){height="" width="50%"}

From here, we'll navigate to the "Testing Stage" of the pipeline, and click "add" on the "Stage Requirements" section.

![Testing Stage](/resources/docs/buildmaster-pipeline-testing-stage-add.png){height="" width="50%"}

Next, we'll select "Add Manual Approval".

![Add Manual Approval](/resources/docs/buildmaster-select-stage-requirement-check-manual-approval.png){height="" width="50%"}

We'll select John Smith, GlobalFinance's CISO, as the user required to give manual approval, along with a description stating the Smoke Test that they will be required to perform.

![Add Manual Approval](/resources/docs/buildmaster-create-user-approval.png){height="" width="50%"}

We'll select "Save", and return to the pipeline where manual approval has been added.

![Manual Approval Added](/resources/docs/buildmaster-pipeline-testing-stage-approval-added.png){height="" width="50%"}

Finally we'll commit our changes at the top of the pipeline page by selecting "Commit Changes".

![Commit Changes](/resources/docs/buildmaster-pipeline-commit-changes.png){height="" width="50%"}

Optionally, we also have the choice of adding a whole group of users to give approval in the case that there are multiple individuals given the authority to do so, such as members of GlobalFinance's senior security team.

![Multiple Users](/resources/docs/buildmaster-create-user-approval-alternative.png){height="" width="50%"}

## Step 2: Observe Manual Approval in Action

Now we'll return to our pipeline and see that it has reached the "Testing Stage", but has now halted due to the manual approval required to progress, as indicated.

The list of required approvals appears on the right, but note that only John Smith will be permitted to interact with it.

![Pipeline Testing Stage](/resources/docs/buildmaster-build-overview-testing-stop.png){height="" width="50%"}

John Smith will then manually approve the testing stage after performing the necessary smoke test.

![Manual Approval done](/resources/docs/buildmaster-build-overview-testing-approved.png){height="" width="50%"}

We'll select the "Testing Stage", and see a log of the manual approval performed, showing that John Smith manually approved the stage at the time indicated.

![Testing Log](/resources/docs/buildmaster-deploy-build-testing-1.png){height="" width="50%"}

The pipeline can now progress on to the "Production Stage".

![Production Stage](/resources/docs/buildmaster-build-overview-production-stage.png){height="" width="50%"}

## Optional: Manual Approval an an Alternative User

In a similar scenario where the CSIO is unavailable to manually approve the "Testing Stage", authorization has been given to the system administrator to deploy the "Testing Stage" in his stead.

In this case, they'll force deployment by selecting the "Testing Stage", and then "force".

![Edit Deploy](/resources/docs/buildmaster-deploy-build-force.png){height="" width="50%"}

They'll then select "Force Promotion" to deploy immediately.

![Production Stage](/resources/docs/buildmaster-force-build-promotion-2.png){height="" width="50%"}

As demonstrated, manual approval is beneficial as it enables quality assurance by allowing human review of changes before deployment, ensuring they meet desired standards, mitigates risks and preventing unauthorized changes.

## Alternative Scenarios

While this article covered one possible scenario, there are many others in which manual approval would be effective, for example:

- The lead UX designer of an e-commerce website approving style changes.
- The quality assurance manager of a healthcare facility approving the installation of new client record software.
- A localization team lead of a software company approving the of release foreign language material after proofreading.
