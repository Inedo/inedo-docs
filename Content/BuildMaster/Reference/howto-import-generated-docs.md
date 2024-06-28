---
title: "HOWTO Import Generated Docs"
order: 1
hidden: true
---

:::(Error)
This is for internal reference only; do not publish
:::

1. **Generate Reference Zips**; this can be done by going to `/reference/functions.zip` and `/reference/operations.zip` in BuildMaster and ProGet

2. **Back-up Site**; settings > Backup & Restore > Backup > "Pre-import Backup"

3. **Delete Existing**; delete the existing "Built-in Operations" and "Built-in Functions" categories

4. **Import Files**; settings > Import  & Export > Import ... make sure to select "Publish" 

5. **Fix Slugs (no redirect)**; due to a bug, category slugs aren't imported, and make sure to say NO when prompted to add a redirect
  -> otter-reference-functions 
  -> otter-reference-operations
  -> buildmaster-reference-functions 
  -> buildmaster-reference-operations