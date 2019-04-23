---
title: Assembling Virtual Package Contents
subtitle: What is a Virtual Package?
sequence: 4
keywords: proget, universal-packages, virtual
---
  To assemble a virtual package, the following logic must be used.

Virtual packages are essentially pointers to other packages and resources, and before consuming a virtual package, it must be "assembled" into a regular package first.

Assembling a virtual package basically involves following the resource pointers and combining the contents of those resources. Once you do that, it's totally indistinguishable from a regular package, and thus it is no longer considered "virtual". Thus, it's a one-way operation.

To assemble a virtual package, the following logic must be used.

{.docs}
- The contents array is enumerated; each resources is assessed as follows:

   {.docs}
    - **If it's a file resource:** the contents are downloaded, verified (if a `hash` is specified), and written to the target virtual path. A directory is created if it doesn't exist, and a file is overwritten if it already exists.
    - **If it's a package resource:** the current package source is queried for the specified package (including `hash`). The contents are then extracted to the target virtual path, overwriting any existing files.

{.docs}
- The metaContents array is enumerated; each resources is assessed as follows:

   {.docs}
    - **If the virtualPath is upack.json or within the packages directory:** the operation fails
    - **If it's a file resource:** the contents are downloaded, verified (if a `hash` is specified), and written to the target virtual path. A directory is created if it doesn't exist, and a file is overwritten if it already exists.
    - **If it's a package resource:** the current package source is queried for the specified package (including `hash`). The contents are then extracted to the target virtual path, overwriting any existing files.

{.docs}
- If any error occurs during assembly, then the entire operation fails.
