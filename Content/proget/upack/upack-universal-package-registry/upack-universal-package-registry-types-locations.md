---
title: "Registry Types and Locations"
order: 4
---

There are three different types of registries (Machine, User, Custom). Each machine can have any number of user and Custom registries:

## Machine-level Registry

A registry with a known location that can be queried by anyone, and by default requires administrator privileges to install/modify.
* Windows: `%ProgramData%\upack`
* Linux: `/var/lib/upack`
 
 ## User-level Registry
A registry with a known location based on the current username, which can be changed by the current user and administrators.
 * Windows: `%USER%\.upack`
* Linux: `~/.upack`

## Custom Registry
A registry with a different location and possibly different permissions.