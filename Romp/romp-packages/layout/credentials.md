---
title: Credentials
subtitle: Passwords and Other Secret Credentials
sequence: 40
keywords: romp, passwords, secrets, credentials
---

Romp supports *Resource Credentials*, which are named credentials used to access a protected resource. Usually, credentials include secrets such as a username and password combination, and are stored securely using encryption, both in memory (during runtime), and on disk.

Like [variables](/support/documentation/romp/romp-packages/layout/variables), the required credentials are defined in a `packageCredentials.json` file in the package root. It is an array of objects that describe credentials. Each object has the following properties.

|<!-- -->    | <!-- -->   |
| ---        |  ---          |
|`type<sup>R</sup>` | a *string* describing the type of the credential; typically it's `UsernamePassword` |
| `name<sup>R</sup>` | a *string* that contains the name of the credential used by the installation script |
| `description` | a *string* to document the credential’s intended usage |
| `restricted` | a *boolean* that indicates whether the `$CredentialValue` variable function may extract a value from the credential; defaults to false |  
| `defaults` | an *object* with key/value pairs representing a non-secured credential property and its default value <br/><br/> *Note: if a secured property (like Password) is defined, it will be ignored* |

An "`R`" denotes a required property. When an object is not specified for a variable, a string indicates a required text variable, while a null represents a required text variable without a value.

### Example {#example data-title="Example"}

```
[
  {
    "type": "UsernamePassword",
    "name": "MyServerLogin",
    "description": "Use sdennis",
    "defaults": { // JSON representation of credentials object (plaintext)
      "UserName": "sdennis",
      "Password": null // cannot define because it’s secure
    }
 ]
 ```

 In the above example, three username/password credentials are specified. In the first definition, the credentials are specified as pure JSON with any secrets clearly exposed. In the second definition, the JSON for the credentials object has been base64 encoded to help prevent revealing a secret through casual observation. In the third definition, the object is completely unspecified, and Romp will prompt for it when installing the package.

 Embedding credentials in a package is a convenience, and the limited obfuscation provided should not be mistaken for any kind of cryptographically secure storage. The only secure way to store credentials in a package is to not store them at all, or to encrypt the entire package by some other means.

## Locally Stored Credentials {#locally data-title="Locally Stored Credentials"}

Romp can store named credentials in an encrypted manner in its local configuration database, meaning you won't have to enter it at installation time or pass it in as an argument. See the [romp store credentials](/support/documentation/romp/command-line/command-overview/credentials#store) command for more details.

## Prompting for Credentials {#prompting data-title= "Prompting for Credentials"}

Credentials may be passed in as an argument to Romp. Like variables, if you don't pass in a required variable, and one isn't defined as a locally-stored credential, then you will be prompted for it (when running interactively). Otherwise Romp will issue an error and not proceed with installation.

## Credentials as Variable Values {#values data-title="Credentials as Variable Values"}

You can use the `$CredentialProperty` variable function to extract a property from a credential, unless that credential is marked as "restricted". When restricted, only operations that can access secure storage will be able to read these properties.
