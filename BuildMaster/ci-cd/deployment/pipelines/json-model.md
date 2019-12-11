---
title: Pipeline JSON Model
keywords: buildmaster, pipelines
sequence: 400
show-headings-in-nav: true
---

::: {.attention .analogy}
Pipelines are stored as JSON-formatted files. You can edit pipelines in the web interface using the visual or text mode.
:::

## Pipeline {#pipeline data-title="Pipeline"}
A pipeline consists of a sequence of stages, variables, and several other properties and options. The following are the contents of a pipeline's JSON file.

| Property | Description |
|---|---|
| `Color` | a *string* in the format `#RRGGBB` used to represent the color used in the UI |
| `Description` | a *string* to document the pipelines intended usage |
| `EnforceStageSequence` | a *boolean* to indicate whether the stage sequence should be enforced (requiring a Force to override), or if any stage can be deployed to at any time |
| `PostDeploymentOptions` | an *object* with the following property/value pairs. Each value is a *boolean*.<ul><li>`CancelReleases`: cancel earlier (lower-sequenced) releases that are still active and have not yet been deployed.</li><li>`CreateRelease`: creates a new release by incrementing the final part after a release has been deployed.</li><li>`DeployRelease`: mark the release and build as deployed once it reaches the final stage.</li></ul> |
| `Variables` | an *object* with property/values representing variable names and values <ul><li>a variable name is a *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable</li><li>a variable value is a *string* of any number of characters</li></ul> |
| `Stages` | an *array* of [Stage](#stage) objects |

## Stage {#stage data-title="Stage"}

A stage consists of a gate, variables, pre-deployment steps, targets, post-deployment steps, and several properties and options.

This object is used to describe the Stage property of a Pipeline object.

| Property | Description |
|---|---|
| `Name` | a *string* representing the name of the stage | 
| `Description` | a *string* to document the stage's intended usage | 
| `AutoPromote` | a *boolean* indicating whether a deployment to the next stage will automatically occur if the target is next stage's gate is met | 
| `TargetExecutionMode` | a *string* with either `Parallel` or `Sequential` | 
| `Gate` | a [Gate](#gate) object | 
| `Variables` | an *object* with property/values representing variable names and values <ul><li>a variable name is a *string* of no more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), spaces ( ), and underscores (_) and must start with a letter, and may not start or end with a dash, underscore, or space; a variable</li><li>a variable value is a *string* of any number of characters</li></ul> |
| `Targets` | an *array* of [Target](#target) objects
| `PreDeploymentSteps` | an *array* of strings containing [OtterScript strings](#otterscript-string) that are run prior to target evaluation |
| `PostDeploymentSteps` | an *array* of objects or strings, representing steps that are run after target execution<br /><br />Each object has two properties:<ul><li>`Script`: a *string* containing an [OtterScript string](#otterscript-string)</li><li>`RunOnFail`: a *boolean* indicating if the step should run even if one of the targets failed</li></ul>Using *string*s directly in this array is equivalent to an object whose `Script` is the string itself and `RunOnFail: false` |

## Gate {#gate data-title="Gate"}

A gate is a mix of manual and automated approval requirements that a build must have before being deployed to a stage, and are used to ensure the quality and acceptability of a build as it passes through the pipeline.

This object is used to describe the Gate property of a Stage object.

| Property | Description |
|---|---|
| `UserApprovals` | an *array* of objects representing people that must approve deployment into the stage; each object has two properties:<ul><li>`Name`: a string of the username of the approver</li><li>`Description`: a string of what will be displayed on the approvals required and received</li></ul> |
| `GroupApprovals` | an *array* of objects representing people in groups that must approve deployment into the stage; each object has 3 properties:<ul><li>`Name`: a *string* of the group name that an approver belongs to</li><li>`Description`: a *string* of what will be displayed on the approvals required and received</li><li>`Count`: an *integer* of how many approvers in that group must approve the build before it's met</li></ul> |
| `AutomatedChecks` | an [AutomatedCheck](#automated-check) object |
| `DeploymentWindows` | an *array* of objects representing times in which a deployment may occur<ul><li>`Days`: an *array* of strings representing the names of the days of the week (in the current culture) that a deployment StartTime occurs</li><li>`StartTime`: a *string* in a ISO 8601 24-hour time format with an optional time zone offset: `hh:mm[:ss.sss`&#177;`hh:mm]`</li><li>`EndTime`: a *string* in a ISO 8601 24-hour time format with an optional time zone offset: `hh:mm[:ss.sss`&#177;`hh:mm]`</li></ul> |

## Target {#target data-title="target"}

A target is comprised of a deployment plan, an environment name and a list of servers or roles to be deployed.

This object is expected in the Targets array property on the Stage object.

| Property | Description |
|---|---|
| `PlanName` | a *string* of the plan name that will be executed at the target |
| `EnvironmentName` | a *string* of the name of the environment that is targeted |
| `RoleNames` | an *array* of strings representing the roles that are targeted |
| `ServerNames` | an *array* of strings representing the names of the servers targeted |

## Automated Check {#automated-check data-title="Automated Check"}

An automated check object describes the automated checks that occur in a pipeline stage gate.

This object is expected in the AutomatedChecks property of the Gate object.

| Property | Description |
|---|---|
| `Issues` | an *object* that represents issue statuses required to allow promotion to this stage; each object has two properties:<ul><li>`Closed`: a *boolean* that indicates "all issues must be closed" prior to promotion, default is `false`</li><li>`Statuses`: an *array* of issue statuses (e.g. "Ready for QA") an issue must be in prior to promotion, omit the property to represent "any status"</li></ul> |
| `Tests` | a *string* of the name of the environment that is targeted |
| `Variables` | an *array* of strings representing the roles that are targeted |

## OtterScript Strings {#otterscript-string data-title="OtterScript Strings"}

OtterScript strings are JSON-encoded strings with optional additional header directives that are used by the UI to determine which pre- or post-deployment step types are displayed.

Here are some examples of valid OtterScript strings in JSON:

#### Send an email...
```
"##AH:Email\\r\\n\\r\\nSend-Email\\r\\n(\\r\\n    To: user@example.com,\\r\\n    Subject: Hello,\\r\\n    Body: Body text\\r\\n);\\r\\n",
```

#### Set stage variable...
```
"##AH:SetVariable\\r\\n\\r\\nset $TestVariable = TestValue;\\r\\n",
```

#### Execute PowerShell...
```
"##AH:PowerShell\\r\\n\\r\\nPsExec\\r\\n(\\r\\n    Text: write-host \\"Hello from PowerShell\\";\\r\\n);\\r\\n",
```

#### Execute custom OtterScript...
```
"Exec D:\\\\Tools\\\\tool.exe;"
```
