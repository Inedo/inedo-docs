---
title: "HOWTO: Publish npm Packages to a Private Registry in ProGet"
order: 2
---

ProGet lets you easily set up private registries for your [npm](https://www.npmjs.com/) packages, so you can publish, store, and share them internally.

This article will show you how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to act as a private npm package Registry. We'll also look at how to create, publish, and install packages from this feed.

:::(info)(Note: Scoped Packages)
In this guide we create a package that is scoped to `@my-organization`. A scoped package in npm is a package that is grouped under a "namespace". Scoped packages are useful for organizing related packages, such as when public registries are added, and helps to reduce dependency confusion attacks.

Scoped packages are prefixed with an `@`, followed by the namespace, e.g., `@my-organization/my-package`.
:::

## Step 1: Create a New Feed

First, we will create a feed to host your npm packages. Start by selecting "Feeds" and "Create New Feed". Next, select "npm Packages".

![](/resources/docs/proget-npm-createfeed.png){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. From here, we will give our feed a name. For this article, we will call it `internal-npm`, and then click "Create Feed".

![](/resources/docs/proget-npm-internal-name.png){height="" width="50%"}

We are then presented with several options. This relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however they are only for users looking to use third party packages. Leave these boxes unchecked, and select [Set Feed Features]. You will then be redirected to your new `internal-npm` feed, currently empty.

![](/resources/docs/proget-npm-internal-empty.png){height="" width="50%"}

## Step 2: Build Your Package

To create a package, navigate to the directory you wish to create it in and initialize a new `package.json` scoped to the namespace `@my-organization` by running running the `npm init` command:

```bash
$ npm init --scope=@my-organization
```

For Yarn, the command is similar:

```bash
$ yarn init --scope=@my-organization
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

To publish your package to your `internal-npm` feed, you will need to run the `npm publish`:

```bash
$ npm publish --registry=https://«proget-url»/npm/internal-npm/ 
```

Or in Yarn using the `yarn publish` command:

```bash
$ yarn publish --registry=https://«proget-url»/npm/internal-npm/ 
```

Your package should then be uploaded to your `internal-npm` feed:

![](/resources/docs/proget-npm-internal-uploaded.png){height="" width="50%"}

## Step 5: Add the Feed to npm Clients

To install packages from the `internal-npm` feed, you'll need to add it as a source in your npm client using `npm config`: 

```bash
$ npm config set registry http://«proget-url»/npm/«feed-name»
```

Or in Yarn using `yarn config`:

```bash
$ yarn config set registry http://«proget-url»/npm/«feed-name»
```

You can confirm that the feed has been set correctly by using `npm get registry` for npm, `yarn config get registry` for Yarn.