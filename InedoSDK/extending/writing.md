---
title: Writing a Simple Create File Operation
sequence: 30
keywords: inedo, inedo sdk
---

You can easily add new operations to BuildMaster, Hedgehog, and Otter using any .NET language.

Follow this guide to create a simplified version of the Create File operation that is already included in the Inedo Core extension. First, make sure to create a project as outlined in [Creating an Extension using the Inedo SDK](/docs/inedosdk/extending/creating).

Next, create a new public class called `CreateFileOperation`, and have it inherit
the [`ExecuteOperation`](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/ExecutingOperation) class:

```
public class CreateFileOperation : ExecuteOperation
{
}
```
Since this operation is supposed to create a file, it would be nice if we could access
the file name and what to put in the file as inputs. To do this, just add a couple of properties and attributes:

```
[Required]
[ScriptAlias("Name")]
[DisplayName("File name")]
public string FileName { get; set; }

[ScriptAlias("Text")]
[DisplayName("Contents")]
[Description("The contents of the file. If this value is missing or empty, a 0-byte file will be created.")]
public string FileText { get; set; }
```

Here's what everything means:

{.docs}
- **[`[Required]`](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Documentation/RequiredAttribute)** - the user must supply a value for this property for the plan to validate
- **[`[ScriptAlias(name)]`](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility/ScriptAliasAttribute)** - this is what the name of the property will look like to OtterScript; this value is required and must be unique to the type of operation
- **`[DisplayName(name)]`** - this is displayed in the graphical plan editor to provide a friendlier name than the script alias; this is optional
- **`[Description(text)]`** - another optional attribute that provides some additional help text in the graphical editor

Now that the inputs are configured, write the code that will actually create the file:

```
public override async Task ExecuteAsync(IOperationExecutionContext context)
{
	var path = PathEx.Combine(context.WorkingDirectory, this.FileName);
	var fileOps = context.Agent.GetService&lt;IFileOperationsExecuter&gt;();
	this.LogDebug($"Creating {path}...");

	await fileOps.CreateDirectoryAsync(PathEx.GetDirectoryName(path));
	await fileOps.WriteAllTextAsync(path, this.Text ?? "");

	this.LogInformation(this.FileName + " file created.");
}
```

Although the [ExecuteAsync](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/ExecuteOperation/ExecuteAsync(IOperationExecutionContext)) method isn't doing too much, we'll break everything down right here:

{.docs}
- **async**

   The **async** keyword in the method declaration instructs the compiler to allow the **await** keyword in the method body. Otter operations can execute asynchronously; if you don't know what this means, consider reading [this MSDN article](https://msdn.microsoft.com/en-us/library/hh191443.aspx). If you would prefer to implement **ExecuteAsync** synchronously, you can just omit the **async** keyword and return the static **Complete** property when the method is complete.
- **var path = PathEx.Combine(context.WorkingDirectory, this.FileName);**

   This allows a relative path to be used for the **FileName** property. Without it, the operation would only work correctly when a user supplies an absolute path. Note that absolute paths will still work; if the second argument of **PathEx.Combine** is absolute, the first argument is ignored.
- **var fileOps = context.Agent.GetService&lt;IFileOperationsExecuter&gt;();**

   This requests a **[IFileOperationsExecuter](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Agents/IFileOperationsExecuter)** service from the Otter agent in the current context. This interface is an abstraction that allows a common set of file system operations on either hosted or SSH agents.
- **await fileOps.CreateDirectoryAsync(PathEx.GetDirectoryName(path));**

   This is a simple way to ensure that the directory exists where we are trying to write the file.
- **await fileOps.WriteAllTextAsync(path, this.Text ?? "");**

   This actually writes the desired text to the file.

For brevity, we've left out the logging messages; but those are pretty self-explanatory. Anything logged will be associated with the current operation.{.info}

Now apply a few attributes to the operation itself to make it discoverable to Otter:

```
[DisplayName("Create File (Example)")]
[Description("Creates a file on a server.")]
[ScriptAlias("Create-File-Example")]
[ScriptNamespace("HDARS")]
[Tag("files")]
[DefaultProperty(nameof(FileName))]
public class CreateFileOperation : ExecuteOperation
{
}
```

The meaning of **DisplayName**, **Description**, and **ScriptAlias** is the same as for properties. As for the other attributes:

{.docs}
- **[ScriptNamespace](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility/ScriptNamespaceAttribute)** - specifies a prefix that is used to qualify the
name specified in **ScriptAlias(name)** - For convenience, this attribute
can also be applied to the assembly rather than on each operation individually.
- **[Tag](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Documentation/TagAttribute)** - specifies a tag which acts as a kind of category for the operation. This attribute can be applied multiple times and can help with discoverability.
- **DefaultProperty** - specifies the name of a property on the operation that receives the default argument value. When in script form, the default argument is the value this is passed to the operation positionally, rather than by name. A default is not necessary, but can help increase the readability of a plan when it is viewed or edited as OtterScript.
