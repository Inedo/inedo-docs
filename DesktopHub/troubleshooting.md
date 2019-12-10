---
title: Troubleshooting
sequence: 40
keywords: installation
---

In the event that an installation fails, in most cases Inedo Hub will show the product with the failed installation as uninstalled. Note that
after an upgrade, **no data is deleted** from your original instance, so you may safely reinstall the product by specifying the same database
connection information.

However, if a failed install/upgrade causes the software to appear already installed, or even causes Inedo Hub to fail to load, perform the following steps:

	1. Ensure your database/files are backed up, just to be safe
	2. Try to uninstall the product (this will **not** delete any of your data)
	3. Uninstall Inedo Hub
	4. Stop all services related to the installed product (these will all start with INEDO in the service manager)
	5. Delete all data from `C:\ProgramData\upack`
	6. Delete the product installation from `C:\Program Files\<ProductName>` if necessary
	7. Download Inedo Hub and install
	8. Reinstall the product using your original database
