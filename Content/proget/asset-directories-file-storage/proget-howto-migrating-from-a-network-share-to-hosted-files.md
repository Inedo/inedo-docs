---
title: "HOWTO: Migrate from a Network Share"
order: 4
---


Migrating from a network share to hosted files in an asset directory could be a smart move for teams that:

* Work with restricted Internet access but do not want to use a network share.
* Would like to host and access files without the need for a VPN.
* Would like to leverage [Replication](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication) and [High Availability](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) in ProGet for the uses of [Edge Computing](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations), [Federated Development](/docs/proget/replication-feed-mirroring/proget-howto-federated-development), and [Disaster Recovery](/docs/proget/replication-feed-mirroring/proget-howto-utilize-feed-replication-for-disaster-recovery).
* Need to easily hyperlink to web-based files versus network shares.

Regardless of the use case, the ability to share files internally is a huge benefit for many teams. The biggest question these teams face when migrating is **how** to migrate thousands of files spread across hundreds of folders.

Using ProGet as a web-based file host makes it easy to transfer files.

In this article, we'll walk through a typical scenario of a company, Kramerica, that wants to migrate some of its files from a network share to hosted files in an asset directory.

## Step 1: Plan What Files/Folders You Want to Move to Asset Directory
In ProGet, your asset directory works very much like a file share. Folders in your asset directory can be easily shared with many users, files can be downloaded and updated.

However, unlike file sharing services, documents cannot be edited directly. This means that a user must download a file, edit it, save it, and then upload a new version to edit it. While this makes editing more difficult, it also makes it less likely that files will be accidentally overwritten. ProGet is an ideal place to store and share files that are rarely changed. But not an ideal place for work in progress.

It's important to know that users who have access to an asset directory in ProGet also have access to all subfolders within it. Therefore, organizations that require multiple levels of security must create separate asset directories with the appropriate permissions and restrictions.

In our example, Kramerica plans to transfer 2 folders:

| Folder | Access |
| --- | --- |
| Sales Assets: Including PDF’s presentations, pricing guides, etc. | Sales Team |
| HR Guides: Including the employee handbook, dress codes, etc. | Everyone |
	

## Step 2: (Optional) Integrate ProGet with Active Directory
This step is optional, but recommended.

Organizations can integrate AD with ProGet and use preset groups and permissions in the following steps.
Kramerica integrated navigate to Settings > "Security & Authentication" > "Change User Directory (LDAP)"

After successfully completing the following popup, LDAP will be integrated with ProGet so we can use all previously configured users and groups from Kramerica.

![Switch to Active Directory in ProGet](/resources/docs/progetassetdir-intergratead.png)
 
## Step 3: Create `HR-Guides` Asset Directory
We’ll now create our first asset directory: `HR-Guides` 

To do this, navigate to “Assets” > “Create New Asset Directory” > and fill in the popup with a name and, optionally, a description.
 
![Create HR-Guides Asset Directory in ProGet](/resources/docs/progetassetdir-createnewfeed-hrguides.png)
 
Now that our feed is created, we want to enable versioning. To do this, navigate to “Manage Directory” > "Properties" > "Asset Directory Settings" > "Versioning" 

Once versioning is enabled, old versions of files will be retained after they are overwritten.

![ProGet Manage Feed of HR Guide](/resources/docs/progetassetdir-managefeed-versioning.png)
 
Now that our asset directory is configured, we can begin uploading HR files and folders. Simply navigate to the asset directory and click on "Add/Upload Files". A popup window will allow you to select how you want to add an asset.

In this example, we'll choose "Upload Files" via the browser, as this is the easiest way.

![ProGet Add Assets](/resources/docs/progetassetdir-addassets.png)
 
After locating the desired folder, we will drag and drop files into the asset directory.

![Asset Directory Upload Files Drag-and-Drop](/resources/docs/progetassetdir-uploadfiles.png)
 
## Step 4: Grant Permissions
Now that our “HR-Guide” asset directory is created, we must make sure that users have access to it. By default, all groups in ProGet have zero permissions. 

[Tasks and permissions can get very granular](/docs/proget/administration-security/proget-howto-configure-permissions-and-restrictions-on-feeds) and organizations with complex team structures might take longer to set up than others. Fortunately, the permissions for the asset directory in ProGet are simple. Users have either read permissions, write permissions, or no permissions.

Kramerica wants the pseudo-group "Everyone" (all users) built into ProGet to have access to "View & Download Packages" from the asset directory "HR -Guide".

To do this, we navigate to Settings > "Security & Authentication" > Users & Tasks > "Tasks" > "Add Permissions".

![Add Privilege to HR Guides Directory](/resources/docs/progetassetdir-addpriv-hrguides-everyone.png)
 
Now all Kramerica’s ProGet users can view and download all “HR-Guides” documents. 

## Step 5: Create `Sales-Assets` Active Directory
Next, we’ll create another active directory `sales-assets`.

We’ll do this by repeating the same thing we did in Step 3.

![Create New Sales Assets Active Directory](/resources/docs/progetassetdir-createnewfeed-salesassets.png)
 
## Step 6: Give Sales Group Permission 
In ProGet, it is easy to restrict Active Directory permissions to individual groups. In our example, only members of the sales team should have access to the active directory "sales-assets".

To set this up, navigate to Settings > Security & Authentication > Users & Tasks > "Tasks" > "Add Permissions" and grant Kramerica's "Sales Team" permission to "View & Download Packages" from "sales-assets". After we set this up, our task overview page looks like this:
 
![Tasks and Feeds Configured](/resources/docs/progetassetdir-tasksoverview-complete.png)