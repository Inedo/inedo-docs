---
title: "License Restrictions"
order: 5
---

ProGet is licensed *per instance* (i.e. installation) on an annual basis. There are [multiple editions of ProGet available](https://inedo.com/proget/pricing), and depending on which edition you are using there will be different features and license restrictions.

## ProGet Editions & License Keys 

The license key you enter in ProGet controls when the license will expire and which set of features  (i.e., the edition) will be enabled.  See [License Key Management and Activation](/docs/myinedo/activating-a-license-key) to learn more.

There are three main editions of ProGet:

* **ProGet Enterprise** is full-featured and includes enterprise-level security, protection, and compliance features; it can also be installed as a high-availability server cluster
* **ProGet Basic** is designed for small teams with basic security needs; it offers basic protections against vulnerabilities, unwanted licenses, and other noncompliant packages
* **ProGet Free** is a great way to get started with packages and containers, and includes more than enough features for personal usage. It’s easy to upgrade when you’re ready

There is also a **ProGet Trial** edition, which allows you to select which feature set to try  (i.e., Basic or Enterprise) within ProGet. There are also two *specialized editions* ([ProGet ISV Edition](https://inedo.com/proget/isv) and [ProGet Edge Computing Edition](https://inedo.com/proget/edge)), but those are beyond the scope of this article.

## Features Restrictions by Edition

ProGet has a *lot* of features; all editions support unlimited users, unlimited feeds (i.e. NuGet, PowerShell, Docker, Ruby Gems, VSIX, Chocolatey, npm, Bower, Maven, PyPI, Debian, Helm, Apk, Conda, etc. ), unlimited packages, and so on.

Instead of showing all features available in all additions (including ProGet Free), the following lists will focus on which features are restricted in each edition.

### Security Restrictions
| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| Feed-level Security |❌|✔|✔
| AD/LDAP Security |❌|✔|✔
| Personal API Keys |❌|✔|✔
| Secure API Configuration |❌|❌|✔
| SAML Authentication |❌|❌|✔

### Vulnerability Restrictions
| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| Detailed Vulnerability Information |❌|✔|✔
| Vulnerability Breakdown & Reporting |❌|✔|✔
| Vulnerability Assessments |❌|☑|✔
| Container Vulnerability Scanning  |❌|❌|✔

ProGet Basic only supports basic vulnerability assessments (i.e. not scoped to a project or policy).

### SCA & Build Restrictions
 track the open source and third-party components (packages) your organization uses and apply Package Policies & Compliance Rules to identify vulnerabilities, unwanted license, etc.

| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| See components & licenses used by builds  |❌|✔|✔
| Identify vulnerabilities in projects  |❌|✔|✔
| Generate SBOM for builds |❌|✔|✔
| Track dependencies across builds |❌|☑|✔
| Generate license attribution reports  |❌|❌|✔
| Advanced project compliance  |❌|❌|✔
| OSS Metadata Updating & Caching  |❌|❌|✔

ProGet Basic only supports 1000 active builds and projects may not be associated with feeds or policies.

### Compliance Restrictions
| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| Connector Filtering  |❌|✔|✔
| Package Promotion  |❌|✔|✔
| Semantic Container Tags  |❌|✔|✔
| Block Noncompliant Packages  |❌|✔|✔
| Package Compliance Policies  |❌|☑|✔

ProGet Basic only supports basic package compliance policies (i.e. only one shared policy, three rule exceptions, and ten compliance API actions per hour)

### Notifications & Webhook Restrictions
| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| Compliance & Vulnerability Alerts |❌|✔|✔
| Custom Webhooks |❌|✔|✔
| Notifications (Email, Slack, Teams) |❌|❌|✔

### Installation & Configuration Restrictions
| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| Connector Metadata Caching  |❌|✔|✔
| Multi-instance Connectors  |❌|✔|✔
| Multi-site Replication  |❌|❌|✔
| Cluster-based Installation |❌|❌|✔

### Package & Storage Restrictions
| Feature | Free | Basic | Enterprise
| -- | -- | -- | --
| Retention Rules  |❌*|✔|✔
| Package Statistics/Metrics  |❌|✔|✔
| Cloud Storage  |❌|✔|✔
| Package Statistics/Metrics  |❌|✔|✔

ProGet Free is also limited to 10/deletes per hour when using the API.

##  Licenses for Non-production / Testing Environments

Many organizations will want to configure multiple instances of ProGet in different environments, such as a testing instance and a  production instance. 

:::(Info)
Because ProGet is licensed *per instance* (i.e. installation), you will need a *separate* license if you wish to maintain a production and non-production instances of ProGet.
:::

However, you don't need to use the same type of license in each environment. For example, consider a scenario where you have ProGet Enterprise configured as a high-availability sever cluster in production:

### 🌟 Best: Enterprise Test/DR/Training Environment
This is the most expensive, as it will involve multiple servers/hosts plus a ProGet Enterprise license. But it’s also the most common, since running a High-availability environment is already fairly expensive and this is typically “less than twice the cost” when all is said and done.

However, it lets you test your high-availability scenarios:
• Removing nodes and making sure load-balancer detects it
• Dynamic/ephemeral infrastructure scripts
• Cluster-based installation/upgrading
• Load-testing

Another advantage is that this environment can double-up as a [Disaster Recovery environment by using replication](/docs/proget/replication-feed-mirroring/proget-howto-utilize-feed-replication-for-disaster-recovery).

Having feeds with a “production level” of package also makes upgrade testing a little more reliable, since it’s a more accurate representation of production.

This kind of environment also triples-up as a training environment.

### ⭐ Alternative: Basic Edition Test/Training Environment
This is a good choice to test ProGet software itself, as well as doing training on features.

Production-level data is a challenge, but you can always “import packages” from production as needed, etc.

### 🆗 Acceptable: Free Edition Test Environment
Similar to a Basic environment, this can serve as Test/Training, but you won’t be able to test out paid features (Security, SCA, Vulnerabilities, etc). The core functionality will still work though.


## ProGet 2023 and Earlier
In ProGet 2024, we refactored a lot of license-checking code and added several new features for ProGet Enterprise.

Our intent was not to "take away" features from existing ProGet Basic users in ProGet 2024, but it's possible that we made a mistake and accidently restricted a feature. If you feel we got them wrong, [please let us know](https://inedo.com/support).

For ProGet 2023 Free Edition, we added the rate limit for deleting packages using the new packages API. 

## License Violations in ProGet Free Edition

Although commercial use of ProGet Free Edition is permitted, you may not connect a ProGet Free instances to another instance of ProGet. This means that you can't:
* Create a connector to another ProGet instance (paid or otherwise)
* Have another ProGet instance (paid or otherwise) connect to ProGet Free 

However, you are still allowed to connect ProGet Free Edition to:
* public repositories/registries such as nuget.org, registry.npmjs.org, repo.maven.apache.org, etc.
* feeds hosted on a [ProGet ISV Edition](https://inedo.com/proget/isv), such as [Inedo's Public ProGet instance](https://proget.inedo.com)
* other feeds in the *same instance* of the free edition (i.e. self-connectors)

If a connector limitation violation is detected, a warning banner will be displayed in the UI of the target ProGet instance listing the instances in violation of the license. To remedy this, any connectors to free editions in the target ProGet instance (free or paid) must be removed, and/or the administrator of an offending free instance must be notified in order to remove the connector to the target instance. 

Once all connectors to the free edition are removed from the target instance and/or all connectors to the target instance are removed from the offending instance(s), you can clear recorded violations and remove the warning. Visit the `Admin` > `License Key & Activation` page within ProGet, edit and re-save the license key. Note, if the offending instance still has a connector, the warning will eventually reappear.

## Licensing Examples & Clarifications

The following examples help to clarify the rules for ProGet licensing:
- <span style="color: red;">🚫 Prohibited</span>- Chad has one ProGet Enterprise license, but installs it in two locations to do replication
- <span style="color: green;">✔ Allowed</span> - 2 distinct business units at Acme Co. both have ProGet Free, both have developers, but do not connect to or reference packages from the other instance
- <span style="color: green;">✔ Allowed</span> - Alana is attempting to code a custom extension for Acme Co. and wants to test the functionality on her laptop before configuring it on Acme Co.'s ProGet installation
- <span style="color: red;">🚫 Prohibited</span>- Chad has a ProGet Enterprise cluster, but "splits" them by configuring 2 servers in a testing environment and 3 in production
- <span style="color: green;">✔ Allowed</span> - Alana has purchased ProGet Enterprise for production and ProGet Basic to test a single-server upgrade in her testing environment
