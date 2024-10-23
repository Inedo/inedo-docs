---
title: "PyPI (Python)"
order: 13
---

The Python Package Index ([PyPI](https://pypi.org/)) is the main repository for software and libraries written in the Python programming language. Since version 5.2, ProGet supports the creation of PyPI feeds to host and deploy custom Python software packages.

### Installing Packages

PyPI packages are installed using [pip](https://pip.pypa.io). To install a package from a ProGet feed, use the following command:

```bash
pip install {package-name}=={package-version} -i http://{proget-server}/pypi/{feed-name}/simple 
```

#### Authenticated Feeds

If you've configured your feed to require authentication, there are two ways that pip can be configured to include authentication; basic authentication passed with the URL in the `pip install` command or by configuing a [pip config](https://pip.pypa.io/en/stable/topics/configuration/) file.

#### Using `pip install` With a URL Parameter

To pass basic authentication along with the URL in the `pip install` command, you will need to add a `--trusted-host` parameter containing your ProGet host and insert the username and password into the beginning of your URL. The pip install command would look like this:

```bash
pip install {package-name}=={package-version} --trusted-host {proget-server} -i https://{user-name}:{password}@{proget-server}/pypi/{feed-name}/simple 
```

For example, if you are installing Flask 2.3.3 from a ProGet feed with the endpoint `https://myprogetserver.com/pypi/approved-pypi/simple`, your command would look like this:

```bash
pip install flask==2.3.3 --trusted-host myprogetserver.com -i https://MyUserName:MyPassword123!@{myprogetserver.com/pypi/approved-pypi/simple
```

If you are using an API key, you will need to use `api` for the username:

```bash
pip install flask==2.3.3 --trusted-host myprogetserver.com -i https://api:MyApiKey123!@{myprogetserver.com/pypi/approved-pypi/simple
```

#### Using a pip Config

To configure pip to use a [pip config](https://pip.pypa.io/en/stable/topics/configuration/) file to store the authenticated feed, you will need to use the [pip config](https://pip.pypa.io/en/stable/cli/pip_config/) command.

```bash
pip config --global set global.index-url https://{user-name}:{password}@{proget-server}/pypi/{feed-name}/simple
pip config --global set global.trusted-host {proget-server}
```

These commands will generate a pip config file that looks like:

```bash
[global]
index-url = https://{user-name}:{password}@{proget-server}/pypi/{feed-name}/simple
trusted-host = {proget-server}
```

::: (Info) (Note:)
The `pip config` can be scoped to global (--global), user (--user), and to the environment (--site).  The commands above are scoped to the global scope.
:::

### Creating Packages

To learn how to create a package that can be hosted by ProGet, visit the [Packaging Python Projects tutorial](https://packaging.python.org/tutorials/packaging-projects/). 

### Publishing Packages

### Twine

PyPI packages are pushed to ProGet using [twine](https://pypi.org/project/twine/). To push a package to a ProGet feed, use the following command:

```bash
twine upload -u {user-name} -p {password} --repository-url http://{proget-server}/pypi/{feed-name}/legacy <dist>
```

::: (Info)
To use an API key, you will need to use `api` for the username and the API key as the password.
:::

### cURL

PyPI packages may be published to ProGet with cURL using the following syntax:

```bash
curl https://{proget-server}/pypi/{feed-name}/upload/<packageName-version.tar.gz> --user <user>:<password> --upload-file <packageName-version.tar.gz>
```

::: (Info)
To use an API key, you will need to use `api` for the username and the API key as the password.
:::

### Connector Technical Limitations (ProGet 2024)
Most PyPI repositories do not provide a user interface and are not designed to be searched or browsed, which makes their display in ProGet challenging. Specifically:

#### Package Searching
 * PyPI repositories do not support searching.  However, you can still navigate to a specific package page by typing the exact name in the search box.
 * When possible, ProGet displays a default list of packages in a feed sorted by download count. For PyPI connectors, ProGet instead uses the RSS feed which is provided by PyPI.org to expose the latest package uploads.

#### Non-PyPI.org repositories
 * PyPI.org implements a JSON API that provides some extra metadata for packages (such as a readme file), but most other repositories do not have this
 * Very basic information can be browsed in the ProGet UI if you know the exact name of the package, but this should primarily be used as a proxy instead of browsed directly
 * You may need to use the "Advanced" tab of the connector to specify an alternative `/simple` path; for example, if you try to connect to `https://download.pytorch.org/whl/cu124` without specifying this, you will receive `403` errors; changing the simple path to `/` will resolve this

### Technical Limitations (ProGet 2023)

 - ProGet only supports searching package name and summary fields via the XML-RPC API (implemented by PyPI.org), other use cases of this API are not supported by ProGet
 - Connectors to other PyPI feeds require the PyPI JSON API to be implemented (both ProGet and PyPI.org support this)
 - Package names are generally normalized (as per the [PEP503 specs](https://www.python.org/dev/peps/pep-0503)), but packages where versions only differ by `-` and `_` (e.g. `my-package 1.0` and `my_package 1.1`) aren't properly listed under `my-package`. In addition, UI-based searching does not treat `_` and `-` the same
 
If these limitations are more than minor inconveniences, please let us know so we can figure out how to fix them.
