---
title: Shell Scripting and BuildMaster
sequence: 800
keywords: buildmaster, shell, bash
show-headings-in-nav: true
---

Shell scripting using Bash is the standard for automating tasks on Unix or Linux-based operating systems. Interaction is typically done using the command line, but also can be executed directly by setting the execute file system permission on a script file, and executing it. BuildMaster is designed to seamlessly integrate its pipelines and plans with Shell scripts, and can store them as script assets to be re-used across your application deployment portfolio.

## Storing Shell Scripts in BuildMaster {#assets data-title="Storing Shell Scripts"}

Shell scripts can be stored in BuildMaster as script assets. If a script is intended to be shared throughout multiple applications in the system, it should be stored as a global script asset. For application-specific scripts, they should be stored as a script asset in the context of an application.

## Running Shell Scripts {#running data-title="Running Shell Scripts"}

After writing or uploading scripts as a script asset, you can invoke them with the [`SHCall` operation](/docs/buildmaster/reference/operations/general/shcall) in a build or deployment plan as follows:

```
shcall ConfigDirectory(
  arguments: -domain=hdars.local -user=$UserName
);
```

Shell scripts, regardless of whether they are invoked as direct text or script assets, are run directly on the server in the context of the build or deployment plan.

## Inline Shell Statements {#inline data-title="Inline Shell"}

With the `SHExec` operation, you can incorporate scripts directly into build or deployment plans. For example:

```
shexec >>
  echo "Hello from $ApplicationName";
>>;
```

BuildMaster will automatically replace variables within the script with either variables defined in the plan or [configuration variables](/docs/buildmaster/administration/configuration-variables). In the above example, `$ApplicationName` would be replaced with the name of the application in context as the plan is run.

## Shell Scripts vs. OtterScript {#comparison data-title="Shell Scripts vs. OtterScript"}

Because a script can do "anything", there's an obvious overlap between BuildMaster's built-in operations and a shell script. In general, you should use a built-in operation when possible. Operations are designed to be massively parallel, cancellation-aware, and highly performant, and also provide descriptions when viewed outside of code.

Many scripts, on the other hand, might be written to run interactively (i.e. in a command line REPL), and may prompt for user input if an argument is missing, or do other things not suitable in non-interactive, scalable environments.

## Advanced Scripting {#advanced data-title="Advanced Scripting"}

### Evaluating Shell Script Literals

OtterScript is not a general-purpose programming language (although technically it is Turing complete), and thus doesn't have built-in support for things like arithmetic. You could, of course, write a [custom variable function](/docs/buildmaster/execution-engine/runtime-variables#variable-functions) to support this, but using the variable function [`$SHEval`](/docs/buildmaster/reference/functions/linux/sheval) is much simpler. For example, if you wanted to convert the value stored in one variable (`$minutes`) to milliseconds. You could `$SHEval` the simple expression `$minutes * 60 * 1000`:

```
set $milliseconds = $SHEval($minutes * 60 * 1000);
```

`$SHEval` runs the expression on the server currently in context, so use it inside of an if/else block to perform different operations depending on the results of the expression on that server.
