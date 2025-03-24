---
title: Inedo Documentation
masterpage: HomePage.html
---

<style>
a {
   color: #025291
}

ul {
   padding: 0;   
}

ul li {
   list-style: none;   
   margin-bottom: 0.75rem;
}

h1 {
    margin-bottom: 14px;
}

h3 {
   margin-top: 4rem;
}

.icon {
  background-size: 1.8rem;
  padding-left: 2.2rem;
}

.content-container .content {
   max-width: 1000px;
}

#header .content {
    max-width: 1000px;
}

#sub-footer.content-container {
   border-top: solid 1px #CCC;
}

#inedo-links.content-container .content {
   max-width: 1000px;
   margin-left: 0;
}

#footer .content {
   padding-left: 0.75rem;
   padding-right: 0.75rem;
}

.home-page-search-box {
   height: 7rem;
}

.product-blocks {
   display: flex;
   flex-wrap: no-wrap;
}

.product-blocks > .block  h3{
    margin-top: 1.25rem;
}

.product-blocks > .block {
   flex-grow: 1;
   flex-basis: 30%;
   max-width: 30%;
   padding-right: 0.75rem;
}

.three-column {
   display: flex;
   flex-wrap: wrap;
}

.three-column.last {
    margin-bottom: 4rem;
}

.three-column h3 {
   border-bottom: solid 1px #CCC;
   flex-basis: 100%;
}

.three-column ul {
   display: flex;
   flex-wrap: wrap;
}

.three-column ul li {
   padding-right: 0.75rem;
   flex-grow: 1;
   flex-basis: 30%;
   max-width: 30%;
}

.two-column {
   display: flex;
   flex-wrap: wrap;
   justify-content: center;
   gap: 0.75rem;
   margin-bottom: 1.25rem;
}

.two-column h3 {
   flex-basis: 100%;
}

.two-column > a {
   border-top: solid 1px #ccc;
   flex-basis: 48%;
   max-width: 48%;
   padding: 5px;
   
}

.two-column > a:hover {
   color: #025291;
   text-decoration: none;
   background-color: #ccc;
}
.two-column > a:hover p {
   color: #444;
   opacity: 1;
}

.two-column > a h5 {
   font-size: 1rem;
   margin-bottom: 0;
}

.two-column > a p {
   color: #444;
   font-size: 0.85rem;
   opacity: 0.8;
}

.two-column > a p:last-child {
   margin-bottom: 0px;
}


@media (max-width: 50rem) {
   .content-container .content.home-page {
      margin-left: 1.25rem;
      margin-right: 1.25rem;
   }
   
   .product-blocks {
      flex-wrap: wrap;
      justify-content: center;
   }
   
   .product-blocks > .block {
      max-width: 48%;
      flex-basis: 48%;
   }
   
   .three-column {
      justify-content: flex-start;
   }
   
   .three-column h3 {      
      max-width: 100%;
   }
   
   .three-column ul {
      justify-content: flex-start;
   }
   .three-column ul li {
      max-width: 48%;
      flex-basis: 48%;
   }
   
   .two-column {
      justify-content: flex-start;
   }
   
   .two-column > a {
      flex-basis: 100%;
      max-width: initial;
   }
}

@media (max-width: 30rem) {
   .content-container .content.home-page {
      margin-left: 1.25rem;
      margin-right: 1.25rem;
   }

   .product-blocks {
      display: block;
      gap: 0;
      flex-direction: column;
      justify-content: flex-start;
   }
   
   .product-blocks > .block {
      max-width: initial;
      flex-basis: initial;
      flex-grow: initial;
   }
   
   .three-column {
      display: block;
      margin: auto;
   }
   
   .three-column h3 {
      max-width: initial;
   }
   
   .three-column ul {
      display: block;
      margin: auto;
   }
   
   .three-column ul li {
      max-width: initial;
      flex-basis: initial;
   }
}

</style>

