---
title: The Pipeline JSON Model
subtitle: The Pipeline JSON Model
sequence: 100
keywords: buildmaster, pipelines
show-headings-in-nav: true

---

:::attention{.analogy}
![](/resources/images/icons/analogy.png) Pipelines are stored as JSON-formatted files in rafts. You can edit pipelines in the web interface using the visual or text mode, or maybe edited externally if a raft supports it (i.e. on disk, on GitHub, etc.)

:::

### Pipelines {#pipeline data-title="Pipeline"}

| <!-- -->  | <!-- -->  |
| ---       | ---    |
| `Color`   | a *string* in the format `#RRGGBB` used to represent the color used in the UI |
| `Description` | a *string* to document the pipelines intended usage |
| `EnforceStageSequence` | a *boolean* to indicate whether the stage sequence should be enforced (requiring a Force to override), or if any stage can be deployed to at any time |
| `PostDeploymentOptions` | an *object* with the following property/value pairs. Each value is a *boolean*. <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226;`CancelReleases`: cancel earlier (lower-sequenced) releases that are still active and have not yet been deployed. <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `CreateRelease`: creates a new release by incrementing the final part after a release has been deployed. <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `DeployRelease`: mark the release and package as deployed once it reaches the final stage. |
| `Variables` | an *object* with property/values representing variable names and values <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; a variable name is a string of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; a variable value is a string of any number of characters
| `Stages` | an *array* of [Stage](#stage) objects |

### Stage {#stage deployment-title="Stage"}
A stage consists of a gate, variables, pre-deployment steps, targets, post-deployment steps, and several properties and options.

This object is used to describe the Stage property of a Pipeline object.

| <!-- -->  | <!-- -->  |
| ---       | ---    |
| `Name`    | a *string* representing the name of the stage |
| `Description` | a *string* to document the stage's intended usage |
| `AutoPromote` | a *boolean* indicating whether a deployment to the next stage will automatically occur if the target is next stage's gate is met<!--- and ... --> |
| `TargetExecutionMode` | a *string* with either `Parallel` or `Sequential` |
| `Gate`        | a [Gate](#gate) object |
| `Variables`   | an *object* with property/values representing variable names and values <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; a variable name is a *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; a variable value is a *string* of any number of characters |
| `PreDeploymentSteps` | an *array* of strings containing [OtterScript strings](#otterscript-string) that are run prior to target evaluation
| `Targets` | an *array* of [Target](#target) objects |
| `PostDeploymentSteps` | an *array* of objects or strings, representing steps that are run after target execution <br /> <br /> Each object has two properties:<br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `Script`: a *string* containing an [OtterScript string](#otterscript-string) <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `RunOnFail`: a *boolean* indicating if the step should run even if one of the targets failed <br /> <br /> a *string* in this array is an OtterScript that is run only when all targets succeeded |

### Gate {#gate data-title="Gate"}

A gate is a mix of manual and automated approval requirements that a deployment set must have before being deployed to a stage, and are used to ensure the quality and acceptability of a deployment set as it passes through the pipeline.

This object is used to describe the Gate property of a Stage object.

| <!-- -->  | <!-- -->  |
| ---       | ---    |
| `UserApprovals` | an *array* of objects representing people that must approve deployment into the stage; each object has two properties: <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `Name`: a *string* of the username of the approver <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `Description`: a *string* of what will be displayed on the approvals required and received |
| `GroupApprovals` | an *array* of objects representing people in groups that must approve deployment into the stage; each object has two properties: <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `Name`: a *string* of the group name that an approver belongs to  <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `Description`: a *string* of what will be displayed on the approvals required and received <br /> <br /> &nbsp;&nbsp;&nbsp;&#8226; `Count`: an *integer* of how many approvers in that group must approve the deployment set before it's met|

### Target {#target data-title="Target"}

A target is comprised of a deployment plan, an environment name, a list of servers or roles, and a list of packages to be deployed. All these fields support *variable expressions*, which you can even set in the pre-deployment steps.

This object is expected in the Targets array property on the Stage object.

| <!-- -->  | <!-- -->  |
| ---       | ---    |
| `UserApprovals` | a *string* of the plan name that will be executed at the target, or null to use the plan embedded in the packages |
|`EnvironmentName` | a *string* of the name of the environment that is targeted |
| `RoleNames` | an *array* of *strings* representing the roles that are targeted |
| `ServerNames` | an *array* of *strings* representing the names of the servers targeted |
| `PackageNames` | an *array* of *strings* representing the names of packages to iterate over <br /><br /> null indicates all packages <br /><br /> an empty array indicates no packages

#### OtterScript Strings {#otterscript data-title="OtterScript Strings"}

OtterScript strings are JSON-encoded strings with optional additional header directives that are used by the UI to determine which pre- or post-deployment step types are displayed.

Here are some examples of valid OtterScript strings in JSON:

```
"##AH:PowerShell\r\n\r\nPsExec\r\n(\r\n    Text: write-host \"Hello from PowerShell\";\r\n);\r\n",
```

```
"##AH:Email\r\n\r\nSend-Email\r\n(\r\n    To: user@example.com,\r\n    Subject: Hello,\r\n    Body: Body text\r\n);\r\n",
```

```
"##AH:SetVariable\r\n\r\nset $TestVariable = TestValue;\r\n",
```

```
"Exec D:\\Tools\\tool.exe;"
```
