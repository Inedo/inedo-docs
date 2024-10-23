---
title: "HOWTO: Publish npm Packages to a Private Registry in ProGet"
order: 2
---

With ProGet you can set up private repositories for your Python packages, so you can publish, store, and share them internally.

This article will run through how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to act as a private Python package repository, as well as covering how to create, publish, and install packages from this feed.

## Step 1: Create a New Feed

To start with, we will create a feed to host your Python packages. In ProGet, select "Feeds" and "Create New Feed". Then, select "Python Packages" under "Developer Libraries".

![](/resources/docs/proget-pypi-createfeed.png){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. Now name your feed. For this example we will call it `internal-pypi`.

![](/resources/docs/proget-npm-internal-name.png){height="" width="50%"}

You are then presented with several options. These relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however they are only for users looking to use third party packages. Leave these boxes unchecked, and select [Set Feed Features]. You will then be redirected to your new `internal-npm` feed, currently empty.

![](/resources/docs/proget-npm-internal-empty.png){height="" width="50%"}

## Step 2: Build Your Package

To create a package, navigate to the directory you wish to create it in and initialize a new `package.json` scoped to the namespace `@my-organization` by running this command:

```bash
$ npm init --scope=@my-organization
```
Note: if you do not want to scope your package, omit `--scope=@my-organization`.

You will be asked for details of the project name, version, etc. After this you will need the URL of your `internal-npm` feed, which can be found on the feed page:

![](/resources/docs/proget-npm-internal-url.png){height="" width="50%"}

Once you have the URL, edit your `package.json` and add a `publishConfig` field, adding the `internal-npm` URL. This will also scope the package to the namespace `@my-organization`: 

```json
{
  "name": "@my-organization/npm-hello-world",
  "version": "1.0.0",
  "description": "Simple Hello World",
  "main": "index.js",
  "publishConfig": {
    "@my-organization:registry": "https:/proget.corp.local/npm/internal-npm/"
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

If you do not want to add a scope to your package, you can edit the `name` and `publishConfig` fields as follows:

```json
 "name": "npm-hello-world",
...
  "publishConfig": {
    "registry": "https:/proget.corp.local/npm/internal-npm/"
  },
```

## Step 3: Authenticate to your npm Feed

Next you will need to authenticate to your `internal-npm` feed creating an `_auth` token and then [configuring it in your npm client](/docs/proget/feeds/npm#authenticating-to-npm-feeds).

## Step 4: Publish Your Package to ProGet

To publish your package to your `internal-npm` feed, you will need to run the npm publish command by entering:

```bash
$ npm publish --registry=https://«proget-url»/npm/internal-npm/ 
```

Your package should then be uploaded to your `internal-npm` feed:

![](/resources/docs/proget-npm-internal-uploaded.png){height="" width="50%"}

## Step 5: Add the Feed to npm Clients

To install packages from the `internal-npm` feed, you'll need to add it as a source in your npm client by entering: 

```bash
$ npm config set registry http://«proget-url»/npm/internal-npm
```

You can confirm that the `public-npm` feed has been set correctly by entering:

```bash
$ npm get registry
```