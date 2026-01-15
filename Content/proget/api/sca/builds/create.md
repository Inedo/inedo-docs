---
title: "Create/Update Build"
order: 1
---

*Create Build* is available as both a `pgutil` command and an HTTP Request, and will create a new build or update an existing build.

:::(Info) (🚀 Quick Example: Creating a new build with pgutil)
This example creates build `1.2.3` of the project `myProject`

```bash
pgutil builds create --build=1.2.3 --project=myProject 
```
:::

## Command Specification (CLI)
The `builds create` command is used to create a new build.

The `--project` and `--build` options are always required.

**Creating a build** requires the project name (e.g. `myProject`) and the build number to be created (e.g `1.2.3`)
```bash
pgutil builds create --build=1.2.3 --project=myProject 
```

## HTTP Request Specification
To create or update a build, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication) and a `CreateOrUpdateBuildOptions` object (see [CreateOrUpdateBuildOptions.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/CreateOrUpdateBuildOptions.cs)) object as the request body.

```plaintext
POST /api/sca/builds
```

## HTTP Response Specification

A `BuildInfo` object (see [BuildInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/BuildInfo.cs)) will be returned on a successful `200` response. A `403` rsponse indicates a [missing, unknown, or unauthorized API Key](https://docs.inedo.com/docs/proget/api/licenses#authentication).