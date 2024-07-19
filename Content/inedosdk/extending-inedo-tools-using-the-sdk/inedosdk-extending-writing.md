---
title: "Writing a Simple Create File Operation"
order: 1
---

You can easily add new operations to BuildMaster and Otter using any .NET language.

Follow this guide to create a simplified version of the Create File operation that is already included in the Inedo Core extension. First, create a project as outlined in [Creating an Extension using the Inedo SDK.](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating){target="_blank"}

Next, create a new public class called `CreateFileOperation`, and have it inherit
the [`ExecuteOperation`](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility.Operations/ExecutingOperation) class:

```
public class CreateFileOperation : ExecuteOperation
{
}
```
Since this operation is to create a file, it would be nice if we could retrieve the file name and contents as input. Just add some properties and attributes to it:

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

- **[`[Required]`](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Documentation/RequiredAttribute?_ga=2.123493259.181855247.1615161210-1123661591.1611720787){target="_blank"}** - the user must supply a value for this property for the plan to validate

- **[`[ScriptAlias(name)]`](https://sdkdocs.inedo.com/sdk/inedosdk/inedo.extensibility/scriptaliasattribute?_ga=2.90518426.181855247.1615161210-1123661591.1611720787){target="_blank"}** - this is what the name of the property will look like to OtterScript; this value is required and must be unique to the type of operation
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

Although the [ExecuteAsync](https://sdkdocs.inedo.com/sdk/inedosdk/inedo.extensibility.operations/executeoperation/executeasync(ioperationexecutioncontext)?_ga=2.74412050.181855247.1615161210-1123661591.1611720787){target="_blank"} method isn't doing too much, we'll break everything down right here:

- **async**

   The **async** keyword in the method declaration instructs the compiler to allow the **await** keyword in the method body. Otter operations can execute asynchronously; if you don't know what this means, consider reading [this MSDN article](https://msdn.microsoft.com/en-us/library/hh191443.aspx){target="_blank"}. If you would prefer to implement **ExecuteAsync** synchronously, you can omit the **async** keyword and return the static **Complete** property when the method is complete.
   <br>
   
- **var path = PathEx.Combine(context.WorkingDirectory, this.FileName);**

   This allows a relative path to be used for the **FileName** property. Without this option, the operation would only work correctly if a user specified an absolute path. Note that absolute paths still work; if the second argument of **PathEx.Combine** is absolute, the first argument is ignored.
   <br>
   
- **var fileOps = context.Agent.GetService&lt;IFileOperationsExecuter&gt;();**

   This requests a **[IFileOperationsExecuter](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Agents/IFileOperationsExecuter){target="_blank"}** service from the Otter agent in the current context. This interface is an abstraction that allows a common set of file system operations on either hosted or SSH agents.
   <br>
   
- **await fileOps.CreateDirectoryAsync(PathEx.GetDirectoryName(path));**

   This is a simple way to make sure that the directory we want to write the file to exists.
   <br>
   
- **await fileOps.WriteAllTextAsync(path, this.Text ?? "");**

   This actually writes the desired text to the file.

For brevity, we have omitted the logging messages; however, these are fairly self-explanatory. Everything that is logged is associated with the current operation.{.info}

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


- **[ScriptNamespace](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Extensibility/ScriptNamespaceAttribute){target="_blank"}** - specifies a prefix that is used to qualify the
name specified in **ScriptAlias(name)** - For convenience, this attribute
can also be applied to the assembly rather than on each operation individually.

- **[Tag](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Documentation/TagAttribute){target="_blank"}** - specifies a tag that serves as a kind of category for the operation. This attribute can be used multiple times and can make it easier to find.

- **DefaultProperty** - specifies the name of a property of the operation that receives the value of the default argument. In script form, the default argument is the value passed to the operation at the position, not the name. A default value is not necessary, but can improve the readability of a plan when it is displayed or edited as OtterScript.