---
title: "Deprecated: WhiteSourcePackage Access Rules"
nav-title: "Package Access Rules"
order: 3
---

:::(error) (WhiteSource & Package Access Rules Were  Discontinued in ProGet 2024)
This article is for users who have configured Whitesource integration or created their own Package Access Rules and wish to explore alternative options in ProGet 2024. 
:::

In the many years since we launched ProGet's WhiteSource integration, both products have evolved quite a bit: 
* Whitesource became Mend.io and no longer supports integrations created by third-parties like our integration
* ProGet introduced a whole host of license management, vulnerability detection, and compliance detection that [are generally rated better than Mend.io's offering](https://inedo.com/proget/vs-mend-whitesource)

In that same time, we couldn't find any other use cases for the "Package Access Rules" extensibility point that we added to ProGet. As such, both are discontinued in ProGet 2024. If you'd like to view our old documentation on how to integrate the producs, check out [How to Integrate ProGet with WhiteSource (archive.org)](https://web.archive.org/web/20230927161336/https://docs.inedo.com/docs/proget-compliance-whitesource).

## WhiteSource (Mend.io) Alternatives

The primary use case for using WhiteSource with ProGet was to define policies that would restrict access to specific packages based on license type and vulnerability severity. Compared to ProGet 2023 and earlier versions, WhiteSource's project- and organization-level projects were more robust and offered more flexibility than ProGet's vunlerability and licensing blocking rules.

However, the [Policies & Compliance Rules](/docs/buildmaster/administration/retention-policies)  feature introduced ProGet 2024 flipped the script. Now, ProGet's policies are more robust and offer more flexibility than WhiteSource policies. 

In addition, instead of getting a "Package Blocked by WhiteSource" message, and then having to hunt down and debug the relevant WhiteSource policies, you can get a clear message in ProGet showing exactly why the package is blocked:

![](/resources/docs/proget-policies-renalyze-package.png){height="" width=""}

ProGet's policies also work with remote packages, while WhiteSource was limited to local/cached packages - which meant you'd have to download a package to determine if it's compliant.


## Package Access Rule Alternatives

To integrate with WhiteSource, we created an extensible component called a `PackageAccessRule` that could be associated with a feed. It was very simple:
* Input: Package Identifier (i.e. Name, Version)
* Output: Blocked (Yes/No) and Reason (Text)

With ProGet 2024's policy-based model, this component no longer makes sense. Packages are no longer "blocked", but are Compliant, Noncompliant, or Warn - and a feed then determines if Noncompliant packages are blocked from being downloaded.

In addition, with WhiteSource being discontinued, there's no use case for this extensibility feature. We're not aware of any users who created an extension with a Package Access Rule.

That being said, we could definitely see some utility in creating a custom compliance rule, that would work similar to a `PackageAccessRule`, but would instead of Blocked would output Compliant, Noncompliant, or Warn. However, before adding that, we'd like to hear from users on what sort of custom rules they would want to develop.

If you're interested in this, just [contact us via the support channel](https://inedo.com/support) and we'll go from there.