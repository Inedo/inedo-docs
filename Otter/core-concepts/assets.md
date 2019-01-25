---
title: Assets
keywords: otter,assets
sequence: 400
show-headings-in-nav: true
---

Assets make it easier to automate your infrastructure by leveraging existing scripts and files to create [Configuration and Orchestration Plans][Plans] and scale up.

Moreover, you can restrict access to your assets by creating [Rafts] in various [Environments] to restrict access to specific environments. This helps maintain and enhance compliance and security, and helps you build on the existing knowledge, tools, and scripts you have already invested in.

## PowerShell and Shell Scripts {#powershell data-title="PowerShell and Shell Scripts"}

Otter's [Tight PowerShell and Scripting Integration][PowerShell] makes it easy to use your current scripts to automate your current infrastructure. Once you've added a PowerShell or Shell script as an asset, you can call it as an operation in a [Configuration or Orchestration][Plans] plan, and Otter will execute those scripts on the appropriate server, and store logs about their execution.

## Module Plans {#module data-title="Module Plans"}

Modules are reusable [Plans] that can be called from another plan, or another module, as if it were an [Operation][Operations]. Like other plans, modules are created using the Otter Visual Mode Editor or [OtterScript], and consist of [Blocks]. Module's reusability allows for quick plan creation, and greater accuracy, by using known configuration pieces.

Modules can be useful to spin up generic [Servers] for rapid testing in the cloud, such as a generic modules to create an [AWS Server](https://aws.amazon.com/) for short testing periods, or to create segments of configuration plans that will be used across different [Server Roles].

## Other Files {#other data-title="Other Files"}

Otter can store any file type that a server might need for configuration, such as SSL certificates, read-me text documents, license key files, etc. You can even store your pictures of [Otters Who Look Like Benedict Cumberbatch](https://encrypted.google.com/search?q=Otters+Who+Look+Like+Benedict+Cumberbatch).

These files can be deployed or ensured using the Get Asset and Ensure Asset operations.

[Plans]: /support/documentation/otter/core-concepts/plans
[Rafts]: /support/documentation/otter/global-components/rafts
[Environments]: /support/documentation/otter/modeling-infrastructure/environments
[PowerShell]: /support/documentation/otter/modeling-infrastructure/powershell
[Operations]: /support/documentation/otter/reference/operations
[OtterScript]: /support/documentation/otter/reference/otter-script
[Blocks]: /support/documentation/otter/execution-engine/statements-and-blocks
[Servers]: /support/documentation/otter/core-concepts/servers
[Server Roles]: /support/documentation/otter/modeling-infrastructure/server-roles
