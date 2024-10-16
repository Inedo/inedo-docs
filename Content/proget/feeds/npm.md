---
title: "npm (Node.js)"
order: 9
---

A ProGet npm feed is a repository that is compatible with the npm client for NodeJS. ProGet npm feeds support both scoped and unscoped npm packages.

## npm Feed Limitations 

The npm client does not support the use of Windows Integrated Authentication, so ProGet attempts to disable its use for npm API endpoints in favor of basic HTTP authentication. However, disabling Windows Authentication on a URL basis does not work in every environment. If you have trouble getting this to work, you can create a second website in IIS for ProGet with Windows Authentication disabled without purchasing a separate license.

### Build Metadata and npmjs.org

Although the [npm semantic versioning documentation](https://docs.npmjs.com/about-semantic-versioning) mentions support for SemVer2, there is some inconsistency with build metadata (i.e., versions with a "+" symbol in them). The build metadata is not exposed in many npmjs.org queries, whereas it is exposed in ProGet queries. When using a connector to npmjs.org, this may present unexpected behavior in the npm client, such as packages not found.

This appears to be limited to a very small number of packages (such as [@hot-loader/react-dom](https://www.npmjs.com/package/@hot-loader/react-dom); we may change this behavior if it's a continued problem, but please contribute to the discussion at [NPM Connector returns plus "+" in versions](https://forums.inedo.com/topic/2948) on the forums.

### npm audit and Proxying 

Starting in ProGet 2023.22, npm audit support has been added to pull vulnerability data directly from ProGet.  This is enabled by default on all npm feeds that have the [vulnerability display and blocking feed feature](/docs/proget-overview?highlight=feed feature#step-4-select-feed-features) enabled.  npm audit support can be enabled, disabled, or configured to proxy to a remote source by navigating to the npm feed's Manage Feed page and configuring `npm Audit` setting.

::: (Info) (NOTE:)
If you previously have configured your npm feed to use an npm audit proxy and want to use ProGet's vulnerability details instead, you will need to change the npm audit settings on your feed's Manage Feed page.
:::

#### Prior to ProGet 2023.22

Npm Audit Proxying is an experimental feature. ProGet may attempt to forward requests to the audit endpoint to npmjs.org or a connector. This may stop working if npmjs.org changes the API or blocks ProGet's requests. You can configure the proxy URL in ProGet 5.3 by navigating to the `Manage Feed` page on your `npm` feed.

## Creating an npm Feed 

Click on the "Feeds" tab in the navigation bar to see a list of feeds in the system.

Click the "Create New Feed" button and select "npm".

Supply a value for the "Feed Name" field. This will be the name of your private npm registry, and should contain characters that can be easily typed in a URL. For example, you could use `private-npm`.

Click the "Create Feed" button, and you should be automatically directed to the newly created registry.

If your ProGet server has access to the Internet, you should see a list of packages from npmjs.org. These are called "connector packages" and are not hosted on your ProGet instance, but transparently proxied from another server.

Once you have created the npm feed, you can add a connector for *npmjs.org* directly to your feed or by clicking on the "Connectors" tab at the top of the page.

This way you can access this extensive library in addition to your private ones. Connectors also allow filtering if you want to include only a subset of the external packages; however, by default, no filtering is applied. For more information about connectors, see the [documentation](/docs/proget/feeds/connector-overview).

## Setting up npm

First, you need to tell npm where to look for its registry; by default, it is configured for npmjs.org. Use the following command and replace `proget` with the name/port of your ProGet server and `private-npm` with the name of the npm feed you created above.

```bash
[~]$ npm config set registry http://proget/npm/private-npm
```

### Installing Packages

npm packages are installed using [npm](https://docs.npmjs.com/cli/install). To install a package from a ProGet feed, first you will need to set up npm using the command above, then use the following command :

```bash
npm install {package-name}@{package-version}
```

## npm Token Authentication 

To authenticate to your feed, you'll need to add an `_auth` token to your `.npmrc` file. This base-64 encoded token will be sent to ProGet as an authentication header.

### Creating an Auth Token

An `_auth` token is a username and password string `«username»:«password»` that's been base64-encoded  We strongly recommend using `api` as the username, and  [API Key](/docs/proget/reference-api/proget-apikeys) as the password for this token. You can create one by navigating to Admin > API Key & Access Logs. Feed Access is all that's required, and you can further restrict this key by associating it to a user you've already given specific permissions

:::(Error) (Base64 Encoding is Required)
npm only supports base64-encoded tokens, and you'll need to use a tool to help you create one. You can search on Google to find a website that can do it for you, use the `btoa()` function in your browser's development console, or a Powershell Script.
:::

#### Example: PowerShell Script to Encode an API Key

Replace `«api-key»` in the script below with your key.

```PowerShell
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("api:«api-key»"))
```

The encoded auth token will be written in the console.

#### Example: Encode in your Web Browser

Press F12 to open the Developer Tools, click on the "Console", then run the following script (replace `«api-key»`).

```JavaScript
btoa('api:«api-key»')
```

The encoded auth token will be written in the console below.

### For npm version 9.4 and above

npm 9.4+ requires that auth tokens are scoped to a server and url. The following command will add the appropriate value to your `.npmrc` file. 

```bash
[~]$ npm config set //«proget-server»/npm/«npm-feed-name»/:_auth «encoded-auth-token»
[~]$ npm config set //«proget-server»/npm/«npm-feed-name»/:email «your-email-address»
```

Make sure to also replace `«proget-server»` with the name and port of your ProGet server and `«npm-feed-name»` with the name of your npm feed. It is also recommended that you set the email associated with this registry.

::: (Warning) (npm login support)
When using the `npm login` and the `npm adduser` commands in npm 9 and above, ProGet 2023.17 or greater is required.
:::

### For npm versions 8 and below

The following command will add the appropriate value to your `.npmrc` file. 

```bash
[~]$ npm config set always-auth true
[~]$ npm config set _auth «encoded-auth-token»
[~]$ npm config set email «your-email-address»
```

### Example Steps to Create an npm API Token

First, create a key by going to ProGet > Admin > API Keys, and entering the value `apikey12345` for the key. Make sure that Feed API is checked.

Next, encode `api:apikey12345` as a base64 string using this PowerShell.

```powershell
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("api:apikey12345"))
```

With a value of `api:apikey12345`, PowerShell (or any base64 encoder) will will output `YXBpOmFwaWtleTEyMzQ1`.

From there, you can execute the following NPM commands:
- For npm version 9.4 and above
    1. Run  `npm config set //proget/npm/private-npm:_auth YXBpOmFwaWtleTEyMzQ1`
    1. Run  `npm config set //proget/npm/private-npm:email support@inedo.com`
- For npm version 8 and below
    1. Run `npm config set always-auth true`
    2. Run `npm config set _auth YXBpOmFwaWtleTEyMzQ1`
    2. Run `npm config set email support@inedo.com`

After that, it should work. Remember, `apikey12345` is just a sample value for illustration purposes; it can be anything, and should not be the value we use in our documentation.

### Using scoped registries

To use a registry for scoped packages, you'll need to specify the scope and namespace.

Before setting the auth token, you'll need to set a configuration scope. 

```bash
[~]$ npm config set @«scope-name»:registry «feed-api-endpoint»
```

Then, you'll need to set a namespace-qualified auth token with these commands:

```bash
[~]$ npm config set //«proget-server»/npm/«npm-feed-name»/:_auth «encoded-auth-token»
[~]$ npm config set //«proget-server»/npm/«npm-feed-name»/:email «your-email-address»
```

For example, if you wanted to scope the `@kramerica` packages to the `private-npm` feed on the `packages.kramerica.corp` server, you'd run these commands:

```bash
[~]$ npm config set @kramerica:registry https://packages.kramerica.corp/npm/private-npm
[~]$ npm config set //packages.kramerica.corp/npm/private-npm:_auth YXBpOmFwaWtleTEyMzQ1
[~]$ npm config set //packages.kramerica.corp/npm/private-npm:email support@kramerica.corp
```

### Advanced: Authenticating using an npmrc file

The `npm config` command is the more reliable and safe way to make configuration changes because it contains proper safeguards and validation checks to ensure proper structure in the npmrc file.  There are times when you may need to edit the `.npmrc` file directly or create a custom npmrc file to use with the `--userconfig` parameter to the `npm` command.

The npmrc file can be stored:
- per-project (`/path/to/my/project/.npmrc`)
- per-user (`$HOME/.npmrc` or via the environment variable `$NPM_CONFIG_USERCONFIG`)
- global (`$PREFIX/etc/npmrc` or via the environment variable `$NPM_CONFIG_GLOBALCONFIG`)
- npm's installation directory (`/path/to/npm/npmrc`)
- User specified with the `--userconfig` parameter

#### Example for npm version 9 and above

Using the examples from above:

```bash
registry=http://proget/npm/private-npm
always-auth=true
//proget/npm/private-npm:_auth=YXBpOmFwaWtleTEyMzQ1
```

For scoped registries, you can also add `@kramerica:registry=http://proget/npm/private-npm`  right after your registry.

#### Example for npm version 8 and below

Using the examples from above:

```bash
registry=http://proget/npm/private-npm
always-auth=true
//proget/npm/private-npm:username=api
//proget/npm/private-npm:_password=YXBpa2V5MTIzNDU=
```

The password in this case is a Base 64 encoded version of just your password.

For scoped registries, you can also add `@kramerica:registry=http://proget/npm/private-npm`  right after your registry.
        
## Publishing a Package 

You can also publish packages to ProGet using npm. First, configure npm token authentication for your feed using an account or an API key that has the Add Package permission, then run the `npm publish` command. For example:

```bash
[~]$ npm publish package.tgz
```

## Troubleshooting

### Error: Unable to query https://npm.pkg.github.com/

GitHub npm connectors work a bit differently than other connectors.  GitHub does not implement the full npm API specification so certain things like package count and the search API are not working.  To get around this in ProGet, you will need to make sure that you have updated to at least ProGet 2023.20 and then use the following settings:

1. On the *General* tab
   1. For the URL use `https://npm.pkg.github.com/<OWNER>`
   2. For Authentication
      1. Use Basic authentication
      2. For the username, use your GitHub username
      3. For the password, use your  and then use a Personal Access Token (classic)
1. On the *Advanced* tab
   1. Check "Exact package name match only" 
   2. Check "Do not perform health check" 

That should allow you to search for the package by the exact package name and allow your npm applications to pull the packages properly.  

::: (Info)
Please note that partial name searches may not return any values from your GitHub connector since GitHub has not implemented the search API.
:::