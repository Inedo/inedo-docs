---
title: "Viewing Upgrade Guidance"
order: 9
---

When updating your current software version, you'll often need to seek upgrade guidance, information on the risks involved, and if the in-software notification of a new release means that upgrading your Inedo product is required.

Upgrading your Inedo product is an easy process, and our upgrade guidance can help get you started. You can view upgrade guidance on [MyInedo](https://my.inedo.com/log-in) to help you find the recommended route for your product version. This is useful when upgrading from a much older version where the upgrade path may not be as clear.

## Remaining on Older Product Versions

Inedo products do not update themselves automatically, so it's common to still be using a much older version of our software, years after release. There are many reasons for this:

* To maintain the stability of your builds and system compatibility.
* Resource, testing, and time constraints preventing your upgrade.
* Potential of breaking changes being introduced (e.g., log-in issues documented after [upgrading to ProGet version 22.0.2](https://forums.inedo.com/topic/3577/proget-login-not-working-anymore-after-upgrading-to-22-0-2) when using a reserve proxy).
* Simply being unaware of new version / patch releases and the benefits of upgrading.

## Offline Inedo Products

If you're using an offline version of an Inedo product you will not be notified of the latest version releases. Instead, visit your MyInedo page and select "Upgrade Guidance and Change Notes" in the ["Downloads"](https://my.inedo.com/downloads) tab to view our recommended upgrade process.

![Downloads](/resources/docs/myinedo-viewingupgradeguidance-downloads.png){height="" width="50%"}

![Upgrade_Guidance_Changle_Notes](/resources/docs/myinedo-viewingupgradeguidance-upgradeguidanceandchangenotes.png){height="" width="50%"}

Since offline versions of Inedo products don’t receive notifications on new version releases it means that bug fixes and important security patches may go unnoticed. We recommend visiting your MyInedo page regularly and viewing our upgrade guidance and change logs to make sure that your software is secure and optimized.

## Why should you View Upgrade Guidance?

When starting the upgrade process yourself, e.g., you have decided to upgrade to the most recent BuildMaster version to keep your software up to date, there are two main factors to consider:

* **Upgrade Path**: Depending on your current software version it might not be possible to directly upgrade to your intended version. By accessing upgrade guidance on MyInedo you can view our recommended upgrade paths and alternative upgrade paths, view the steps needed to upgrade, and save time in the process.
* **Change Notes**: Even if the changes between the two software versions you are upgrading between are limited in size and scope, it's important to look over them carefully to acknowledge and mitigate potential risks. Newer versions could bring plugin compatibility issues, affect user permissions, or introduce API breaking changes.

Therefore, it's recommended to carefully read our provided upgrade guidance to make the transition between versions as smooth as possible.

## Version Specific Upgrade Guidance

We've already put together guides featuring the recommended upgrade paths of each product, focusing on major releases:

* [ProGet Upgrade Guide](/docs/proget-upgrade-guide)
* [BuildMaster Upgrade Guide](/docs/buildmaster-upgrading)
* [Otter Upgrade Guide](/docs/otter-upgrading)

However, if you are looking to upgrade from a maintenance release or from a much older version, viewing our recommended upgrade path will get you on the right track faster.

## How to View Upgrade Guidance

[Inedo products can be upgraded or downgraded in the Inedo Hub](/docs/installation/windows/inedo-hub-upgrade-downgrade), but there are times a direct upgrade is not possible, and it is recommended that you carefully read the change notes between software versions provided in our upgrade guidance.

:::(Info)(Backing Up Your Data)
Before upgrading your Inedo Product, we recommend creating a backup so you can easily restore things if needed.
:::

On your MyInedo page, choose your Inedo product, current version, and chosen upgrade version, then select "Get Upgrade Guidance".

![Upgrade_Guidance](/resources/docs/myinedo-viewingupgradeguidance-getupgradeguidance.png){height="" width="50%"}

## Recommended Upgrade Path

You can see our recommended steps for upgrading, tailored specifically to your version transition, providing you with a careful assessment of risk, and linking to Inedo Documentation pages with further instructional detail on upgrading in your situation.

Upgrading can be a straightforward process, such as moving from ProGet version 2024.25 to 2024.9:

![Upgrade_Recommended](/resources/docs/myinedo-viewingupgradeguidance-upgraderecommended.png){height="" width="50%"}

In this case, follow the steps on the [Upgrading your Inedo Product](/docs.inedo.com/docs/installation/upgrading) page to upgrade.

However, direct upgrades are not always possible, especially when upgrading from older versions. In these cases, multiple steps may be required. Refer to our guidance for detailed instructions:

![Upgrade_Not_Possible](/resources/docs/myinedo-viewingupgradeguidance-directupgradenotpossible.png){height="" width="50%"}

In this case, the first step requires upgrading to a different version of ProGet. Visit the [Upgrading to ProGet 5.2](/docs.inedo.com/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2) page for detailed instructions on upgrading. 

We may recommend upgrading to the latest version, for security and stability reasons, particularly when transitioning between two older versions of software:

![Upgrade_Not_Recommended](/resources/docs/myinedo-viewingupgradeguidance-upgradenotrecommended.png){height="" width="50%"}

Keeping your software up to date is important for security and compatibility. Always review our guidance and change notes to assess potential risks before upgrading. 

## Change Notes

Despite a risk assessment being provided alongside your recommended upgrade path, we recommend noting the changes in each maintenance release up to your intended version, visible on the same page.

![Change_Notes](/resources/docs/myinedo-viewingupgradeguidance-changelogsredbox.png){height="" width="50%"}

Use these change notes to determine if there are any fixes, implemented features, or compatibility issues, that could result in a rollback.

Visit an issue number page on our public issue tracker for further insight into how these changes may conflict with your current operating environment and needs after an upgrade, and to see the progress of impending fix releases.

### Viewing Change Logs

There may be situations where you want to view change notes in detail, to pinpoint the source of an issue after an upgrade, or to search for the version in which a patch or feature you were anticipating was released. The easiest way to do so would be to visit our [Detailed Change Logs page](/docs.inedo.com/docs/myinedo/viewing-change-logs)
