---
title: "Formal Specification"
order: 1
---

This document describes the runtime behavior of OtterScript (used by Otter and BuildMaster) and is intended primarily for reference. It is not intended to be helpful for learning how to create scripts, although it may explain why a block behaves in a certain way and why the text mode of OtterScript looks slightly different than the visual version.

## General Notes

### Script Editor & Abstraction

To make Infrastructure as Code as accessible as possible, the OtterScript Editor and associated documentation abstract away some of the implementation details:

* **General Block** - this is a combination of anonymous block statements, context statements, and execution statements
* **Loop Block** - this is either a context iteration statement or an iteration block statement


## OtterScript Executer

The OtterScript Executer evaluates the instructions in a script in the order in which they appear. If an error occurs, the execution status changes to `failing` and execution is halted; this causes execution to abort unless the error occurred within a Try Statement

### Execution Properties

An execution has the following properties.

*   Run State - indicates the current running state of the execution: Pending, Executing, Completed
*   Status - status to report to the user: Normal, Warning, Error
*   Execution Mode - Collect Only, Collect and Execute, Execute Only
*   Simulation - true or false

#### Execution Modes

The execution engine can run a script in three different modes:

*   **Collect Only**\- only [ICollectingOperation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/ICollectingOperation) operations will run; if the operation is a [IComparingOperation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/IComparingOperation), then drift may be indicated. All ensure operations implement both interfaces.
*   **Collect then Execute** - a collection pass is performed as described above; if any drift is indicated, an execution pass is performed that runs:
    *   operations that indicated drift
    *   [IExecutingOperation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/IExecutingOperation) operations in the same scope as a drift-indicating operation that do not implement [IComparingOperation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/IComparingOperation); this is all execution actions
    *   operations with an execution policy of AlwaysExecute; this can only be set on a Context Setting Statement
*   **Execute Only**\- only [IExecutingOperation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/IExecutingOperation) operations will run; all ensure and execute operations implement this interface

<span style="color:orange">BuildMaster scripts always execute in *Execute Only* mode.</span>


## Configuration and Orchestration Plans

Executions come from three sources. In each case, the user's script is wrapped in different statements.


| Type | TaskRunner | Mode |
| --- | --- | --- |
| Orchestration Job | JobDispatcher | Execute Only |
| Routine Configuration Check *(Otter only)* | Routine Configuration | CollectOnly or CollectAndExecute |
|Configuration Job *(Otter only)* | JobDispatcher | CollectAndExecute |

An Orchestration Job may or may not target servers. If there are target servers, the above logic is performed. If no servers are specified, the script is executed directly.

