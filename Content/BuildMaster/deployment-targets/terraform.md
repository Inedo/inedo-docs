---
title: "Terraform"
order: 4
---

Terraform is a free and open source platform primarily used for building, changing, and managing cloud-based infrastructures. Environments are defined as code in a configuration language called HCL (HashiCorp Configuration Language) and then deployed using the `terraform` command-line tool.

While Terraform can significantly reduce the time required to work with cloud-based infrastructure, it also carries significant risk. A simple typo in your "infrastructure as code" could permanently destroy your production environment by deprovisioning the wrong resources, deleting the wrong databases, etc.

BuildMaster can effectively eliminate this risk with a change management and deployment pipeline that automates the validation, testing and execution of your Terraform configuration. In addition, this:

* **Eliminates the need to give engineers direct access to infrastructure**; instead, they can create configurations on their local work stations, test against their own sandboxes, and commit their changes to source control
* **Segregates testing and production environments**; use completely different credentials to different cloud-provider accounts so that tested configuration changes don't impact live environments
* **Limits access to variables and configuration**; restrict access to secrets, even to those who can deploy to testing or production
* **Enables self-service testing and deploying of infrastructure changes**; allow engineers to test their own changes, QA to validate those changes, and operations to verify and deploy those changes—all in separate environments

BuildMaster provides a lot of flexibility in creating pipelines for Terraform, and has a built-in Terraform CI/CD template to help you get started.

## Configuration in Source Control

The first step is creating a delivery pipeline for your Terraform configurations will be putting your HCL code in source control. BuildMaster integrates with a variety of [source control systems](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control/buildmaster-ci-cd-continuous-integration-server-source-control), but we'll use GitHub for our [sample repository](https://github.com/Inedo/terraform-sample) and examples in this document.

### Example: Getting Configuration from GitHub

After creating a GitHub Project [secure resource](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials), you can get the source code as follows.

```
GitHub::Get-Source
(
    From: InfraConfigRepo,
    CommitHash => $CommitId,
);
Set-BuildVariable CommitId
(  
    Value: $CommitId  
);
```

This will also create a build-scoped variable to record the commit used.

### One Configuration vs Multiple Configurations
Some engineers will prefer to use one repository for all Terraform configurations, while others will prefer separate repositories.  

BuildMaster has the flexibility to handle both preferences; if you wish to use a subdirectory, you can use a General Block that specifies a subdirectory, as follows.

```
with configs\my-app
{
    << validate and capture artifacts >>
}
```

## Creating Verified Artifacts 

A [build artifact](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) is essentially a .zip file that is captured in the first stage of a pipeline and then deployed in each subsequent stage. They ensure that the exact same code is deployed in each environment.

Terraform code is not compiled, but used to generate an environment-specific configuration plan (the `plan` command), which is then applied to a specific environment (the `apply` command).

Since `plan` applies environment-specific variables, but also contains sensitive information such as secrets, it is not useful to capture a configuration plan as an artifact and deploy it to every environment. Instead, this should be done in each environment before the configuration is applied.

However, you can use the `validate` command to make sure that your Terraform code is syntactically valid. There are also additional tools, such as TFLint, that can check your configuration long before you run it.

This validated code should then be captured as an artifact.

### Example: Validating and Capturing an Artifact

This uses the `ExecTerraform` module that's included in the Terraform CI/CD template. You can customize this module as needed.

```
call ExecTerraform
(
    Command: validate,
    Arguments: -backend=false
);

Create-Artifact InfraConfig;
```

## Inputs and Prompts (Variables)

When Terraform is run interactively (i.e., manually on the command line), it prompts the user for input. This usually involves environment-specific values (such as DNS or host names) or sensitive secrets (such as a password or API key).

While you could use a [Release Template](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-releases-templates) to prompt for input at various points in the delivery process, it's simpler and more secure to use a [configuration file asset](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) to store these values. 

The easiest way to do this is by creating a `env-config.tfvars` configuration file asset with one instance per environment you intend to deploy to.

### Example: Using a Configuration File Asset

To use the configuration file asset, deploy it to the working directory immediately after the artifact.

```
Deploy-Artifact InfraConfig;

Deploy-ConfigFile
(
    ConfigFileName: env-config.tfvars,
    Instance: $EnvironmentName
);
````
This will allow you to run `plan` and `apply` against environment-specific configuration.

## Applying Verified Configuration

After deploying the artifact and environment-specific configuration file instance to the working directory, you can directly run the `apply` command. This will generate the environment-specific plan and apply it. 

```
call ExecTerraform
(
    Command: init
);

call ExecTerraform
(
    Command: apply,
    Arguments: -var-file env-config.tfvars
);
```

Note that this also uses the `ExecTerraform` module that's included in the Terraform CI/CD template. 

## Manually Verifying Generated Plans 

Since a generated configuration plan (i.e., the output of `plan`) may contain sensitive information such as secrets, it can be challenging to securely verify the generated plan before it's executed. Fortunately, with multiple environments and relatively simple prompts/configurations, this type of verification isn't really necessary.

However, if you want to be extra safe and not worry about accidentally exposing the generated plan, one option is to temporarily release the generated plan as a build artifact during deployment, manually review it, and then delete it.

This can be tracked with the `Perform-ManualOperation`, which will halt deployment until the specified user certifies that they performed the specified task. For example:

```
call ExecTerraform
(
    Command: plan,
    Arguments: -var-file env-config.tfvars
);

Create-Artifact InfraConfigPlan;

Perform-ManualOperation
(
    Name: Verify InfraConfigPlan & Delete Artifact;
);

call ExecTerraform
(
    Command: apply
);
```

## Tagging Applied Configuration

At the last stage of your pipeline, it's a good idea to apply a tag to the exact code that was applied. This can be done using the commit captured in the first stage.

```
GitHub::Tag
(
    From: InfraConfigRepo,
    Tag: $ReleaseNumber.$BuildNumber,
    Branch: $Branch,
    CommitHash: $CommitId
);
```