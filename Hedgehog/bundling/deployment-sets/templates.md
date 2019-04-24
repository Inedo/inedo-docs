---
title: Deployment Set Templates
subtitle: Deployment Set Templates
sequence: 100
keywords: hedgehog, packages, deployment sets

---
Deployment set templates are designed to add a layer of consistency to the deployment set creation process by assigning default properties (e.g. pipeline selection, initially-included deployables), prompting for variables at various points during the deployment lifecycle.

Deployment set templates are stored in rafts and can be consumed by any project and are accessible by the standard [project content name](/support/documentation/hedgehog/bundling/projects/content-name).

## Components {#components data-title="Components"}

Deployment set templates consist of 3 primary components:

1. **Pipeline** - the default pipeline selection when creating a deployment set
2. **Packages** - the default package versions to be included in the deployment set
3. **Template Variable Prompts** - defines the variable prompts that occur at deployment set creation, and deployment to a pipeline stage. The configurations for template variables are the only settings in a deployment set template that are considered "live", such that changing them could change the prompts when packages are deployed for sets currently assigned to the template.

### Template Variables and Configurations {#template data-title="Template Variables and Configurations"}

#### Hierarchy {#hierarchy data-title="Hierarchy"}

Deployment set template variables are configurable at two levels

1. **Deployment set creation** - when creating a deployment set, inputs will appear for the specified variables and once the set is created, corresponding [Deployment Set Variables](/support/documentation/hedgehog/bundling/deployment-sets#variables) will be created. These variable values may be edited later on the Deployment Set Details page.
2. **Deployment to a pipeline stage** - when deploying a set with a template assigned, inputs will appear on the "Deploy to Stage" page and once deployment begins, corresponding [Runtime Variables](/support/documentation/executionengine/components/runtime-variables) will be injected into the deployment and are not editable (though they may be overridden within a deployment plan using the [Set Variable statement](/support/documentation/executionengine/otterscript/statements-and-blocks/other-statements).

#### Types {#types data-title="Types"}

1. **Text** - a simple string value whose prompt is rendered as a textbox
2. **Checkbox** - a simple boolean value with optional description whose prompt is rendered as a checkbox
3. **List** - a collection of multiple values defined in the template variable configuration whose prompt is rendered either as a dropdown list or a tag-textbox if the list is not restricted to the elements specified in the configuration
4. **Dynamic List** - a collection of values such as a list of Hedgehog servers, environments, or any external source (e.g. ProGet package IDs/versions). These values are extensible and can be added via *List Variable Sources*. The prompt is rendered as a textbox with autocomplete values generated from the configuration source.

#### Common Template Variable Configuration {#common data-title="Common Template Variable Configuration"}

Each of the 4 template variable types can be configured in different ways, but the following options are shared for all of them:

{.docs}
- **Initial value** - the pre-populated value that will appear in a rendered variable prompt
- **Required** - indicates that a value must be present to continue creating the deployment set or to deploy
- **Obscure the value of this variable from casual viewing** - indicates that when a corresponding deployment set variable is created, that it is hidden from the UI and will require additional edit permissions to view it. Note that this is not a means to "secure" a variable value which is handled through [Resource Credentials](/support/documentation/hedgehog/global-components/resource-credentials).

## Creating a Deployment Set Template {#creating data-title="Creating a Deployment Set Template"}

To create a deployment set template, select the Deployment Set Templates tab under the Deployment Sets navigation menu. While the UI editor is the recommended method to create deployment set templates, it is also possible to create them from a [JSON object](#jso).

## Using a Deployment Set Template {#using data-title="Using a Deployment Set Template"}

Once a deployment set template is created, it can be used when creating a new deployment set from the UI.

If there is only a single deployment set template accessible to a project, that template is selected by default when creating a new deployment set. Once a set is assigned a template, any of the 3 variables properties that contain template variable configurations will then prompt for values as per the [Hierarchy](#hierarchy) section above.

A deployment set may be assigned a different template at any time while the set is active, however note that this can change the deployment variable prompts whose values may be expected by a deployment plan.

## JSON Format {#JSON data-title="JSON Format"}

### Deployment Set Template Object {#object data-title="Deployment Set Template Object"}

| <!-- -->    | <!-- -->    |
| ----        | ----        |
| Name*       | a *string* representing the name of the deployment set template |
| Pipeline*   | a *string* representing the name of the default pipeline during set creation|
| DeploymentSetVariables | an *array* of Variable objects, each object represents a variable prompt that occurs during deployment set creation |
| DeploymentVariables | an *array* of Variable objects, each object represents a variable prompt that occurs during deployment to a specified pipeline stage (or all stages) |
| Packages | an *array* of Package objects, each object represents a package to attach to the set when a set is created |

### Variable Object {#variable data-title="Variable Object"}

| <!-- -->    | <!-- -->    |
| ----        | ----        |
| Name*       | a *string* representing the name of the variable, this value is unique per each **DeploymentSetVariables** property, and unique in **DeploymentVariables** section if all names are associated with a **PipelineStageName** property |
| Description | a *string* describing the variable that if supplied, is displayed in the UI instead of the variable name |
| InitialValue | a *string* representing the initial value of a variable (i.e. the text or selected list value when prompted) |
| Required    | a *boolean* indicating whether a prompted value must be supplied |
| Sensitive   | a *boolean* indicating whether a value is obscured from the UI |
| Type        | a *string* indicating the type of the variable, one of: **Text**, **List**, **DynamicList**, or **Checkbox** |
| PipeLineStageName | a *string* representing the name of the pipeline stage the variable applies to, null/empty representing all stages. This value is ignored if not under the set template's **DeploymentVariables** property |  
| ListValues       | an *array* of possible List variable values. This property is ignored if the **Type** is not “List” |
| ListMultiple    | a *boolean* indicating whether selection of multiple items in a List variable is allowed |
| ListRestrict    | a *boolean* indicating whether prompted items are restricted to values in **ListValues** |
| ListSourceDescription | (automatically generated for informational purposes and not settable) |
| ListSourceXml         | a persisted XML string containing the serialized List Source configuration, this value is for informational/copying purposes and is not recommended to be configured manually as text |
| ListVariableSource    | (automatically generated for informational purposes and not settable) |

### Package Object {#package data-title="Package Object"}

| <!-- -->    | <!-- -->    |
| ----        | ----        |
| PackageSource* | a *string* representing the name of the package source |
| Name*          | a *string* representing the name of the package |
| DefaultPackageVersion | a *string* representing which package version to include, may be one of the following values: **Latest**, **Specific**, **BestMatch**, **PreviousDeploymentSet** |
| SpecificVersion | a *string* representing the specific package version to include, required if **Specific** is selected as the **DefaultPackageVersion**

(* indicates a required field)
