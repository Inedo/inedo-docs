---
title: Repository Hooks
show-headings-in-nav: true
sequence: 900
keywords: buildmaster, releases, builds, hooks
---

BuildMaster supports custom webhook events fired by GitHub and GitLab. Visit the [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki) for more information on the configuration required for each specific system.

### Hook & Monitor Plans

Both Repository Monitors and Repository Hooks support common variables that can be used within the plan. The default variables include:

{.docs}
*   `$Branch` - the ref that a commit was pushed to without `refs/heads/` at the beginning, e.g. `master`
*   `$CommitHash` - the ID, SHA, or hash associated with the commit, e.g. `525571720b56ca53d1d3229e774c7b7980d3ff2b`

::: {.attention .best-practice}

**Note:** these variables are _only_ available when a custom plan is specified. If using the _default_ setting (i.e. creating a build for the latest release), these variables are not available. This is primarily to support backwards compatibility of existing pipelines that rely on a configuration variable with the same name at a different scope.

:::

To replicate the _default_ no-plan behavior with these variables in context, you may reference a system-level plan defined simply as:

```
Create-Build
(
    Application: $ApplicationName,
    Variables: %(Branch: $Branch, CommitHash: $CommitHash)
);
```

Additional context may be available depending on the repository host. Visit the [Git extension Wiki](https://github.com/Inedo/inedox-git/wiki) for more information.