---
title: PyPI (Python)
subtitle: PyPI Feeds in ProGet
sequence: 500
keywords: proget,feeds,pypi,python
show-headings-in-nav: true
---

The Python Package Index ([PyPI](https://pypi.org/)) is the primary repository for software and libraries written in the Python programming language. As of v5.2, ProGet supports creating PyPI feeds to host and serve in-house Python software packages.

### Installing Packages

PyPI packages are installed using [pip](https://pip.pypa.io). To install a package from a ProGet feed, use the following command:

```
pip install {package-name}=={package-version} -i http://{proget-server}/pypi/{feed-name}/simple 
```

### Creating Packages

To learn how to create a package that can be hosted by ProGet, visit the [Packaging Python Projects tutorial](https://packaging.python.org/tutorials/packaging-projects/). 

### Publishing Packages

### Twine

PyPI packages are pushed to ProGet using [twine](https://pypi.org/project/twine/). To push a package to a ProGet feed, use the following command:

```
twine upload --repository-url http://{proget-server}/pypi/{feed-name}/legacy <dist>
```

### cURL

PyPI packages may be published to ProGet with cURL using the following syntax:

```
curl https://{proget-server}/pypi/{feed-name}/upload/<packageName-version.tar.gz> --user <user>:<password> --upload-file <packageName-version.tar.gz>
```

#### Technical Limitations

{.docs}
 - ProGet only supports searching package name and summary fields via the XML-RPC API (implemented by PyPI.org), other use-cases of this API are not supported by ProGet
 - Connectors to other PyPI feeds require the PyPI JSON API to be implemented (both ProGet and PyPI.org support this)