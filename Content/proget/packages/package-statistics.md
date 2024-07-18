---
title: "Package Download Statistics"
order: 4
---

ProGet's package download statistics allow you to track individual package downloads. When enabled, ProGet will record the download date, user, etc., and gives you information about the packages your organization uses.

All editions of ProGet can record package downloads, but date, user, etc. are only visible in the paid and trial versions of ProGet.

## Configuring Package Download Statistics  

Since package download statistics can take up a lot of database disk space, you must first enable these statistics. To do so, go to Manage Feed > Properties > Change "Package Statistics" From here you can enable "Record individual downloads for advanced statistics

You can also "Restrict viewing download statistics to Feed Administrators," if you want to limit the display of potentially sensitive information such as IP addresses.

## Viewing Package Download Statistics in the UI 

Once download statistics are enabled, ProGet records the following information each time a package file is requested:

- **Username** - the name of the authenticated user who downloaded the package, or "Anonymous" if the user was not logged in
- **IP Address** - the IP address that requested the package
- **User Agent** - the user agent header that was sent in the download request
- **Date/time** - the exact date/time the package was downloaded

On the Package page, you can view this download data by clicking the Usage & Statistics tab on a package's overview page.

![UsageAndStatistics](/resources/docs/UsageAndStatistics.png){height="" width="50%"}

### Limitations in Free 

The free version of ProGet allows you to preview this feature and test it manually.

ProGet will still record the actual downloads, but Free users will see the date, user and IP address marked as "hidden". To view the records you'd see in paid and trial editions of ProGet, click "add record" and manually add a record with an IP address of 127.0.0.* (not 1).

## Advanced Reporting  

For more advanced analysis of package downloads, you can query the ProGet SQL Server database directly and/or export the data as needed. Each download record is stored in the `PackageDownloads` table.

For example, the following query will identify the users who downloaded packages named `RubbishSoft.LongGUID` in the past 90 days.

```
SELECT DISTINCT [User_Name]
  FROM [PackageDownloads]
 WHERE [Download_Date] > DATEADD(DAY, -90, GETUTCDATE())
   AND [Feed_Name] = 'approved-nuget'
   AND [Package_Id] = 'RubbishSoft.LongGUID'
```

## Purging Package Download Statistics
:::(Warning)
Package Download Statistics can lead to a big headache if you're using SQL Server Express, as the database is limited to 10GB. It may take years to exceeding that limit, but when it happens ProGet will become mostly unusable, since new data cannot be logged. 
:::

Tracking each and every download of a frequently downloaded package can add up to a nontrivial amount of diskspace. For example, after a few years of heavy usage (e.g. thousands of downloads per hour), you'll end up with several gigabytes of log records.

If you want to use package statistics for frequently downloaded packages, we recommend purging records on an at least an annual basis. This is not built-in to ProGet, but it's easy to do with a few SQL Scripts.

### HOWTO: Quickly Purge all Package Statistics
In the event that you inadvertently enabled package statistics and just want to delete everything quickly, you can run `TRUNCATE [PackageDownloads]`.The rows will be deleted in an instant.

### HOWTO: Selectively Purge Package Statistics
You could simply run a query like `DELETE PackageDownloads [Download_Date] < '2023-01-01` to delete all package download statistics before 2023. However, if there are millions of rows, then it might take a lot of server resources (and a long time) due to transaction logging.

To solve this problem, you can a "chunking strategy" for your purge scripts. For example, if you wanted to purging all package statistics from before 2023 from the `unapproved-nuget` and `unapproved-npm` feeds, you would use a script like this:

```
DECLARE @DELETED INT = 1
WHILE (@DELETED > 0)
BEGIN
    BEGIN TRANSACTION
    
    -- PURGE OLD DATA
    DELETE TOP (10000) [PackageDownloads]
    WHERE [Feed_Name] IN ('unapproved-nuget', 'unapproved-npm') OR [Download_Date] < '2023-01-01'
    SET @DELETED = @@ROWCOUNT
    
    COMMIT
    CHECKPOINT 
END
```

You can then run  query to shrink the database:

```
DBCC SHRINKFILE (N'ProGet' , 0, TRUNCATEONLY)
DBCC SHRINKDATABASE(N'ProGet' )
```
