---
title: "Release Notes"
order: 1
---

There are times you'll want to attach additional information to a [release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) or [build](/docs/buildmaster/builds-continuous-integration/buildmaster-builds), usually to document something for later auditing purposes or to share information with another team member.

For example, you might want to document some of the following:

* The reason for using a different pipeline for the release instead of the usual pipeline
* Why a release was un-canceled
* Special considerations for testing
* Deployment issues found in a particular build

BuildMaster facilitates this documentation through the use of release notes. Release notes are often helpful to model part of an existing release process.

## Creating and Editing Notes

You can create or edit a release note on either the release or build overview page.

This is only possible for users who have special permission to manage release notes.

## Automatically Creating Notes

The Create Release Note operation allows you to add a note to the current release, a release, or a build in any application using an operation in your deployment plan. You can include any text in the release note, including:

*   Corrective steps automatically executed using a try/catch statement after an error occurred during a deployment
*   The ID of an issue created using an [issue tracking tool operation](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking)
*   The specific server used in a resource pool