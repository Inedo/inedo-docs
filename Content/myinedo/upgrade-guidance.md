---
title: "Viewing Upgrade Guidance"
order: 9
---

<!-- I think that after re-reading the Upgrading your Inedo Product page (https://docs.inedo.com/docs/installation/upgrading) that either this whole page could be condensed and added to that page or link to it could be placed there. -->

<!-- As for cross linking, maybe it makes sense to link to this page on the following other pages:

Installation and Maintenance:

What is the Inedo Hub? Probably link to it in the Upgrades section (https://docs.inedo.com/docs/installation/windows/desktophub-overview)

HOWTO: Upgrade or Downgrade with the Inedo Hub, which already talks about when to upgrade (https://docs.inedo.com/docs/installation/windows/inedo-hub-upgrade-downgrade)

Legacy Installer - Only becuase it mentions upgrading and I know that people using offline products won't be notified to upgrade so would people using the legacy installer also need assistance? (https://docs.inedo.com/docs/installation/windows/installation-legacy-traditional-installer)

Manually Upgrade an Existing Installation - Although if someone is doing this they probably already know exactly what version to upgrade to and have probably investigated the change logs already etc. because it seems like a much more involved process (https://docs.inedo.com/docs/installation/manual-installation/installation-manually-upgrade)

MyInedo:

What is MyInedo? - In the bullet points on this page that describe the features of MyInedo include 'View Upgrade Guidance' and link to this page (https://docs.inedo.com/docs/myinedo/overview)

License Keys and Activation - In the Re-activation of a Product section at the bottom of the page it mentions major upgrades being a possible cause for needing to re-activate, I don't know if this should be mentioned as a possible risk on Viewing Upgrade Guidance or if the pages should be linked? (https://docs.inedo.com/docs/myinedo/activating-a-license-key)

I wouldn't link to those pages from this Viewing Upgrade Guidance page, becuase I don't think that this needs to act as a kind of contents page for upgrading, just as help for people how get stuck and need some guidance.
-->





<!-- I have changed the narrative of the first paragraphs to target people who will be already be upgrading / trying to upgrade, and may be encountering difficulties. I have also removed the references to version specific upgrading as it already exists on the Inedo Docs. -->

If you're upgrading your Inedo product and don't know where to start or need a hand, you can view upgrade guidance on [MyInedo](https://my.inedo.com/log-in), for information on upgrade paths, the risks involved, and if the in-software notification of a new release means that upgrading your Inedo product is required.

Upgrading your Inedo product is an easy process, and our upgrade guidance can help get you started in determining the recommended route for your intended product version, which is useful when upgrading from an older product version where the upgrade path may not be as clear.

## Offline Inedo Products

Your Inedo product will notify you of new version releases, but if you're using an offline version then you will not be notified of the latest version releases. Instead, visit your MyInedo page and select **Upgrade Guidance and Change Notes** in the [**Downloads**](https://my.inedo.com/downloads) tab to view our recommended upgrade process.

![Downloads](/resources/docs/myinedo-viewingupgradeguidance-downloads.png){height="" width="50%"}

![Upgrade_Guidance_Changle_Notes](/resources/docs/myinedo-viewingupgradeguidance-upgradeguidanceandchangenotes.png){height="" width="50%"}

Since offline versions of Inedo products don’t receive notifications on new version releases it means that bug fixes and important security patches may go unnoticed. We recommend visiting your MyInedo page regularly and viewing our upgrade guidance and change logs to make sure that your software is secure and optimized.

## How to View Upgrade Guidance

[Inedo products can be upgraded or downgraded in the Inedo Hub](/docs/installation/windows/inedo-hub-upgrade-downgrade), but there are times a direct upgrade is not possible, and it is recommended that you carefully read the change notes between software versions provided in our upgrade guidance.

:::(Info)
**Backing Up Your Data**
Before upgrading your Inedo Product, we recommend creating a backup so you can easily restore things if needed.
:::

On your MyInedo page, choose your Inedo product, current version, and chosen upgrade version, then select **Get Upgrade Guidance**.

![Upgrade_Guidance](/resources/docs/myinedo-viewingupgradeguidance-getupgradeguidance.png){height="" width="50%"}

## Recommended Upgrade Path

You can see our recommended steps for upgrading, tailored specifically to your version transition, providing you with a careful assessment of risk, and linking to Inedo Documentation pages with further instructional detail on upgrading in your situation.

Upgrading can be a straightforward process, such as moving from **ProGet version 2024.25** to **2024.9**:

![Upgrade_Recommended](/resources/docs/myinedo-viewingupgradeguidance-upgraderecommended.png){height="" width="50%"}

In this case, follow the steps on the [**Upgrading your Inedo Product**](/docs.inedo.com/docs/installation/upgrading) page to upgrade.

However, direct upgrades are not always possible, especially when upgrading from older versions. In these cases, multiple steps may be required. Refer to our guidance for detailed instructions:

![Upgrade_Not_Possible](/resources/docs/myinedo-viewingupgradeguidance-directupgradenotpossible.png){height="" width="50%"}

In this case, the first step requires upgrading to a different version of ProGet. Visit the [**Upgrading to ProGet 5.2**](/docs.inedo.com/docs/proget/installation/proget-upgrade-guide/proget-installation-and-maintenance-and-upgrade-notes-upgrading-to-proget-5-2) page for detailed instructions on upgrading. 

We may recommend upgrading to the latest version, for security and stability reasons, particularly when transitioning between two older versions of software:

![Upgrade_Not_Recommended](/resources/docs/myinedo-viewingupgradeguidance-upgradenotrecommended.png){height="" width="50%"}

Keeping your software up to date is important for security and compatibility. Always review our guidance and change notes to assess potential risks before upgrading. 

**See Guidance** for detailed Inedo Documentation pages explaining how to upgrade, notable improvements and changes, and common issues / risks encountered.

<!-- I think that viewing the change notes is important to mention here. but if the user is already in the upgrading process, do they need to seek out viewing the change logs? -->

## Change Notes

Despite a risk assessment being provided alongside your recommended upgrade path, it's good practice to note the changes in each maintenance release up to your intended version, visible on the same page.

![Change_Notes](/resources/docs/myinedo-viewingupgradeguidance-changelogsredbox.png){height="" width="50%"}

Use these change notes to determine if there are any fixes, implemented features, or compatibility issues, that could result in a rollback.

Visit an issue number page on our public issue tracker for further insight into how these changes may conflict with your current operating environment and needs after an upgrade, and to see the progress of impending fix releases.

<!-- ### Viewing Change Logs

There may be situations where you want to view change notes in detail, to identify where an issue is stemming from after an upgrade, or to search for the version in which a patch or feature you were anticipating was released. The easiest way to do so would be to visit our [**Detailed Change Logs page**](/docs.inedo.com/docs/myinedo/viewing-change-logs) -->

<!-- While the below content might be useful for specific upgrade guidance, based on the feedback I don't feel the need to link it here since the user would have likely already sought out this information specifically for their product? -->

<!-- ## Version Specific Upgrade Guidance

We've already put together guides featuring the recommended upgrade paths of each product, focusing on major releases:

* [**ProGet Upgrade Guide**](/docs/proget-upgrade-guide)
* [**BuildMaster Upgrade Guide**](/docs/buildmaster-upgrading)
* [**Otter Upgrade Guide**](/docs/otter-upgrading)

However, if you are looking to upgrade from a maintenance release or from a much older version, viewing our recommended upgrade path will get you on the right track faster. -->