---
title: "Get License"
order: 2
---

*Get License* is available as both a `pgutil` command and an HTTP Request, and will return information on a single license.

:::(Info) (🚀 Quick Example: Getting a license with pgutil)
This example returns information on a license with the ID `ABC-1.0`:

```bash
pgutil licenses info --code=ABC-1.0
```
:::

## Command Specification (CLI)
The `licenses info` command is used to get information on a license.

The `--code` options is always required.

**Getting a license** requires the license code (e.g. `MIT`):

```bash
pgutil licenses info --code=MIT

```
Example Output:

```bash
Code: MIT
Title: MIT License

Detection:
 SPDX: MIT
 Url:
  - go.microsoft.com/fwlink/?linkid=865381
  - go.microsoft.com/fwlink/?linkid=868514
  - licenses.nuget.org/MIT
  - opensource.org/license/mit/
  - raw.githubusercontent.com/Microsoft/dotnet/master/LICENSE
  - spdx.org/licenses/MIT.html
  - www.opensource.org/licenses/MIT

Files:
 - 0630520a7440edc1e05c3f318eba0df31920d5b4ff0848ed10d7922b34eed796
```

### Getting a License File
The `licenses files show` command is used to get a license file.

The `--code` and `--hash` options are always required.

**Getting a license file** requires the license code (e.g. `MIT`) and the license file's hash (e.g. `00462de3d7b6f3e5551a69ae84344bc69d23c02e1353be3e8445d16f025e523b`):

```bash
pgutil licenses files show --code=MIT --hash=0630520a7440edc1e05c3f318eba0df31920d5b4ff0848ed10d7922b34eed796
```

Example Output:

```plaintext
MIT License

Copyright (c) <year> <copyright holders>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice (including the next paragraph) shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

## HTTP Request Specification
To return a specified license file, simply `GET` to the URL with the ID of the `license`, the license file hash, and an [appropriate API Key](/docs/proget/api/licenses#authentication).

```plaintext
GET /api/licenses/files/download?code=«license-id»&hash=«file-hash»
```

## HTTP Response Specification
A [LicenseInfo.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/LicenseInfo.cs) object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/licenses#authentication).
