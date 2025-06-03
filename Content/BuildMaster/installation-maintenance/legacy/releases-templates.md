---
title: "Release Templates"
order: 7
url-slug: "buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-releases-templates"
---

:::(Error) (Release Templates are Deprecated)
With the introduction of [variable prompts](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variable-prompts) to Pipelines in BuildMaster 2022, and the planned removal of Deployables, Release Templates have become redundant, so we've deprecated them.
:::

Release templates serve to add a layer of consistency to the release creation process by assigning default properties (e.g., pipeline selection, initial included deployables ), prompting for variables at various points in the release lifecycle, and simplifying Release & Deployment API usage.

Release templates are application-specific, but can be easily copied into other applications using JSON text mode.

## Components

Release templates consist of three primary components:

1. **Pipeline** - the default pipeline selection when creating a release 
2. **Deployables** - the default deployables that are included or referenced when creating a release 
3. **Template Variable Prompts** - defines the variable prompts that occur during release creation, build creation, and deployment in a pipeline stage. Template variable configurations are the only settings in a release template that are considered "live", so changing them can change the prompts during build creation or release deployment that are currently assigned to the template.

### Template Variables and Configurations

#### Hierarchy

Release template variables are configurable at any of three levels:

1. **Release creation** - when a new share is created, inputs for the specified variables appear, and once the share is created, the corresponding [Release Variables](/docs/buildmaster/reference/functions/releases/releasevariable) are created. These variable values can be edited later on the Release Details page.
2. **Build creation** - when creating a new build of a release with an assigned template, inputs for the specified variables are displayed, and once the build is created, the corresponding _Build Variables_ will be created. These variable values can be edited later on the Build Details page.
3. **Deployment to a pipeline stage** - when deploying a build of a release with an assigned template, inputs are displayed on the Deploy to Stage page, and once the deployment starts, the corresponding _Execution Variables_ will be injected into the deployment and are not editable (but they can be overridden within a deployment plan using the [Set Variable statement](/docs/executionengine/otterscript/statements-and-blocks/other#set-variable)).

#### Types

There are four types of Template Variables:

1. **Text** - a simple string value whose prompt is presented as a text box 
2. **Checkbox** - a simple boolean value with optional description whose prompt is presented as a checkbox 
3. **List** - a collection of multiple values defined in the template variable configuration whose prompt is presented either as a dropdown list or as a tag text box if the list is not limited to the elements specified in the configuration. 
4. **Dynamic List** - a collection of values such as a list of BuildMaster servers, environments, or an external source (e.g. ProGet package IDs/versions). These values are extensible and can be added via _List Variable Sources_. The prompt is presented as a text field with auto-completed values generated from the configuration source.

#### Common Template Variable Configuration

Each of the four template variable types can be configured in different ways, but the following options are the same for all of them:

* **Initial value** - the pre-filled value that will appear in a rendered variable prompt
* **Required** - indicates that a value must be present to proceed with version/build creation or to perform deployment
* **Obscure the value of this variable from casual viewing** - indicates that a corresponding release or build variable is hidden from UI when it is created and requires additional editing permissions to view it. Note that this is not a means to "secure" a variable value, which is handled by [Resource Credentials](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials).

## Creating a Release Template

To create a release template, in the context of an application, select the Releases tab in the Releases project sub-navigation menu. Although the UI editor is the recommended method for creating release templates, it is also possible to create them from a [JSON object](#json).

## Default Release Template

As of BuildMaster 6.1.11, a release template named Default is automatically used for shares for which no template is selected. From the perspective of UI, this template also behaves slightly differently: it is not displayed as an associated template on the release overview page, and the release template selection option is hidden if there is only one template named Default.

## Using a Release Template

Once a release template is created, it can be used in one of two places: create a new release from the UI, or the [Create Release from Template](/docs/buildmaster/reference/api/release-and-build#create-release-from-template) API endpoint.

If there is only a single release template for an application, that release template is selected by default when a new release. Once a release is assigned to a release template, each of the 3 variable properties that contain template variable configurations will be prompted for values according to the [Hierarchy](#hierarchy) section above.

A release may be assigned a different release template at any time as long as the release is active. Note, however, that this may change the variable prompts whose values are expected by a deployment plan.

## JSON Format

### Release Template Object


| Object  | Description  |
| --- | --- |
| Name\* | a _string_ representing the name of the release template |
| Pipeline\* | a _string_ representing the name of the default pipeline during release creation |
| ReleaseVariables | an _array_ of [Variable](#var-obj) objects, each object represents a variable prompt that occurs during release creation |
| BuildVariables | an _array_ of [Variable](#var-obj) objects, each object represents a variable prompt that occurs during build creation |
| DeploymentVariables | an _array_ of [Variable](#var-obj) objects, each object represents a variable prompt that occurs during deployment to a specified pipeline stage (or all stages) |
| DefaultDeployableOption | a _string_ value, one of: <p>1.  **PreviousRelease:** default deployables will be identical to the highest-sequenced release <p>2.  **PreviousReleaseSameTemplate:** default deployables will be identical to the highest-sequenced release that is also associated with this release template <p>3.  **SpecificVersions:** (advanced) default deployables are not included in the release, but referenced and assigned specific release numbers (requires Deployables property values with ReferencedReleaseNumber set) |
| Deployables | an _array_ of [Deployable](#deploy-obj) objects |


### Variable Object


| Object | Description |
| --- | --- |
| Name\* | a _string_ representing the name of the variable, this value is unique per each **ReleaseVariables**, **BuildVariables** properties, and unique in **DeploymentVariables** section if all names are associated with a **PipelineStageName** property |
| Description | a _string_ describing the variable that if supplied, is displayed in the UI instead of the variable name |
| InitialValue | a _string_ representing the initial value of a variable (i.e., the text or selected list value when prompted) |
| Required | a _boolean_ indicating whether a prompted value must be supplied |
| Sensitive | a _boolean_ indicating whether a value is obscured from the UI |
| Type | a _string_ indicating the type of the variable, one of: **Text**, **List**, **DynamicList**, or **Checkbox** |
| PipelineStageNames | an _array_ of strings representing the names of the pipeline stages the variable applies to, null/empty representing all stages. This value is ignored if not under the release template's **DeploymentVariables** property |
| ListValues | an _array_ of possible List variable values. This property is ignored if the **Type** is not "List" |
| ListMultiple | a _boolean_ indicating whether selection of multiple items in a List variable is allowed |
| ListRestrict | a _boolean_ indicating whether prompted items are restricted to values in **ListValues** |
| ListSourceDescription | (automatically generated for informational purposes and not settable) |
| ListSourceXml | a persisted XML string containing the serialized List Source configuration, this value is for informational/copying purposes and is not recommended to be configured manually as text |
| ListVariableSource | (automatically generated for informational purposes and not settable) |



### Deployable Object


| Object | Description |
| --- | --- |
| Name\* | a _string_ representing the name of the deployable |
| ReferencedReleaseNumber | a _string_ representing the release number of the deployable to reference, this value is required if the **DefaultDeployableOption** property is set to **SpecificVersions** |



(\* indicates a required field)