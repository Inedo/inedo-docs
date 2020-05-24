---
title: Package/Container Scanners
sequence: 700
keywords: proget
subtitle: Package/Container Scanners
show-headings-in-nav: true
---

ProGet can connect to an external resource such as Otter or Kubernetes to collect and display how packages and Docker container images are used across your infrastructure.

## Package/Container Scanners

To begin displaying package/container usage, you must first configure a package/container scanner. Go to the *Administration->Package/Container Scanners* page, and click the *Create New Scanner* button.

To add the new scanner source to a feed.  Navigate to the feed you would like to add the scanner to.  Then, click the _Manage Feed_ button in the upper right corner and then click the _Usage_ tab.  You can then add then add the scanner to the feed by clicking the _add_ link at the top of the _Feed Package/Container Usage Scanner Sources_ table. 

### Configuring Otter {#otter data-title="Configuring Otter"}

Otter can be configured to collect both package and container usage from your servers. See [server packages] in the Otter documentation for instructions on configuring this feature.

Once Otter has been configured to collect package and/or container data, you will need to create an API key that allows access to the *Package/Container Usage API*. Once this key has been created, you are ready to add the Otter scanner to ProGet.

### Configuring Kubernetes {#kubernetes data-title="Configuring Kubernetes"}

ProGet can scan your Kubernetes cluster and identify which Docker container images stored in ProGet are currently in use. It does this by connecting to Kubernetes via the Kubernetes API.

#### Kubernetes API

ProGet uses the Kubernetes Web API to scan for running images on your infrastructure.  The Kubernetes API is not always enabled by default.  In order to enable access to the Kubernetes API, you will need to run the following command.

```
kubectl proxy --port=8080
```

The port specifies which port the API is listening to requests on.

##### Kubernetes API Authentication

There are many ways to setup authentication.  You can see more information on authorizing the Kubernetes API in the [authorization documentation](https://kubernetes.io/docs/reference/access-authn-authz/authentication/). Currently ProGet supports Basic Authentication and Bearer Tokens.

#### Creating The Kubernetes Scanner

To enable ProGet to scan the Kubernetes cluster, you must first install the Kubernetes extension under _Administration->Extensions_.  Once the extension is installed, you will need to create a Kubernetes source under *Administration->Package/Container Scanners*.

When you create your source, you will need to enter the Kubernetes API URL and select what type of authentication is required to connect to the API.  Please note that the _User Name_ and _Password_ fields are only used when selecting _Basic Authentication_.  If you select the _Bearer Token_ authentication type, you will need to supply a _Bearer Token_.

#### Adding The Kubernetes Scanner To A Feed

A Kubernetes Scanner can be used for one or more container registries.  To add the scanner to your registry, navigate to the feed you would like to add the scanner to.  Then, click the _Manage Feed_ button in the upper right corner and then click the _Usage_ tab.  You can then add then add the scanner to the registry by clicking the _add_ link at the top of the _Feed Container Usage Scanner Sources_ table.

## Viewing Container Usage

Once data has been collected, container images in ProGet will now have a *Usage* tab. Click this tab to display a list of every server that is using the image, along with other relevant information such as container name and status.