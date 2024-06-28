---
title: "Collecting Installed Packages & Running Containers"
order: 3
---

Otter will also collect information about the packages (such as Universal, Chocolatey, PowerShell, etc.) and Docker containers on your servers. This means you don't have to log in to each server to view installed packages. or run `docker inspect` on each server.


### Configuring & Viewing  Packages
To configure a server package collection, go to the *Packages & Containers tab* on the server's overview pages, and click the *configure* button. You will be prompted for which types of packages to collect:

![servers 4.png](/resources/docs/servers%204.png){height="" width=""}

When configured, Otter will regularly collect information about installed packages and display it in an easy-to-read report.

![servers 5.png](/resources/docs/servers%205.png){height="" width=""}

### Configuring& Viewing  Docker Containers

To configure server container collection, go to the *Packages & Containers tab* on the server's overview page, and click the *configure* button. Check the box to collect server containers.

Otter will now display container information along with server packages in one simple report.