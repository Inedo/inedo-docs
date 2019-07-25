---
title: Overview
subtitle: Otter's Execution Engine
keywords: otter,excecution-engine
sequence: 100
---

To monitor, configure, and orchestrate your infrastructure, Otter uses an advanced execution engine that's capable of running thousands of different operations and scripts on thousands of different servers. If you're already familiar with general scripting or programming concepts, then much of Otter will be familiar to you.

Otter's execution engine is an integral part of Otter itself, but it's also incorporated into Romp (a stand-alone, command-line tool) to run plans against the local machine for testing or other purposes.

## Operations {#operations}

Operations are tasks the execution engine will run on a server. Otter ships with dozens of [built-in operations](../reference/operations) and <!--there are a number of extensions available that add even more. You --> you can add more through [Otter Extensions](../administration/extensions).

Regardless of where the operation comes from, there are two fundamental types:

{.docs}
- **Ensure** – an operation that defines a desired state of configuration, gathers configuration information from a server, and if the actual and desired configuration differ, then configures or  changes something on that server so that it matches the desired state
- **Execute** – an operation that does, or changes something on a server

You can use either of these operations in any type of plan, though in an ideal world, you would only use ensure operations in [configuration plans](/docs/otter/core-concepts/plans#configuration), and execute operations in [orchestration plans](/docs/otter/core-concepts/plans#orchestration). 

### Declarative vs. Imperative {#declarative-vs-imperative}

Most programmers are familiar with "procedural" (imperative) programming: essentially, describing the specific steps that must be taken to accomplish the goal. The declarative approach, however, focuses more on describing the desired goal. Some Infrastructure as Code tools mandate a single approach, but Otter supports both approaches.

#### Ensure Operations

Ensure operations all start with the word "Ensure", and they are designed to be idempotent, which means they can be run repeatedly with no side effects. This enables you to create **declarative** configuration plans that simply describe the desired state of configuration.

For example, the [Ensure-File](../reference/operations/files/ensure-file) operation, as the name implies, ensures that the file you describe exists (or does not exist) at the location you specify. If it's exactly as it should be, then the operation will not do anything; but if it's different, the operation will bring it to the desired state.

#### Execute Operations

Execute operations start with verbs like "Get" or "Create", and they are designed to perform a particular task, which makes them **imperative**, or procedural in nature.

For example, the [Create-File](../reference/operations/files/create-file) operation will create the file you specify, and may raise an error if you don't specify to overwrite an already-existing file. In some cases, the difference appears to be entirely semantical, as the same goal can be accomplished with either operation.

<tab-block>
<tab name="Declarative (Ensure-File)">

```
Ensure-File hdars.txt (
  Text: hello world!
)
```

</tab>
<tab name="Imperative (Create-File)">

```
Create-File hdars.txt (
  Text: hello world!,
  Overwrite: true
)
```

</tab>
</tab-block>

While using the right semantics are very important to help others understand your goals, the behavior is slightly different depending on which type of plan the operation is in.

### Configuration Plan Runs {#plan-runs}

When running a configuration plan (either routinely or as a job), the execution engine will first perform a "collection run". In this run, the ensure operations will only gather configuration from a server, and then store that configuration and report if it's different than the desired configuration. It's through this mechanism that you can see the *actual* configuration of the servers:

![Otter Configuration Details collected](/resources/documentation/otter/engine-iis-conifg.png){.screenshot}

Note that only the details you specify in the ensure operation are collected and compared; this means that you only see what was important to you. 

When the execution engine performs an "execution run" against a configuration plan, only a minimal set of operations are executed:

{.docs}
- Ensure operations that reported drift
- Execute operations in the same (or nested) block of an ensure operation that reported drift

For example, consider the following block in a configuration plan:

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/engine-sleep-block.png" alt="Otter Configuration Plan Block" />
    </tab>
<tab name="Text Mode (OtterScript)">

```
##AH:UseTextMode
# Ensure HDarsSite
# We need to restart the application pool and wait 5 seconds if the deploy path changes, otherwise we get dropped transactions
{
    Stop-AppPool HDarsAppPool;

    Sleep 5;

    Ensure-Site HDarsSite
    (
        AppPool: HDarsAppPool,
        Path: c:\websites\hdars$HDarsVersionNumber
    );

    Start-AppPool HDarsAppPool;
}
```

</tab>
</tab-block>

When the $HDarsVersionNumber changes (part of the Ensure HDars Site operation which can be seen in text mode), the site will change -- but before doing that, the previous two operations will run because they are in the same block. This behavior lets you balance ideal- and real-world configuration problems.
