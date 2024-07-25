---
title: "Programmatically Invoking OtterScript"
order: 2
---

BuildMaster 5.1.6 introduced the ability to programmatically invoke OtterScript. This can be particularly useful in cases such as:

 * Maintaining your OtterScript build and deployment plans alongside your application code (in source control)
 * Simplifying your complicated plans by building and invoking OtterScript at runtime
 * Invoking plans and modules from other applications

 These are accomplished through three operations:
 * `Invoke-Plan`, which executes a deployment plan stored within BuildMaster, in the current application, another application, or the global context
 * `Invoke-Module`, which calls a module stored within BuildMaster
 * `Invoke-OtterScript`, which invokes an arbitrary string of OtterScript

### Invoking plans and modules

The `Name` argument (`Plan_Name` or `Module_Name`) references a plan or module that is stored in BuildMaster.
 * Global plans/modules are are indicated by the `global::` prefix
 * Plans/modules from other applications use the `applicationName::` prefix
 * No prefix indicates the current application

Some examples include:
 * `SampleApplication1::DeployApp1` references the `DeployApp1` plan/module within the `SampleApplication1` application
 * `global::DeploySampleComponent` references the `DeploySampleComponent` global plan/module
 * `MyPlan1` references the `MyPlan1` in the current application
    

### Invoked OtterScript & Variables

When you execute OtterScript through invocation, the parent call stack will not inherited. Ultimately, this doesn't have too much of an impact:

 * logging will still occur at the same scope, and you won't be able to tell that it's a sandboxed invocation
 * execution temporary directories will be the same
 * the application, release, et.c contexts will be identical

The most important consideration of is that runtime variables you've defined before executing an Invoke operation will *not* be visible to the invoked script, and variables that you set within the invoked script will not be available. Here's a simple example to illustrate the behavior:

    set $MyName = David;
    
    # this will throw an error, because $MyName is not defined in the invoked script
    try 
    { 
      Invoke-OtterScript "Log-Information Hello, `$MyName;";
    }
    catch 
    { 
      warn; 
    }
    
    # This will output "Hello, Steve"
    Invoke-OtterScript >>
      set $MyName = Steve;
      Log-Information Hello, `$MyName;
    >>;
    
    # This will output "Hello, David"
    Log-Information Hello, $MyName;

You can, however, use the `AdditionalVariables` or `Arguments` to pass in variables to an invoked script, and the `CaptureOutputVariables` and  `CaptureOutputArguments` to set the value of variables from that invoked script. Here's another example:

    # This will output "Hello, Steve"
    Invoke-OtterScript
    (
      Text: >> 
        set $MyName = Steve;
        Log-Information Hello, `$MyName; 
        >>,
      CaptureOutputArguments: @(MyName)
    );
    
    # This will output "Hello, Steve"
    Log-Information Hello, $MyName;


### Programmatically invoking single operations

You can also use `Invoke-Operation` to execute a single operation:

    Invoke-Operation [Operation_Name]
    (
       [Name: <Operation_Name>],
       [Arguments: <%(key1: value1, ...)>],
       [CaptureOutputArguments]: @(var1,var2)]
    );

This operation uses a different mechanism than invoking OtterScript, and doesn't run in a sandboxed environment. It's often useful if you only need to invoke a single operation, instead of an entire plan or module.