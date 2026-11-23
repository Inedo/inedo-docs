---
title: "Storage and Quotas"
order: 10
---

ProGet can use a variety of storage for your packages, blobs, and assets. To help you understand what's consuming the most space, use the Storage Overview page, available from the Administration page.

## Storage Overview

On this page, you can see a useful breakdown of space consumption by package, Docker, and assets (files), along with information on specifically which feeds are the largest.

## Storage Quotas

You can configure a global storage quota by selecting Configure Quota from the dropdown menu. A global storage quota sets a limit for how much storage can be consumed by everything across all of ProGet.

You can also configure feed-specific storage quotas on the Feed Management page under the Storage & Retention tab.

### Recalculation

An accurate calculation of storage usage is performed by a job daily, but can also be run immediately from this page. As packages and other items are added (or removed) throughout the day, ProGet will attempted to maintain these counts, but a full and more accurate count is still performed daily to reconcile any inaccuracies.