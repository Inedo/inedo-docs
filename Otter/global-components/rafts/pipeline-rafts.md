---
title: Pipeline Rafts
subtitle: Pipeline Rafts
keywords: otter,rafts
sequence: 200
---

**Pipeline Rafts** are a special type of raft added in Otter 2.1. Unlike other raft types, they don't store any data on their own, but refer instead to other rafts based on the current environment.

## Creating and Configuring a Pipeline Raft

Click the Create Pipeline Raft button on the Admin > Rafts page to open the Create Pipeline Raft dialog.

Enter environment-raft mappings one per line in the format "Environment=Raft" in the Environment Rafts field. When the pipeline raft is used in a plan, the environment in context will be used to select the raft from this list. If no matches are found, an error will be raised when the plan is executed.

Example of environment-raft mappings:

```
Integration=Int-Raft
Production=Prod-Raft
```

## Pipeline-only Rafts

A non-pipeline raft may be designated as a *pipeline-only* raft. This means that the pipeline-only raft may **only** be used indirectly by a pipeline raft. Pipeline-only rafts are displayed in a separate list on the Admin > Rafts page. To designate a raft as pipeline-only, check the corresponding checkbox in its configuration.
