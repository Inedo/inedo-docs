---
title: Attachments
subtitle: Attachments
sequence: 200
keywords: hedgehog, packages, deployment sets

---
:::attention{.technical}
Attachments are an advanced topic.
:::

There are some cases where you may want to "attach" files to a deployment set without creating and publishing an entire package to an external source.  This is exactly where an attachment can come in.

:::attention{.best-practice}
![](/resources/images/icons/best-practices.png) For example, one of the stages in your pipeline may generate a "release notes" HTML file and a later stage may email that file to customers after a release engineer verified the file looks acceptable.
:::

Attachments are essentially zip files that are stored in a directory that Hedgehog manages, which can be on a local disk or a network share. Each zip file is associated with a deployment set, and these zip files can be uploaded or downloaded from the web user interface, as well as created and deployed using the *Create-Attachment* and *Deploy-Attachment* operations.

Unlike universal packages, attachments have no metadata associated with them, or even version numbers. They are merely a zip file that is attached to a package. In addition to the "release notes" example, you can do all sorts of things with attachments, such as importing build output directly from a CI server, or even create an end-to-end continuous delivery workflow, where Hedgehog retrieves and compiles your source code.
