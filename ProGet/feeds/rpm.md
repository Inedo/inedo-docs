---
title: RPM (yum)
subtitle: RPM Feeds in ProGet
sequence: 500
keywords: proget,feeds,rpm,yum
show-headings-in-nav: true
---

[RPM Package Manager](https://rpm.org/) (RPM, originally Red Hat Package Manager), is a free open-source package management system used by many Linux distributions including Red Hat and CentOS. As of ProGet v5.2.25, RPM packages can be hosted and installed from ProGet.

## Prerequisite Configuration

### Client Configuration

In order to actually install packages from ProGet, each client must perform the following steps:

##### 1. Add a .repo file to /etc/yum.repos.d
Yum stores its repository configuration in `.repo` files located in `/etc/yum.repos.d/`. To register a ProGet feed as a repository, create a new `<RepoName>.repo` file in that directory with content:
```
[<RepoName>]
name=<RepoName>
baseurl=http://<ProGet>/rpm/<FeedName>/
gpgcheck=0
enabled=1
```
Note that `<RepoName>` can be anything, but we recommend using the ProGet feed name for clarity.

##### 2. Verify yum configuration

To verify that yum is able to download package metadata, execute:
```
yum repolist all
```
If the configuration is correct, this will list all repos (including the ProGet feed registered in step 1).

## Common Tasks

### Installing Packages

RPM packages are installed using yum. To install a package hosted by ProGet, use the command: 

```
yum install <package-name>
```

### Creating Packages

To learn how to create an RPM package, visit a resource such as [a simplified guide to creating your first RPM](https://developers.redhat.com/blog/2019/03/18/rpm-packaging-guide-creating-rpm/) on the Red Hat Developer Blog.

### Publishing Packages

`yum` does not support uploading a package, so ProGet offers a few alternative methods to publish RPM packages:

#### Upload from ProGet Web Application

On the feed overview page, select "Add Package" and "Upload Package" to upload a `.rpm` file directly from your browser.

#### Publish via HTTP

To push an RPM package via HTTP, issue a PUT or POST request with the package file as the content to: `http://{proget-server}/rpm/{feed-name}/`

##### Examples:

###### cURL:

```
curl http://{proget-server}/rpm/{feed-name}/ --user <user>:<password> --upload-file {my-package}.rpm
```

###### wget:

```
wget http://{proget-server}/rpm/{feed-name}/ --http-user <user> --http-password <password> --method POST --body-file {my-package}.rpm
```

###### PowerShell:

```
Invoke-WebRequest http://{proget-server}/rpm/{feed-name}/ -Credential [System.Net.NetworkCredential]::new('<user>', '<password>') -Method PUT -InFile {my-package}.rpm
```


#### Technical Limitations {#limitations}

 - Connectors are not supported
 - GPG signing is currently not supported, but is on our internal roadmap.
   - If you would like us to prioritize this feature for an upcoming release, please [open a ticket](https://my.inedo.com/tickets/new) or [contact Inedo](https://inedo.com/support).
