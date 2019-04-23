---
title: Example Virtual Packages
subtitle: Example Virtual Packages
sequence: 20
keywords: proget, universal-packages, virtual
---
## Minimal {#minimal data-title="Minimal"}

```
{
 "name": "HDARS.Combined",
 "version": "1.3.9",
 "contents": [ "HDARS.Web:1.3.9", "HDARS.API:1.3.9"  ]
}
```
## Path References {#path data-title="Path References"}

```
{
"group": "initrode/vendors/abl",
"name": "ABLast.AstDist",
"version": "2.2.1",
"contents": [
{
"type": "virtualDirectory",
"virtualPath": "vendors/common/ast",
"source": "initrode/vendors/abl/ABlast:2.2.1:ab60bf74fc8147ca41bd53bdb1defc3aae35bc91"
},
{
"type": "virtualFile",
"virtualPath": "common/logo/logo.png",
"source": "http://proget/endpoints/customer-assets/content/ast-logo.png"
}
]
```
