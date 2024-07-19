---
title: "Backing Up and Restoring a Database"
order: 1
---

This article will walk you through backing up and restoring the SQL Server database that's part of your Inedo product, as well as creating regular backup schedules. 

If you've never worked with Microsoft SQL Server before, don't worry - you won't even know you're working with it.  But if you're already familiar with Microsoft SQL Server and know how to back up and restore databases, this article will not provide much value; just use whatever database backup approach works best.

## Backing up your database
:::(Error)
Backing up a database on it's own isn't sufficient to backup your Inedo product. This guide only covers the database backup portion; for more details on this we recommend reading [Backing Up and Restoring](/docs/installation/backing-up-restoring)
:::

### Instance Name
In addition to the server name/address, an "instance name" is required to connect to Microsoft SQL Server

If you selected "Built-in Database" during installation, then the Inedo Hub would have most likely installed Microsoft SQL Server Express Edition with an instance named `INEDO`. However, older versions used a different instance name:

- BuildMaster v5 and earlier installed SQL Server 2005 Express, and the SQL Express was named `BUILDMASTER`
- ProGet v3 installed SQL Server 2005 Express, and the SQL Express was named `PROGET`
- Otter v1 installed SQL Server 2005 Express, and the SQL Express instance was named `OTTER`

### Using osql.exe
`osql.exe` is a command-line tool that is used to write SQL statements and scripts when using Microsoft SQL Server.  See [Managment Tools](/docs/installation/sql-server#management-tools) for more information.

By using `osql.exe` you can run a single command to back up the database, which you may need to run as an administrator if UAC is enabled in Windows:

| Product | Back up command |
| --- | --- |
| BuildMaster | ``osql -S .\INEDO -E -Q "BACKUP DATABASE [BuildMaster] TO DISK=N'C:\Backups\BuildMaster.bak' WITH FORMAT"``
| ProGet | ``osql -S .\INEDO -E -Q "BACKUP DATABASE [ProGet] TO DISK=N'C:\Backups\ProGet.bak' WITH FORMAT"``
| Otter | ``osql -S .\INEDO -E -Q "BACKUP DATABASE [Otter] TO DISK=N'C:\Backups\Otter.bak' WITH FORMAT"``

You can customize the path on the disk as needed. To run this command on a regular basis, set up a scheduled task using the [Task Scheduler](http://windows.microsoft.com/en-US/windows-vista/Schedule-a-task). Note that your Inedo product can be [backed up at any time](/docs/installation/backing-up-restoring) without having to stop any of the services.

### Using SQL Server Management Studio
SQL Server Management Studio (SSMS) is a GUI-based IDE tool used for managing SQL Server databases. See [Managment Tools](/docs/installation/sql-server#management-tools) for more information.

You can easily back up and restore databases using SSMS. Refer to Microsoft's documentation ([Create a Full Database Backup](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-ver16)) to learn how.

### SQL Server Azure Backups

SQL Database automatically creates database backups that are kept between 7 and 35 days, and uses Azure read-access geo-redundant storage (RA-GRS) to ensure that they are preserved even if the data center is unavailable. These backups are created automatically and at no additional charge.

## Restoring your database
:::(Warning)
Restoring a database on its own is not sufficient to roll back to a previous product version. Make sure to also install or rollback to that version, and that any artifact files (BuildMaster) and/or packages files (ProGet) are where they are expected to be.

:::

### Using osql.exe

When you need to restore a database from a backup made using the command mentioned above, you can use the following command:

| Product | Restore command |
| --- | --- |
| BuildMaster | ``osql -S .\INEDO -E -Q "RESTORE DATABASE [ProGet] FROM DISK = N'C:\Backups\ProGet.bak' WITH FILE = 1, NOUNLOAD, STATS = 10"``
| ProGet | ``osql -S .\INEDO -E -Q "RESTORE DATABASE [BuildMaster] FROM DISK = N'C:\Backups\BuildMaster.bak' WITH FILE = 1, NOUNLOAD, STATS = 10""``
| Otter | ``osql -S .\INEDO -E -Q "RESTORE DATABASE [Otter] FROM DISK = N'C:\Backups\Otter.bak' WITH FILE = 1, NOUNLOAD, STATS = 10"``

Make sure the Inedo product's Windows Service(s) is stopped; otherwise, SQL Server will complain with a "database in use" error.

### Using SQL Server Management Studio

As with backing up a database, restoring it is simple when using SSMS. To learn more about doing so, refer to this documentation: [Restore a Database Backup using SSMS](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-ver16)

## Creating a Backup Schedule

In order to ensure that backups are created regularly, we recommend scheduling a full backup every 24 hours. There are several tools available available for scheduled backup, but the easiest to use are:
* [SQL Server Agent](https://learn.microsoft.com/en-us/sql/ssms/agent/sql-server-agent?view=sql-server-ver16)
* [Windows Task Scheduler]( https://docs.microsoft.com/en-us/windows/win32/taskschd/introduction-to-task-scheduler.).

SQL Server Agent is specifically designed for managing and scheduling SQL Server tasks, and simplifies creating backups. However it may require you to enable advanced services to support specific types of tasks. Task Scheduler on the other hand can be easier to set up than SQL Server Agent, but does require scripts to be written.

### How to Schedule Backups with SQL Server Agent

In order to schedule a backup with SQL Server Agent, follow these steps:

1. Select "Connect" > "Database Engine", then in the "Connect to Server" dialog box, select the appropriate values in the "Server type" list, the "Server name" list, and the "Authentication" list, and select "Connect".

2. In Object Explorer, expand Databases, and right-click the database you want to back up. Then select "Tasks", and then select "Back Up".

3. In the Back Up Database dialog box, make sure that "Backup type" is set to "Full" and "Backup component" is set to "Database". Under "Destination", select "Disk" for the "Back up to" option and then select "Add".

4. In the "Select Backup Destination" dialog box, enter a path and a file name in the "Destinations on disk" box, and then select "OK".

5. In the Script list, select "Script Action" to "Job".

6. In the "New Job" dialog box, select "Steps" under "Select a page", and then select "Edit" if you want to change the job parameters. Then under "Select a page", select "Schedules", and then select "New".

7. In the "New Job Schedule" dialog box, enter the job name in the "Name" box, specify the job schedule, and then select "OK".

8. Select "OK" two times. You should receive the following message:

    "The backup of database 'DatabaseName' completed successfully."

For more information on scheduling database backups with SQL Agent, see [How to schedule a database backup operation by using SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/schedule-database-backup-operation-ssms?view=sql-server-ver16)

:::(Warning) (SQL Agent is not supported on all versions of SQL Server Express)
Only the "SQL Server Express with Advanced Services" edition supports SQL Agent; if you're using the standard edition of SQL Server Express, you'll need to upgrade your instance or use a different approach.
:::


 ### How to Schedule Backups with Windows Task Scheduler

To schedule a backup with SQL Server Agent using `osql.exe`, follow these steps:

1. Click Start, then type "Task Scheduler" in the search box and open the application.

2. In Task Scheduler, right-click on "Task Schedule Library" and select "Create Basic Task…". Provide a name for the task and click "Next".

3. Set the recurrence to daily and click "Next". Then, choose "Start a program" as the action and click "Next".

4. Click "Browse," locate `osql.exe` in your SQL Server Installation folder, and then click "Open."

5. In the "Add arguments (optional)" field, enter the following: `S .\INEDO -E -Q "RESTORE DATABASE «product-name» FROM DISK = N'C:\Backups\«product-name».bak' WITH FILE = 1, NOUNLOAD, STATS = 10"`

6. Select the "Open the Properties dialog for this task when I click Finish" check box and click "Finish".

7. In the Properties window under Security options, verify that the user listed has permission to make SQL Backups, and then press "OK".

For more information on scheduling database backups with Task Scheduler, see [Schedule and automate backups of SQL Server databases in SQL Server Express](https://learn.microsoft.com/en-us/troubleshoot/sql/database-engine/backup-restore/schedule-automate-backup-database)