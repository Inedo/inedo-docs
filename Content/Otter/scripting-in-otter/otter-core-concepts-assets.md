---
title: "What are \"scripts\" in Otter?"
order: 1
---

Otter is designed to [run your existing scripts](/docs/otter/orchestration-server-automation/otter-jobs-templates) -- PowerShell, Bash, Python, Windows Batch, etc. -- against any number of servers. You can also use your scripts to [verify server configuration](/docs/otter/collecting-verifying-configuration/otter-compliance-as-code-with-powershell-psverify), detect configuration drift, and [remediate/automate changes](/docs/otter/drift-remediation-configuration-as-code/otter-remediating-drift-with-powershell-psensure) as needed.

A "script" in Otter is a file that you've uploaded to Otter, or that is available to Otter in an [external Git repository that you've configured](/docs/otter/scripting-in-otter/otter-rafts-and-git-storage). While Otter can use your existing scripts as-is, you can also add [augmented help](/docs/otter/scripting-in-otter/otter-scripting-augmented-help) to help Otter generate a GUI for scripts that includes parameter descriptions, help text, dropdown, or checkbox inputs, and more.

## Adding Your Scripts to Otter
You can add scripts to Otter by navigating to Scripts > Add Script, and then choosing one of the options.

* **Upload Scripts & Assets**; just browse for files on your computer, and upload scripts and folders in bulk if you'd like

* **OtterScript Orchestration**; this is a special type of script that lets you run  other scripts, perform custom server targeting, and any number of other tasks required for orchestration.
* **Create a blank script / asset**; if you prefer to create a blank script in Otter, you can then edit the file later

You can also add scripts and assets directly to a Git repository that you've configured as a [Git-based raft](/docs/otter/scripting-in-otter/otter-rafts-and-git-storage), and Otter can use those.

## Supported Scripting Languages
In addition to OtterScript, Otter supports the following languages. When a file has one of these extensions, it's considered that type of script.

| Language | File Extension | Available Functionality |
| --- | --- | --- |
| **PowerShell** | `.ps1` | Execute Scripts<br/>Inline Execution<br/>Detect/Remediate Drift<br /> |
| **Bash (Shell)** | `.sh` | Execute Scripts<br/>Inline Execution<br/>Detect/Remediate Drift<br /> |
| **Python** | `.py` | Execute Scripts<br/>Detect/Remediate Drift<br /> |
| **Windows Batch** | `.bat` | Execute Scripts |

## OtterScript Orchestrations
OtterScript is a DSL used for deployments, orchestration, and configuration. Creating an OtterScript Orchestration allows you to perform complex orchestration that can run different commands or scripts on different servers, and target those servers sequentially, in parallel, or even with branching and iterating (looping) logic.

Although OtterScript is based on programming logic, you do not need to be a coder to use OtterScript, which offers both a text editor and a low-code visual editor.

See [Jobs & Templates](/docs/otter/orchestration-server-automation/otter-jobs-templates) and [OtterScript & Operations](/docs/otter/scripting-in-otter/otter-otterscript-and-operations) to learn more.

### OtterScript Modules

OtterScript Modules are reusable snippets of OtterScript that you can call from another OtterScript script as if it were an [Operation](/docs/otter/scripting-in-otter/otter-otterscript-and-operations). This allows you to more quickly create other OtterScripts, and provides greater accuracy by using known configuration pieces.

OtterScript Modules can be used as a template for all sorts of things, including provisioning new [Servers](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter) for rapid testing in the cloud, or to create segments of configuration  that will be used across different Server Roles.

Like with OtterScript Orchestrations, you don't need to be a coder to create OtterScript Modules. They use both a text editor and a low-code visual editor.

## Other file types (script assets)
In addition to your scripts, Otter can store any file type that a server might need for configuration, such as SSL certificates, read-me text documents, license key files, etc. 

:::(Warning) (Script Asset File size)
Note that Otter's Script Asset Storage is not designed for large files. There is no hard-coded limit on how large a script asset should be, but anything more than a couple megabytes should be stored outside of Otter. 

Consider ProGet's [Asset Directories And File Storage](/docs/proget/asset-directories-file-storage/what-is-an-asset-directory) if you need a place to store arbitrarily large files.
:::

You can deploy these files in OtterScript using the `Get-Asset` and `Ensure-Asset` operations.
