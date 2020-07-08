---
title: Package Download Statistics
sequence: 300
---

ProGet's package download statistics let you track individual package downloads. When enabled, ProGet will record the download date, user, etc., giving you information about the packages your organization is using.

All editions of ProGet can record package downloads, but dates, users, etc. are visible only with paid and trial editions of ProGet. 

## Configuring Package Download Statistics  {#configure data-title="Configure Package Download Statistics"}

Because package download statistics can consume a lot of database disk space, you must first enable these statistics. Do this by going to Manage Feed > Properties > Change "Package Statistics." From here you can enable "Record individual downloads for advanced statistics." 

You can also "Restrict viewing download statistics to Feed Administrators," if you want to restrict whom can view potentially sensitive information like IP addresses. 

## Viewing Package Download Statistics in the UI {#view-stats data-title="Viewing Package Download Statistics in the UI"}

Once download statistics are enabled, ProGet will record the following information each time a package file is requested:

{.docs}
- **Username** – the name of the authenticated user who downloaded the package, or “Anonymous” if the user was not logged in
- **IP Address** - the IP address that requested the package
- **User Agent** - the user agent header that was sent in the download request
- **Date/time** - the specific date/time the package was downloaded

On the Package page, you can see this download data by clicking on the Usage & Statistics tab on a package overview page.

![](/resources/documentation/proget/PG_usage-and-stats_tab.png){.screenshot}

### Limitations in Free {#free data-title="Limitations in Free"}

The free version of ProGet allows you to preview this feature, as well as to trial it manually.

ProGet will continue to record actual downloads, but Free users will see the date, user, and IP address marked "hidden":

![](/resources/documentation/proget/paid view of package download stats (IP address hidden).png){.screenshot}

To view the records you'd see in paid and trial editions of ProGet, click "add record" and manually add a record with an IP address of 127.0.0.* (not 1):

![](/resources/documentation/proget/free view of package download stats.png){.screenshot}


## Advanced Reporting  {#advanced-reporting data-title="Advanced Reporting"}

For more advanced analysis of package downloads, you can query the ProGet SQL Server database directly and/or export the data as needed. Each download record is stored in the `PackageDownloads` table.

For example, the following query will identify which users downloaded packages named `RubbishSoft.LongGUID` in the past 90 days.

```
SELECT DISTINCT [User_Name]
  FROM [PackageDownloads]
 WHERE [Download_Date] > DATEADD(DAY, -90, GETUTCDATE())
   AND [Feed_Name] = 'approved-nuget'
   AND [Package_Id] = 'RubbishSoft.LongGUID'
```
