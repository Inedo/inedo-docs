---
title: "Node.js"
order: 3
---


[Node.js](https://nodejs.org/) is a JavaScript runtime environment that allows developers to build various types of applications, from console utilities to server-side web applications written in JavaScript. JavaScript has evolved from a client-side scripting language applicable only within HTML to a full-fledged development language that spans multiple layers. Node applications can be developed using a variety of IDEs, from simple text editors to Atom to VSCode. Node is the language referred to as the "N" part of a MEAN development/deployment stack (MongoDB, Express, Angular, Node) that can be used to quickly build dynamic websites.

## Building Node.js Applications 

Since JavaScript is an interpreted language, compilation in the traditional sense of C/C++ isn't required. However, there are several languages that can be transpiled to JavaScript, including CoffeeScript, Dart, TypeScript, etc. In addition, there are several productivity tools that can be run to turn raw source code into the building blocks of an application:

 - **Babel** - converts modern JS into backward-compatible JS code to run newer features on older platforms
 - **Broccoli** - a JS build tool that packages resources (images, CSS, JS, etc.) into a distributable form
 - **Webpack** - a JS bundler that handles both applications/libraries (.js) and assets (images/fonts/stylesheets)
 - **JSLint** - static analysis tool for JS applications
 - **TypeScript** - a language developed by Microsoft that transpiles into JS
 - **Grunt** - a task runner based on JS to automate common build or other repetitive tasks when developing JS applications
 - **Mocha** - a JS unit test runner
 
These tools are run as a series of command-line operations, commonly as part of a build script.

## Deploying Node.js Application 

Usually, Node applications are deployed in one of the following ways:

 - packaged into containers, then deploying the container to a cloud host such as AWS, Azure, or Google Cloud
 - deployed directly to Apache or nginx if using the web framework [Express](https://expressjs.com/)
 - published to Platform-as-a-Service providers such as [Heroku](https://heroku.com) or [Azure Web Apps](https://azure.microsoft.com/en-us/products/app-service/web)

The publish process involves little more than copying files. In BuildMaster's case, using the `Deploy-Artifact` operation is the simplest method to deploy Node.js applications.

## Package Management with npm 

Most useful Node.js features are provided by library packages provided by [npm](https://www.npmjs.com/). 
Private registries (such as [ProGet](https://inedo.com/proget)) can also be used for an additional layer of reliability and security.

## Automation with BuildMaster

::: (INFO)
**See it live!** The *ProfitCalc Node.js Sample* is available when creating a new application in your own instance of BuildMaster.
:::

Because Node.js applications are built primarily using the npm CLI.  BuildMaster has added the most common npm commands and includs a generic `npm::Execute-Command` that covers the remaining commands.  

### Prerequisites
BuildMaster's npm operations require [nodes.js](https://nodejs.org) to be installed on the server that is executing the npm operation and the Node extension to be installed in BuildMaster.

### Building a node.js application:
The general process for building a node application:
 - Get source code from the source control repository
 - Install npm dependencies using `npm install`
 - Lint and transpile source code using `npm Build`, `npm Publish` or `npm run <Script Name>`
 - Capture artifact (if not using `npm publish`)

A rough example plan of this would be:
```
Git::Checkout-Code();

npm::Set-ProjectVersion(
    Version: $ReleaseNumber-ci.$BuildNumber
);

npm::Install;

npm::Build;

Create-Artifact
(
    From: dist
);
```

### ProGet & Other Private npm Feed Integration
BuildMaster has added support for ProGet & other private npm registry feeds for the npm operations.  When setting a `PackageSource` on any of the npm operations, a local `.npmrc` will be created in the working directory and will be passed via the `--userconfig` command line parameter to npm.  If you need a more custom the `.npmrc` file to reference, then you  can specify the path to that file using the `NpmrcPath`.  *Note: the `PackageSource` and `NpmrcPath` cannot be used at the same time, the `PackageSource` parameter will take precedence.*

An example install operation connecting to a ProGet feed:
```
npm::Install
(
    PackageSource: feed::approved-npm
);
```

::: (Info) (Note:)
When using a self-signed certificate with your private npm registry, the npm CLI will throw invalid certificate errors.  When using the `PackageSource` parameter, you can set `AllowSelfSignedCertificate` to `true` and these certificate validation errors will be bypassed.
:::
