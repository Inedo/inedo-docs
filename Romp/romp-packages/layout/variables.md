---
title: Variables
subtitle: Variables
sequence: 30
keywords: romp, variables
---

A raft is used to store plans, templates, variables, and asset files. If a Romp Package has any dependency on raft data, the entire raft can be included in the package. Further information about rafts can be found in the [Otter documentation](/support/documentation/otter/global-components/rafts).

Package variables typically contain configuration values that are required for installing a package in a specific environment, or to allow installation to be customized.

Variables are defined in a `packageVariables.json` file located in the package root with `upack.json`. It is simply an object with variable names and values as properties. The value can be either a string, null, or an object with the following optional properties:

| <!-- --> | <!-- -->|
| ---      |  ---       |
| `value`  | a *string* of the variable’s value itself |
| `description` | a *string* to document the variable's intended usage |
| `required` |  a *boolean* to indicate that a value is required; when a required variable does not have value (i.e. not null or empty), the installation will not proceed; defaults to false if not specified |
| `sensitive` | a *boolean* to indicate that the value should not be displayed in logs; default is false <br/><br/> *Note: an installation script may still Log the value of the variable to the console* |
| `type` | a *string* of the variable's type; `text` (default), `boolean` (indicates only `true` or `false` string literals are allowed, `list`, or `map`

When an object is not specified for a variable value, a string indicates a required text variable, while a null represents a required text variable without a value. If you wish to allow an empty string, you will need to use the object syntax.

### Example: packageVariables.json file {#example data-title="Example: packageVariables.json file"}

```
{
  "myStringValue": "Steve Dennis",
  "myArray": [1, 2, 3, 4, 5],
  "myMapValue": {
    "p1": "hdars",
    "p2": "test"
  },
  "requiredButNotSpecifiedValue": null
}
```

The example above defines 3 variables with values. Each property on the JSON object corresponds to a runtime OtterScript variable of `scalar`, `vector`, and `map` types respectively. The final variable, `requiredButNotSpecifiedValue`, is required but not specified. This will cause Romp to prompt for this value when the package is installed.

Package variables specified as command line arguments are returned first, followed by variables in packageVariables.json, followed by raft variables, in the event of variables of the same name.

**You should not store sensitive passwords or other secrets in variables. They are not secure, at all. Instead, consider [resource credentials](/support/documentation/romp/romp-packages/layout/credentials).** {.announcement}

## Prompting for Required Variables {#prompting-required data-title="Prompting for Required Variables"}

If a variable is required for package installation, it may be passed in as an argument to Romp. If you don't pass in a required variable, then you will be prompted for it (when running interactively), or Romp will issue an error and not proceed with installation.

## Best Practices: Variable Descriptions {#best-practices data-title="Best Practices: Variable Descriptions"}

Think of variable descriptions like comments. If it's not obvious from the name how to use a variable, or how it might work, you should briefly describe that in the field. You can always include additional instructions on how to use the package in the package's description metadata field.

Variable descriptions are primarily used by `{cmd line}` to show a user how to install a package, and should be brief.
