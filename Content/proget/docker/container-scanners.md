---
title: "Package/Container Scanners"
order: 6
---

ProGet can connect to an external resource like Otter or Kubernetes to collect and display where your Docker container images are being used.

## Configuring a Container Scanners

To begin displaying container usage, you must first configure a container scanner; this is done on the Manage Feed > Usage & Scanning page.

Container scanners are an extensible component, which means you can [create your own extension](/docs/proget/administration/extensions). 

## Configuring Otter

Otter can be configured to collect container usage from your servers. See Server Packages in the Otter documentation for instructions on configuring this feature.

Once Otter has been configured to collect container data, you will need to create an API key that allows access to the *Package/Container Usage API*.

Once this key is created, you can add the Otter scanner to ProGet.

## Configuring Kubernetes

ProGet can scan your Kubernetes cluster and determine which Docker container images stored in ProGet are currently in use. This is done by connecting to Kubernetes via the Kubernetes API.

### Kubernetes API

ProGet uses the Kubernetes web API to check for running images in your infrastructure. The Kubernetes API is not always enabled by default. To enable access to the Kubernetes API, you must run the following command.

```
kubectl proxy --port=8080
```

The port specifies which port the API is listening to requests on.

### Kubernetes API Authentication

There are many ways to set up authentication. You can find more information on authorizing the Kubernetes API in the [authorization documentation](https://kubernetes.io/docs/reference/access-authn-authz/authentication/). Currently ProGet supports Basic Authentication and Bearer Tokens.

### Creating The Kubernetes Scanner

For ProGet to scan the Kubernetes cluster, you must first install the Kubernetes extension at _Administration > Extensions_.

When you create your source, you will need to enter the Kubernetes API URL and select which type of authentication is required to connect to the API. Please note that the _Username_ and _Password_ fields are only used if you select _Basic Authentication_. If you select _Bearer Token_ authentication type, you must specify a _Bearer Token_.

### Adding the Kubernetes Scanner to a Feed

A Kubernetes scanner can be used for one or more container registries. To add the scanner to your registry, navigate to the feed to which you want to add the scanner. Then click the _Manage Feed_ button in the upper right corner and click the _Usage_ tab. You can then add the scanner to the registry by clicking the _Add_ link at the top of the _Feed Container Usage Scanner Sources_ table.

## Viewing Container Usage

After data collection, container images in ProGet now have a *Use* tab. Click this tab to see a list of all servers using the image, along with other relevant information such as container name and status.