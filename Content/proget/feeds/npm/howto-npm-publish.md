---
title: "HOWTO: Publish npm Packages to a Private Repository in ProGet"
order: 2
---

ProGet lets you easily set up private repositories for your [npm](https://www.npmjs.com/) packages, so you can publish, store, and share them internally.

This article will show you how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to act as a private npm package repository. We'll also look at how to create, publish, and install packages from this feed.

## Step 1: Create a New Feed

First, we will create a feed to host your npm packages. Start by selecting "Feeds" and "Create New Feed". Next, select "npm Packages".

![](){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. From here, we will give our feed a name. For this article, we will call it `internal-npm`, and then click "Create Feed".

![](){height="" width="50%"}

We are then presented with several options. This relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however they are only for users looking to use third party packages. Leave these boxes unchecked, and select [Set Feed Features]. You will then be redirected to your new `internal-npm` feed, currently empty.

![](){height="" width="50%"}

## Step 2: Create an API Key { #step-2 }

Now you'll need an [API Key](/docs/proget/reference-api/proget-apikeys) to allow your local client to authenticate to your `internal-npm` and publish packages to it, as well as install them once they are in the feed.

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![Admin Overview](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![Create Key](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Then fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-npm` feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one. Check the "View/Download" and "Add/Repackage" boxes, and select "Save".

![New Key](/resources/docs/proget-conda-apikey-2.png){height="" width="50%"}

## Step 3: Build Your Package

To create a package, navigate to the directory you wish to create it in and initialize a new `package.json` by running this command:

```bash
$ npm init
```

You will be asked for details of the project name, version, etc. After this you will need the URL of your `internal-npm` feed, which can be found on the feed page:

![](){height="" width="50%"}

Once you have the URL, edit your package.json and add a `publishConfig` field, adding the internal-npm URL. It should look like this:

```json
{
  "name": "npm-hello-world",
  "version": "1.0.0",
  "description": "Simple Hello World",
  "main": "index.js",
  "publishConfig": {
    "registry": "https:/proget.corp.local/npm/internal-npm/"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Inedo",
  "license": "MIT",
  "dependencies": {
    "npm-hello-world": "^1.0.0"
  }
}
```

### Scoping Your Packages

If you have created scopes for your organization, you can add these to your package by editing the publishConfig field in the package.json. For example, if your package uses the scope `my-scope` you would add:

```json
  "publishConfig": {
    "@my-scope:registry": "https:/proget.corp.local/npm/internal-npm/"
  },
```

## Step 5: Create an .npmrc file

To authenticate to your internal-npm feed, your package will need an .npmrc file containing an `_auth` token. This will use your API Key, encoded into base64. To encode your API Key you can use this PowerShell script:

```powershell
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("api:«api-key»")) 
```

Then create an .npmrc file in your project folder, and enter the following:

```plaintext
registry=https://«proget-url»/npm/internal-npm/
always-auth=true
//«proget-url»/npm/internal-npm/:_auth=YXBpOnByb2dldA==
```

## Step 4: Publish Your Package to ProGet

To publish your package to your `internal-npm` feed, you will need to run the npm publish command by entering:

```bash
$ npm publish --registry=https://«proget-url»/npm/internal-npm/ 
```

Your package should then be uploaded to your `internal-npm` feed:

![](){height="" width="50%"}

## Step 6: Add the Feed to Local npm Environments

To install packages from the `internal-npm` feed, you'll need to add it as a source in their local environment by entering: 

```bash
$ npm config set registry http://«proget-url»/npm/internal-npm
```

You can confirm that the `public-npm` feed has been set correctly by entering:

```bash
$ npm get registry
```