## Welcome to Inedo Docs!
Inedo Docs is the documentation portal for our software products (ProGet, BuildMaster, and Otter), related tools, and our [MyInedo](https://my.inedo.com){ target=_blank} user portal. 

<div class="product-blocks"><div class="block">

### **ProGet** {.icon .proget}
- [Getting Started With ProGet](/docs/proget/overview)
- [Packages in ProGet](/docs/proget/packages/what-is-a-package)
- [How to Approve and Promote Open-source Packages in ProGet](/docs/proget/packages/package-promotion/proget-howto-promote-packages)
      
</div><div class="block">

### **BuildMaster** {.icon .buildmaster}
- [Getting Started With BuildMaster](/docs/buildmaster/overview)
- [What is a "Build" in BuildMaster?](/docs/buildmaster/builds-continuous-integration/buildmaster-builds)
- [Build Scripts & Templates](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts)
      
</div><div class="block">

### **Otter** {.icon .otter}
- [Getting Started With Otter](/docs/otter/overview)
- [Jobs & Templates](/docs/otter/orchestration-server-automation/otter-jobs-templates)
- [What is a "server" in Otter](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter)
      
</div></div>


<div class="three-column">

### **Installation** {.icon .installation}

- [ProGet Installation Guide](/docs/proget/installation/installation-guide)
- [BuildMaster Installation Guide](/docs/buildmaster/installation-maintenance/buildmaster-installation-guide)
- [Otter Installation Guide](/docs/otter/installation-upgrading/otter-installation-guide)
- [Windows Installation (Inedo Hub)](/docs/installation/windows/inedo-hub-installation-guide)
- [Linux Installation (Docker)](/docs/installation/linux/docker-guide)
- [Configuring IIS Roles & Features](/docs/installation/installing-on-iis/various-iis-configuring-iis-roles-and-features)
- [SQL Server & Inedo Products](/docs/installation/sql-server)
- [HTTPS Support on Windows](/docs/installation/installing-on-iis/installation-windows-https-support)
- [HTTPS Support on Linux](/docs/installation/linux/https-support)

</div>

<div class="two-column">

### **Popular Topics** {.icon .popular}

<a href="/docs/proget-upgrade-2024" class="item">

##### Upgrading to ProGet 2024
ProGet 2024 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

</a>
<a href="/docs/buildmaster-upgrade-2023" class="item">

##### Upgrading to BuildMaster 2023
BuildMaster 2023 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

</a>
<a href="/docs/otter-upgrade-2023" class="item">

##### Upgrading to Otter 2023
Otter 2023 is a major upgrade, and this article provides information about what changed, the impact to your instance, and how to mitigate risk during upgrade.

</a>
<a href="/docs/proget/api/pgutil" class="item">

##### Getting Started with pgutil
`pgutil` is an open-source (github.com), cross-platform command line tool that provides a variety of commands to upload/download packages, manage feeds, audit package compliance, assess vulnerabilities, etc. 

</a>
<a href="/docs/installation/security-ldap-active-directory" class="item">

##### LDAP/AD Integration
Inedo products come with built-in users and groups, but it can also integrate with an external directory service like Active Directory and other LDAP-based services. This allows you to use the organization's existing users, logins, and groups to secure the product and define the tasks users are permitted to do. 

</a>
<a href="/docs/installation/saml-authentication/various-saml-overview" class="item">

##### SAML Authentication
SAML-based single sign-on is an authentication mechanism that uses a third-party identity provider to verify the identity of user, and relay user metadata back into the Inedo product, effectively replacing the Inedo product login page, with that of the identity provider's, or removes it altogether if a user is already signed-in to the identity provider.

</a>
<a href="/docs/installation/high-availability-load-balancing/high-availability-load-balancing" class="item">

##### High Availability & Load Balancing
The distributed architecture of ProGet, BuildMaster, and Otter allows you to configure an instance over any number of servers for both load-balancing and failover purposes. By doing so, any single server can take over the cluster in the event of server failure and outage. This can improve your Inedo product's performance in high-traffic environments.

</a>
<a href="/docs/installation/backing-up-restoring" class="item">

##### Backing up & Restoring
Your Inedo products should be backed up frequently.

</a>

</div>

<div class="three-column">

### **Inedo Hub** {.icon .inedo-hub}

- [What is the Inedo Hub?](/docs/installation/windows/desktophub-overview)
- [Configuring & Maintaining Inedo Products](/docs/installation/windows/inedohub-configure-products)
- [Offline Installation](/docs/installation/windows/desktophub-offline)
- [HOWTO: Install on Windows](/docs/installation/windows/inedo-hub-installation-guide)
- [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/inedo-hub-upgrade-downgrade)
- [HOWTO: Install Pre-release Product Versions](/docs/installation/windows/howto-install-prerelease-product-versions)

</div>

<div class="three-column last">

### **Inedo Agent** {.icon .inedo-agent}

- [What is the Inedo Agent?](/docs/inedo-agent/inedoagent-overview)
- [Installation Guide](/docs/inedo-agent/inedoagent-installation-installation-guide)
- [Upgrade Guide](/docs/inedo-agent/inedoagent-installation-installation-guide/inedoagent-installation-upgrading)
- [Configuring the Inedo Agent](/docs/inedo-agent/maintenance-configuration/inedoagent-configuration-configuration-file)
- [Downloads & Release Notes](/docs/inedo-agent/inedoagent-versions)
- [Troubleshooting](/docs/inedo-agent/maintenance-configuration/inedoagent-configuration-troubleshooting)

</div>