In Otter, a routine configuration check will create a distinct execution per server. Each of these executions will be a script comprised of a single [Set Context Statement](#set-context-statement) (with a ContextType of server, and ContextValue of the server name); the body of this statement consists of the individual configuration scripts of the role (wrapped in a Set Context Statement for "role" and the role name), followed by the configuration script of the server (wrapped in an Anonymous Block Statement ).

In Otter, a configuration job targets servers directly or indirectly (role and/environment). A list of servers will be gathered, and a script comprising of a single [Context Iteration Statement](#context-iteration-statement) will be created with the Source set to a literal expression of the server names (e.g., `@(Server1, Server2, Server3)`). The Body contains an [Execution Directive Statement](#execution-directive-statement) with an Asynchronous flag, and the Body of that will contain the actual script.

## Script Elements
A script is essentially a Scoped Statement Block with Additional Headers.

### Additional Headers

These are a set of strings that may contain metadata for the script. They are special comments that are not currently used by the execution engine, but are used by the script editor to determine whether the script should be displayed in visual or text mode, etc.

### Scoped Statement Block

This is a grouping of statements and named modules; it is not a statement itself, but an element of several "block" statements. Both runtime variables and modules declared in a statement block with scope are accessible to all nested scopes, and a script is simply a statement block with scope.

### Modules

A `module` is like a method or subroutine. They may be declared within a Scoped Statement Block, in which case they are accessible only within the block and nested blocks.

A module is comprised of:

* Name - a valid name
* Body - a statement block with scope
* Parameters - a dictionary of Template Arguments

A module is comprised of a Name, Output Indicator, and a Default Value.

## Script Statements
There are many differing statement types; the only common element between statements is a description (see [comments and descriptions](/docs/executionengine/otterscript/comments-and-descriptions)).

#### Action Statement

This invokes an [operation](/docs/executionengine/overview/operations-functions), and is comprised of:

*   Action Name – a qualified name of the operation's namespace and script alias
*   Arguments – a string-keyed dictionary of string values
*   Positional Arguments – a list of strings; operations currently support only a single positional argument (a property with a [DefaultPropertyAttribute](https://msdn.microsoft.com/en-us/library/system.componentmodel.defaultpropertyattribute(v=vs.110).aspx))
*   Output Arguments – a string-keyed dictionary of runtime variable names

When this statement is encountered, the runtime does the following:

1.  Attempt to load the Operation with from the [ScriptAlias](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility/ScriptAliasAttribute) and [ScriptNamespace](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility/ScriptNamespaceAttribute); an error is raised if cannot be found
2.  A log scope is created using the Operation's description
3.  The Arguments and Positional Arguments are mapped to either the Operation's template properties (for [EnsureOperation<TConfig>](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/EnsureOperation(TConfig)) operations) or the Operation's properties using the property's ScriptAlias.
4.  Unless the property has a [DisableVariableExpansionAttribute](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility/DisableVariableExpansionAttribute), the values are evaluated
5.  The values are assigned to the mapped property.
<span style="background-color: #9263AB">*(Otter only)*</span> During a collection run, if the Operation type is: <ol><li>Collecting: the Collect method is invoked</li><li>Ensure: the Collect, Store, then Compare methods are invoked</li><li>Intrinsic: the Execute method is invoked</li></ol> 
      <span style="background-color: #9263AB">*(Otter only)*</span>    During an execution run, if the Operation type is: <ol><li>Executing or Intrinsic: the Execute method is invoked</li><li>Ensure, and the execution type is Execute Only: then the Execute method is invoked<li>Ensure, and the execution type is Collect and Execute: if the Compare method reported differences, then the Execute method is invoked</li></ol>
    
   
6.  In BuildMaster, every script is effectively an orchestration job, so all operations are invoked.
7.  If an exception occurred within one of the above methods or a log message was written with an error level, then the execution state will change to Failing and an error will be Raised
8.  If a log message was written with a warning level, then the execution state will change to Warning unless it's already in a Failing state
9.  The Output Arguments are mapped from the Operation or Configuration using the Script Alias, and the specified runtime variables are created or assigned
10.  The log scope is closed

#### Anonymous Block Statement

This consists of a single Scoped Statement Block. When this instruction is encountered, the execution engine performs the following steps:

1. If the statement has a description, creates a log section with the first line of this description
2. Executes the Scoped Statement Block
3. Ends the log section if it was created

#### Assign Variable Statement

This consists of a Variable Name and a Variable Value. When this instruction is encountered, the execution engine performs the following steps:

1. Searches for a runtime variable created in the current or parent scope
2. If no runtime variable was found, a new runtime variable is created for the current (and nested) scope
3. The Variable Value is evaluated; if the expression type does not match the variable type (Scalar, Vector, Map), an error is issued; otherwise, the value is assigned to the found or created runtime variable

If a configuration variable already exists (e.g. a server or environment variable), this statement will still create a runtime variable that overwrites the configuration variable in the current and nested scopes.

Note that, unless a variable is in the global scope (i.e., declared with a [Declare Global Variable Statement](#declare-global-variable-statement)), asynchronously-running blocks create a copy of the parent scope's runtime variables, so assigning a runtime variable in an asynchronous block will _not_ update the parent, non-asynchronous scope.

#### Assign Variable Statement
This consists of a variable name, value, and optional scope modifier. When this instruction is encountered, the following steps are performed:
1. Searches for the specified variable. When the `local` scope modifier is specified, only the current scope is searched
2. If a match is found, the variable's value is set. If a match is not found, a new variable is created in the current scope
    
#### Await Statement

This consists of an optional Token name. When this instruction is encountered, the execution engine performs the following steps:

1. Searches for all child execution engines created from an Execution Directive Statement
2. If a token is specified, engines without the corresponding token are ignored
3. If no matches are found, a warning message is logged
4. Execution is suspended until the child engines are completed
5. If the execution status of the child execution engine is "failure/warning", the parent execution engine inherits this status. If the status is "failed" and the await statement is inside a TryStatement statement, its catch handler is executed.

#### Break Statement
This has no elements; if this statment is found, the execution engine past the enclosing Iteration or Context Iteration statement. If there is no enclosing iteration block, a warning is written to the log and no other action is performed.

#### Call Module Statement

This invokes an OtterScript Module, and is comprised of:

*   Module Name – a qualified name of the module's raft container and declared name
*   Arguments –  a string-keyed dictionary of value strings

When this statement is encountered, the execution engine does the following:

1. If no raft name is given, a module is searched for in the current scope and then in all parent scopes
2. If no module is found, a module file is searched for in the specified raft container or in the current raft
3. If no module was found, an error is output
4. The arguments of the found module are then enumerated 1. If no suitable argument was included in the statement and the module argument has no default value, an error is raised 2. If no suitable argument was contained in the statement, but the module argument has a standard value, the argument is created as a runtime variable in the current validity area 3. If a suitable argument was found, the value is evaluated and created as a runtime variable in the current scope
5. If the statement has a description, a log area is created with the first line of that description
6. Executes the main part of the module
7. Closes the log scope if it was created


#### Context Iteration Statement

This enumerates a vector expression, changes the context each time, and executes the contained statements. It's comprised of:

*   Context Type – either directory, role, or server
*   Source – a valid vector expression
*   Body –  a scoped statement block

When this statement is encountered, the execution engine does the following:

1.  If the statement has a description, creates a log scope with the first line of that description
2.  Evaluates the Source; if the expression is not a vector, an error is raised
3.  Reads the current item in Source; the same logic is performed as Set Context Statement, as if it were the Context Value
4.  If the statement has a description, creates a log scope with current item
5.  Executes the Body
6.  Closes the log scope if created
7.  Moves next on the Source; if there is a current item, Step 2 is performed
8.  Closes the outer log scope if created

#### Continue Statement
This has no elements; if this statment is found, the execution engine advances to the next iteration of the enclosing Iteration or Context Iteration statement. If there is no enclosing iteration block, a warning is written to the log and no other action is performed.
    
#### Declare Global Variable Statement

This is comprised of a Variable Name and an optional Variable Value. When this statement is encountered, the execution engine does the following:

1. If an instruction other than _Declare Global Variable_ was executed, a fatal (uncatchable) error is raised
2. Initializes a new runtime variable in the global scope
3. If a variable value is present, it is evaluated; if the expression type does not match the variable type (Scalar, Vector, Map), an error is raised; otherwise the value is assigned to the runtime variable
4. If no variable value is present, an empty string, vector, or map is assigned to the variable

Variables declared in the global scope are assignable by asynchronously-running blocks.

#### Error Statement

This has no elements; if this statement is found, the execution engine changes the execution status to `failed`.

#### Execution Directive Statement

This instructs the executer to change the execution behavior of the instructions it contains. It consists of:

* Retry Count - an integer specifying how many times the body should be repeated; the default value is 0
* Asynchronous - a flag indicating that the body should be executed asynchronously
* AsyncToken - a token name that can be waited for
* LockToken - a token name (or empty string) that can be prefixed with '!' to indicate a global lock
* Timeout - the number of seconds for which the block should be attempted
* Execution Policy - either always or on change
* Isolation - specifies that all remote operations within the block will be executed in a new process, which will be terminated when the control flow leaves the block
* Credentials - name of the UserName/Password credentials that will be used to start a new process to perform the remote operations within the block
* Additional flags - currently unused
* Body - a block of statements with validity range

When this statement is found, the execution engine proceeds as follows:

1. If the statement has a description, a log area is created with the first line of that description
2. If Execution Policy is specified, all Ensure Operations will execute in the execution phase of a collection and Execute phase are executed, regardless of whether changes were detected
3. If a Retry value is specified and a non-major error occurs during one of the following steps (and the number of retries has not been exceeded), the execution state is reset to the state it had when it entered the block, and execution starts again from the following step onwards
4.  If a Timeout value is specified, a [CancellationTokenSource](https://msdn.microsoft.com/en-us/library/system.threading.cancellationtokensource(v=vs.110).aspx) is created with the specified value; if the timeout has elapsed before the remainder of the statement is executed, a CancellationToken will be created that signals the current task to safely abort
5.  If a Lock Token is specified:
    1.  If the current statement is within a block that specifies a Locked flag, a fatal error is raised
    2.  If there are any other statements currently executing in a statement that shares a LockTocken, either in the current execution (or in all executions when the token is prefixed with a `!`), wait until the other statements finish
6.  If Asynchronous is specified:
    1.  A new child execution engine is created with the specified token
    2.  All current runtime variables from the current execution are copied to the child
    3.  The child execution engine then executes the Body
    4.  If an error is encountered during the child execution, the error will be raised in the parent execution engine, where the with block began
7.  Otherwise (no async), the Body is executed
8.  Closes the outer log scope if created

#### Fail Statement

This statement has no elements; when this statement is encountered, the execution engine changes the execution state to Failing and terminates execution immediately; recovery is not possible, even within a Try/Catch statement.

#### Force Normal Statement

This statement has no elements; when this statement is encountered, the execution engine changes the execution status to Normal.

#### Iteration Block Statement

This enumerates a vector expression, changes the context each time, and executes the contained statements. It's comprised of:

*   Iteration Variable Name – a valid scalar variable expression
*   Source – a valid vector expression
*   Body –  a scoped statement block

When this instruction occurs, the execution engine performs the following steps:

1. If the statement has a description, it creates a log scope with the first line of that description
2. Evaluates the Source; if the expression is not a vector, an error is issued
3. Reads the current item in Source; creates a runtime variable with the specified name in the current scope
4. If the statement has a description, creates a log scope with the current item
5. Executes the Body
6. Closes the log scope if created
7. Continues in the Source; if there is a current item, step 2 is executed
8. Closes the outer log area, if created

Note that a runtime variable is always created and never assigned. This means that if a runtime variable was already created in a higher scope, this value would not be overwritten.

#### Log Statement

This statement has two elements:

*   Log Message – an expression
*   Log Level – an integer enum with valid values of (0=Debug, 10=Information, 20=Warning, 30=Error)

When this statement is encountered, the execution engine writes the specified message to the specified level. If Warning or Error is specified, the execution status will change appropriately (Error causes Failing, Warning causes Warning; unless already Failing), but an error will not be raised.

#### Predicate Statement

This allows branching logic in a script. It's comprised of:

*   Predicate – one of the six types of predicates
*   True Statements – a scoped statement block
*   False Statements – a scoped statement block

There are six types of Predicates; each can evaluate as true or false.

* And Predicate- contains a set of Predicates; evaluated as true if all contained predicates are evaluated as true, otherwise as false
* Or Predicate- contains a set of Predicates; evaluates to true if any of the contained predicates evaluate to true, otherwise evaluates to false
*   Not Predicate - contains a single Predicate; evaluates to true if the contained predicate evaluates to false; otherwise, evaluates to true
* Equality Predicate - contains two expressions; evaluates to true if both evaluated expressions are identical (case dependent)
* Inequality - contains two expressions; evaluates to true if both evaluated expressions are not identical (case sensitive)
* True - contains a single expression; evaluates to true if the expression (case insensitively) is identical to "true"

When this statement is encountered, the execution engine does the following:

1.  If the statement has a description, the engine creates a log scope with the first line of that description
2.  Tests the Predicate, and evaluates either True Statements or the False Statements
3.  Closes the outer log scope if created

#### Return Statement
This has no elements; if this statment is found, the execution engine ends the current script and returns execution to the calling script, if any.

#### Set Context Statement

This changes the current execution context. It's comprised of:

*   Context Type – either directory, role, or server
*   Context Value – an expression
*   Body –  a scoped statement block

When this statement is encountered, the execution engine does the following:

1.  If the statement has a description, creates a log scope with the first line of that description
2.  Evaluates the Context Value; if the Context Type is
    1.  directory – creates a new context based on current context, but with the current item as the working directory
    2.  role – if the server in context does not have the specified role, the entire block is skipped; otherwise, it creates a new context based on the current context with the current item as the specified role
    3.  server - if the execution allows it, creates a new context based on the current context with the current item as the specified server; otherwise, raises an error
3.  Closes the outer scope if created

#### Throw Statement

When this statement is encountered, the execution engine changes the execution status to Failing and raises an error. This error may be caught if within a Try/Catch statement. If a message is provided after the `throw` statement, it is written to the current log context as an error.

#### Try Statement

This statement has two statement blocks: a Body, and an Error Handler. When the execution engine encounters this statement:

1. If the statement has a description, a log area is created with the first line of that description
2. If an error occurs during the following step, the Error Handler is executed instead of reporting an error and changing the execution state as usual
3. The Body is executed
4. Closes the log scope if it was created

#### Warn Statement

This Statement has a single, optional element: Force flag. When this statement is encountered, the execution engine changes the execution status to Warning unless the Force flag is not specified and the current execution status is Failing.