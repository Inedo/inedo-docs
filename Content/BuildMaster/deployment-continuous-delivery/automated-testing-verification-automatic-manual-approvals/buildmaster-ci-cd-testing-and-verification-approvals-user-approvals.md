---
title: "User Approvals"
order: 1
---

There are two types of manual approvals: user and group. A user-based approval can only be entered by the specified user, whereas a group-based approval can be entered by any members of the specified group. You can also specify that more than one member of a particular group must approve a build before deploying to a stage.

In either case, when a user approves a build, the following is recorded:

* User's Name
* Group name (if group-based)
* Stage name
* Approval Description
* User Comments
* Date/Time

Users may revoke a manual approval they've given, provided that the build wasn't already deployed to the target stage.