---
title: "Manually Installing Extensions"
order: 4
---

When developing your own extnesions, or for installations that are restricted from accessing the internet, extensions can also be installed manually:

 1. Download the desired version of the extension from the [public ProGet feed](https://proget.inedo.com/feeds/Extensions)
Copy the extension file (e.g. Windows.upack) to the extensions path.
 2. Visit the Admin > Advanced Settings page within your Inedo product instance to determine where extensions are stored by examining the `Extensions.ExtensionsPath` setting.
 3. Ensure the file is named `<extension-name>.upack` in the extensions directory, and that no other files with the same name exist in that directory, even with a different extension. For example, make sure to delete an existing `<extension-name>.inedox` file if you are installing `<extension-name>.upack`. 
   ::: (WARNING) (Note)
   The downloaded file may contain a version part, which should be removed (e.g. InedoCore-1.5.0.upack should be just InedoCore.upack)
   :::
 4. Restart the product's Windows service: Admin > Service > Stop then Start (e.g. INEDOBMSVC, INEDOPROGETSVC)
 6. Restart the product's Website application, either the IIS application pool: Admin > Service > Restart Web App
 7. Verify that the new extension has been loaded (Admin > Extensions)
