---
title: "Upgrading to Otter 2023"
order: 1
url-slug: "otter-upgrade-2023"
---

Otter 2023 is a major upgrade, and this article provides information about what changed, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Although Otter 2023 is a major upgrade,  the majority of changes were additive features, platform updates (library upgrades, etc.), and UI changes. 

No major features have been removed and there were no significant changes to the core components; we anticipate the upgrade will be relatively easy and no preparation will be required. 

:::(Warning)
If you're upgradidng from Otter v3 or earlier, make sure to read [what changed in Otter 2022](/docs/otter-upgrade-2022) so you can learn how it may impact your installation.

:::
You can safely downgrade to Otter 2022 without rolling back the database.

## Changes in Otter 2023

Most of the changes were additive between Otter 2022 and Otter 2023.

* **OtterScript Visual Editor Improvements**; we improved the look/styling of the visual editor and added tag-based searching for statements
* **Ability to Clone or Rename Job Template**; you can now clone an existing job template
* **More Options for PowerShell 6/7 Support**; you can now change the default behavior of PowerShell scripts on Windows; before you'd need to specify whether a PowerShell script would use PowerShell or PowerShellCore at the operation level; now you can set a configuration variable for that
* **Added environments to role page**; see [feature request in the forums](https://forums.inedo.com/topic/3926) for more details

### OtterScript Updates
To help simplify your OtterScript scripts, we added a handful of control statements:
* `break` can be used in side of an iteration (loop) statement, and will exit the iteration
* `continue` can be used inside of an iteration (loop), and will skip to the next item
* `return` will exit  the current script or module
* `else if`, which will make long if/else statements easier to work with.

We also made some minor UI changes to the low-code Script Editor that makes it easier to work in visual mode.

### New "Set" Statement Behavior
The set statement (i.e. `set $myvar = myval;`) has a slightly new behavior; it will simply default to using the global scope when creating a new variable instead of the current "kind of local, kind of global" behavior.

For example, consider the following script.
```
# This script works in Otter 2023 but fails in Otter 2022
{
  set $myvar = myval;
}
Log-Information "myvar" is $myvar;
```

In Otter 2022 and earlier, this would fail with an error like `$myvar is not declared`. But if you used `set global $myvar = newval`  then it would work and log the result. In Otter 2023, the script will not error, and will simply work as if you wrote `set global`.

```
# This script works in Otter 2022 and Otter 2023
{
  set global $myvar = myval;
}
Log-Information "myvar" is $myvar;
```

The above example works, but this is not a good general purpose solution, because `set global` *only* considers the global scope for assignment, so the below example could yield confusing behavior:
```
{
  set $myvar = myval;
  
  {
    set global $myvar = my new val;
  }

  Log-Information "myvar" is $myvar;
}
```
The output of the above example would be `myvar is myval`, because `set global` will _only_ assign or create variables in the global scope.

If you want Otter 2023 to create a variable in the local scope, then you can use the `local` scope as follows:
```
# This script fails in Otter 2022 and 2023
{
  set local $myvar2  = myval;
}
Log-Information "myvar2" is $myval;
```
Note that `set local` will _always_ create a variable in the local scope, even if one already exists in a higher enclosing scope.

What made Otter 2022's default `set` behavior a bit confusing is that:
* Variables already initialized in a higher scope would be set
* Uninitialized variables would be set in the current scope

We've made default `set` more intuitive in Otter 2023. The new behavior is:
* Variables initialized in a higher scope will still be set
* Uninitialized variables will be created in the global scope

By defaulting `set` to create new variables in the global scope, this will behave as more users expect.

:::(Warning) (OtterScript Changes Risk Mitigation)
It's very unlikely your scripts will be impacted by any of these changes. If you're relying on the Otter 2022 `set` behavior, you may need to modify your scripts to use `set local` prior to upgrading to Otter 2023.
:::


## Custom Extensions Changes
Otter 2023 targets `Inedo.SDK-2.4`, which means you will not need to rebuild custom extensions you've created for Otter 2022. If you are upgrading from an earlier version of Otter v3 or earlier, you'll need to rebuild your extensions.  See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.


## Upgrade Process
You can perform the upgrade from the Inedo Hub. Like with all upgrades, you should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).

You can upgrade from any version of Otter to v2022, and there is no need to install intermediate versions. But do note that you can only rollback to Otter 3.0; any earlier, and you'll need to restore your database as well.


## Rolling Back

You can rollback using the Inedo Hub.

:::(Info)
**You can rollback to any version of Otter 2022 without restoring the database.**
:::

While there are database schema changes, they are all backwards-compatible, which means you can safely rollback your Otter installation if there's a showstopper bug, and then upgrade later.
