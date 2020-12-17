---
title: npm (Node.js)
sequence: 300
keywords: proget, feeds, npm, connectors
show-headings-in-nav: true
---

A ProGet npm feed is a repository compatible with the npm client for NodeJS. ProGet npm feeds support both scoped and unscoped npm packages.

## npm Feed Limitations {#npm data-title="npm Feed Limitations"}

The npm client does not support using Windows Integrated Authentication, so ProGet attempts to disable its usage for npm API endpoints in favor of basic HTTP authentication. However, disabling Windows Authentication on a per-URL basis does not work in every environment. If you are having trouble getting this working, you may create a second web site in IIS for ProGet with Windows Authentication disabled without acquiring a separate license.

### Build Metadata and npmjs.org

Although the [npm semantic versioning documentation](https://docs.npmjs.com/about-semantic-versioning) mentions support for SemVer2, there is some inconsistency with build metadata (i.e. versions with a "+" symbol in them). The build metadata is not exposed in many npmjs.org queries, where as it is exposed in ProGet queries. When using a connector to npmjs.org, this may present unexpected behavior in the npm client, such as packages not found.

This appears to be limited to a very small number of packages (such as [@hot-loader/react-dom](https://www.npmjs.com/package/@hot-loader/react-dom); we may change this behavior if it's a continued problem, but please contribute to the discussion at [NPM Connector returns plus "+" in versions](https://forums.inedo.com/topic/2948) on the forums.

### npm audit and Proxying (Experimental feature)

Npm Audit Proxying is an Experimental feature. The npm audit API is undocumented and npm, Inc. does not support third-party implementations. But, ProGet can attempt to forward requests to the audit endpoint to npmjs.org or a connector. This may stop working if npmjs.org changes the API or blocks ProGet's requests. You can configure the proxy URL in ProGet 5.3 by navigating to the `Manage Feed` page on your `npm` feed.

## Creating an npm Feed {#creating data-title="Creating an npm Feed"}

Click on the *Feeds* tab in the navigation bar, and you will see a list of feeds in the system.

Click on the *Create New Feed* button and select *npm*.

Supply a value for the <b>Feed name</b> field. This will be the name of your private npm registry, and should contain characters that can be easily typed in a URL. For instance, you could use **private-npm**.

Click the **Create Feed** button, and you should be automatically directed to the newly-created registry.

If your ProGet server has access to the Internet, you should see a listing of packages from npmjs.org. These are called *connector packages*, and are not hosted on your ProGet instance, but are transparently proxied from another server. 

After you create the npm feed, you can add a connector for *npmjs.org* directly within your feed or by clicking on the **Connectors** tab at the top of the page. 

This way you can have access to that rich library of packages in addition to your private ones. Connectors also allow filtering, if you would like to include only a subset of external packages; however, no filtering is performed by default. For more information about connectors, see the [documentation](/docs/proget/core-concepts/connectors).

## Setting up npm {#setting data-title="Setting up npm"}

First, you will need to tell npm where to look for its registry; by default it will be configured for *npmjs.org*. Use the following command, substituting **proget** for the name name/port of your ProGet server, and **private-npm** for the name of the npm feed you created above.

```
[~]$ npm config set registry http://proget/npm/private-npm
```

### Installing Packages

npm packages are installed using [npm](https://docs.npmjs.com/cli/install). To install a package from a ProGet feed, first you will need to set up npm using the above command, then use the following command :

```
npm install {package-name}@{package-version}
```

## npm Token Authentication {#token}

In order to interact with your private registry without using the `npm adduser` command you will need to follow these steps:

- **[Create an API Key](/docs/proget/administration/security/api-keys)** - you can create an API Key in ProGet (Admin > API Key & Access Logs) with Feed Access. you can further restrict this key by associating it to a user you've already given specific permissions
- **Encode The API Key** - Once the key has been generated you will need to encrypt the key along with the username `api`. This needs to be encoded in base64 format. Your string prior to encoding should be: `api:{APIKEY}` 

Once you have this token you will need set your _auth value with it using npm. It is also recommended that you set always-auth to true and set the email associated with this registry.  
          
```
[~]$ npm config set always-auth true
[~]$ npm config set _auth {ENCODEDAPIKEY}
[~]$ npm config set email {email address}
```

### HOWTO: Create a npm API Token

First, create a key by going to ProGet > Admin > API Keys, and entering the value `apikey12345` for the key. Make sure that Feed API is checked.

Next, find a base64 encoder; you can search on Google to find a website that can do it for you, or you can use the `btoa()` function in your browser's development console. The value you'll want to encode is `api:apikey12345` -- and the output will be `YXBpOmFwaWtleTEyMzQ1`.

From there, you can execute the following NPM commands:

1. Run npm config set always-auth true
2. Run npm config set _auth YXBpOmFwaWtleTEyMzQ1
2. Run npm config set _auth support@inedo.com

After that, it should work. Remember, `apikey12345` is just a sample value, for illustration purposes; it can be anythying, and should not be the value we use in our documentation.

### Using scoped registries

To use a registries for scoped packages, you would run similar commands normal [token authenticaion][#token], but you will also need to specify the scope and namespace. To do this you will need to run these commands:

- **[Create an API Key](/docs/proget/administration/security/api-keys)** - you can create an API Key in ProGet (Admin > API Key & Access Logs) with Feed Access. you can further restrict this key by associating it to a user you've already given specific permissions
- **Encode The API Key** - Once the key has been generated you will need to encrypt the key along with the username `api`. This needs to be encoded in base64 format. Your string prior to encoding should be: `api:{APIKEY}` 
- The namespace is equal to your registry excluding the http: or https:. ex: for `http://proget/npm/private-npm` use `//proget/npm/private-npm`

Once you have this token you will need set your _auth value with it using npm. It is also recommended that you set always-auth to true and set the email associated with this registry.  

```
[~]$ npm config set {scope}:registry {registry}
[~]$ npm config set {namespace}:always-auth true
[~]$ npm config set {namespace}:_auth {ENCODEDAPIKEY}
[~]$ npm config set {namespace}:email {email address}
```

#### Scoped Registry Example:

In this example, we will use the following values:
- Scope: `@inedo` 
- Registry: `http://proget/npm/private-npm` 
- API key of `apikey1234`.

First encode the API Key: `api:apikey1234` as YXBpOmFwaWtleTEyMzQ1

Then run:
1. Run `npm config set @inedo:registry http://proget/npm/private-npm`
2. Run `npm config set //proget/npm/private-npm:always-auth true`
2. Run `npm config set //proget/npm/private-npm:_auth YXBpOmFwaWtleTEyMzQ1`
2. Run `npm config set //proget/npm/private-npm:_auth support@inedo.com`

        
## Publishing a Package {#publishing data-title="Publishing a Package"}

You can also publish packages to ProGet using npm. First, use the `adduser` command to log in as ProGet's default Admin account, and when prompted for a password, use the same one that you used to log in to ProGet (default is Admin).

```
[~]$ npm adduser
[~]$ npm publish package.tgz
```
