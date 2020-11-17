---
title: Creating and Customizing Tasks
sequence: 200
keywords: proget,packages
---

  The built-in tasks are a good starting point, but as you expand ProGet within your organization, you may want to customizing tasks to help model governance and compliance policies.     

  A task has a name and description, and is comprised of a set of more granular security attributes that define what principals may or may not do when granted the task. Some attributes may be scoped to a feed, whereas others can only be applied to the system scope. For example, scheduled jobs are managed at the system level (and are used by all feeds), the "Scheduled Jobs" attribute cannot be scoped to a single feed.

  ## Proxy Security Attributes {#proxy data-title="Proxy Security Attributes"}

  Any system that allows for granular security attributes has the concept of "granting by proxy", or proxy attributes. For example, even if you only grant permission to create and delete files on a file system, you are still granting edit permission by proxy; a user can simply delete and then create a file as a workaround for not being able to edit it. Likewise, users with only edit permissions can delete by proxy, because they can simply edit the file to have no content.

  There is no sensible way for any software to prevent proxy operations, which is why it's important to understand what proxy attributes you may be unintentionally allowing.

  ## Nonsensical Security Attributes {#nonsensical data-title="Nonsensical Security Attributes"}

  Granular security attributes can also lead towards creating "nonsensical" policies. For example, giving manage but not view permissions is nonsensical because you can't manage something you can't view. In this case, the behavior is undefined; generally, you'll be able to view the thing, but there are some places where it might not be visible.  

  While you wouldn't intentionally create such a policy, it might occur if you create multiple overlapping restrictions and permissions across multiple overlapping groups. The best way to mitigate this risk is to carefully plan your use of restrictions.

## Transitive Access {#transitive data-title="Transitive Access"}

Even with a well-planned security restrictions in one part of a system, users can bypass those restrictions from another part of the system that didn't consider the same security restrictions. For example, simply denying view privileges on sensitive files on a network drive is not enough to protect sensitive data; anyone with access to the backups will have transitive access to all of those files.  Of course, you can encrypt the backups, but then anyone with the decryption key will have transitive access to all the backups.

## Security Attribute Reference {#security data-title="Security Attribute Reference"}

Some of the attributes are denoted with a superscript F, which indicates that they are scopable by feed.

### Administration

##### Configure ProGet {#admin data-title="Configure ProGet"}

**This attribute is a proxy for all other attributes, and should be only granted to people you intend to be system administrators.** It is a sort of "administrative catch all" that is used to secure pages in the administration section not covered by other attributes, including security, extensions, and all settings.

##### Manage Connectors<sup>F</sup> {#connectors data-title="Manage Connectors"}

This allows users to create, edit, and delete connectors. This can provide transitive access to editing feeds that use a connector.

##### Manage Feeds<sup>F</sup> {#feeds data-title="Manage Feeds"}

This allows for full control over the feed, including changing package stores. It is effectively administrative access for the feed, and is a proxy attribute for all other attributes.

##### Manage Scheduled Tasks {#manage-scheduled data-title="Manage Scheduled Tasks"}

Manage will allow users to manually execute, and also deactivate and change the status of these jobs.

##### View Scheduled Tasks  {#view-scheduled data-title="ViewScheduled Tasks"}

View will allow users to see the result of scheduled jobs.

### Licenses

##### Manage Licenses {#licenses data-title="Manage Licenses"}

Allows users to create, edit, and delete known license types, as well as define licensing rules on feeds and globally. This is not feed-specific by design; many packages do not use standard SPDX codes, but instead specify a custom URL (often, to their project site or source repository), and it would be practically unusable to not be permitted to define new known license types, or to make known license types a feed-specific setting.

### Credentials

:::attention {.analogy}
Currently, ProGet only uses Credentials for advanced Active Directory configuration; eventually we will use credentials for connecting username/passwords as well.
:::

##### Manage Credentials {#credentials data-title="Manage Credentials"}

View Passwords will allow users to view sensitive (encrypted) fields on resource credentials. This attribute primarily exists to allow some users to view credentials only associated with certain environments.

##### View Passwords {#view-passwords data-title="View Passwords"}

Manage will allow users to create, edit, delete credentials, as well as view all sensitive (encrypted) fields. This could provide transitive access to connected systems, so be very careful when granting this.

### Vulnerabilities

##### Assess {#vulnerabilities data-title="Assess Vulnerabilities"}

Allows users to create, edit, and delete assessment types, as well as asses vulnerabilities.

### Feeds

These attributes all allow for manipulation of packages in a feed. Generally, you offer Add and Pull to package authors, but restrict Overwrite and Delete access to administrative users; packages are intended to be immutable (and can be automatically purged through retention policies), and giving this access encourages a suboptimal workflow.

_Note: Delete with Add privileges are proxy attributes for Overwrite._

##### Accept Package Promotions<sup>F</sup> {#accept-promotions data-title="Accept Package Promotions"}

This attribute permits promoting a package from another feed (the "source" feed) into the feed this permission applies to. You should also grant View and Download Package permissions on the source feed.

##### Add Package <sup>F</sup> {#add-package data-title="Add Package"}

The Add attribute allows for UI- and API-based adding of packages.

##### Delete Package <sup>F</sup> {#delete-package data-title="Delete Package"}

The Delete attribute allows for UI- and API-based deleting of packages. 

_Note: Delete with Add privileges are proxy attributes for Overwrite._

##### Download Package <sup>F</sup> {#download-package data-title="Download Package"}

The Download attribute allows for UI- and API-based downloading of packages. This attribute is essentially required for read-only access to a feed. There are some very limited use cases for allowing viewing feed and package metadata, but not allowing packages themselves to be downloaded (or vice versa).

##### Overwrite Package <sup>F</sup> {#overwrite-package data-title="Overwrite Package"}

The Overwrite attribute allows for UI- and API-based overwriting of existing packages. 

_Note: Delete with Add privileges are proxy attributes for Overwrite._

##### Pull Package <sup>F</sup> {#pull-package data-title="Pull Package"}

The Pull attribute only allows for using the UI's "pull package" function. Granting only Pull may be useful for building a sort of approval workflow for connector packages.

##### Unlist Package <sup>F</sup> {#unlist-package data-title="Unlist Package"}

The Unlist attribute allows for UI- unlisting of packages from the Feed and the API.

##### View Package <sup>F</sup> {#view-package data-title="View Package"}

The View attribute allows for UI- and API-based viewing of packages. This attribute is essentially required for read-only access to a feed. There are some very limited use cases for allowing viewing feed and package metadata, but not allowing packages themselves to be downloaded (or vice versa).

