---
title: Registry Types and Locations
subtitle: Registry Types and Locations
sequence: 30
keywords: proget, romp, universal-packages

---

There are three different types of registries (Machine, User, Custom). Each Machine can have any number of User and Custom registries:

## Machine-level Registry {#machine data-title="Machine-level Registry"}

A registry with a well-known location that is queryable by anyone and, by default, requires administrative privileges to install/change.

{.docs}
- Windows: `%ProgramData%\upack`
- Linux: /var/lib/upack

## User-level Registry {#user data-title= "User-level Registry"}

A registry with a well-known location based on the current username that can be changed by the current user and administrators.

{.docs}
- Windows: `%USER%\.upack`
- Linux: ~/.upack

## Custom Registry {#custom data-title="Custom Registry"}

A registry with a different location and potentially different privileges.
