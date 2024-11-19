---
title: "Integrating PipEnv or Poetry with ProGet"
order: 3
---

When managing Python packages, `pip` is a common tool of choice. However, you can also use tools like [PipEnv](https://pipenv.pypa.io/en/latest/) or [Poetry](https://python-poetry.org/) when working with your PyPI feeds in ProGet. This page covers how to configure and integrate these two tools with you PyPI feeds as a source and authenticate to them. 

This page covers PipEnv and Poetry specific configuration. You can read our other documentation to learn more about [proxying OSS PyPI packages from your PyPI feed](/docs/proget/feeds/pypi/howto-pypi-proxy) and [publishing packages to them](/docs/proget/feeds/pypi/howto-pypi-publish). 

## Using Your PyPI Feed as a Source to Install Packages { #add-source }

### In PipEnv

To configure [Pipenv](https://pipenv.pypa.io/en/latest/) to use your PyPI feed, set the `PIP_INDEX_URL` environment variable in your shell before running `pipenv install`, or include it in your Pipfile:

```bash
$ export PIP_INDEX_URL=https://«proget-server»/pypi/«feed-name»/simple
```

Alternatively, modify your Pipfile to include:

```bash
[[source]]
name = "«feed-name»"
url = "https://«proget-server»/pypi/«feed-name»/simple"
verify_ssl = true
```

Then install packages with:

```bash
$ pipenv install «package-name»==«package-version»
```

### In Poetry

To configure [Poetry](https://python-poetry.org/) to use your PyPI feed, you can add the repository to your project using the `poetry config` command:

```bash
$ poetry config repositories.«feed-name» https://«proget-server»/pypi/«feed-name»/simple
```

Then, to install a package from your feed, use the `poetry add` command:

```bash
poetry add «package-name» --source «feed-name»
```

## Authenticating to your PyPI Feed { #authenticate-feed }

If you've configured your feed to require authentication, you can use either PipEnv or Poetry to authenticate to it.

::: (Info) (💡 Best Practices: Use API Keys for Authenticated Feeds)
Instead of using your ProGet username/password for a PyPI feed, we recommend [Creating a ProGet API Key](/docs/proget/reference-api/proget-apikeys) to authenticate. You can enter `api` as the username and your key as the password.
:::

### Using PipEnv

To use your feed with Pipenv, you can set the `PIP_INDEX_URL` environment variable with the authenticated URL:

```bash
$ export PIP_INDEX_URL=https://api:«api-key»@«proget-server»/pypi/«feed-name»/simple
```

Then, install packages with Pipenv:

```bash
$ pipenv install «package-name»==«package-version»
```

### Using Poetry

To configure Poetry to authenticate with your PyPI feed, add the repository and credentials to your Poetry configuration:

```bash
$ poetry config repositories.pypy-feed https://«proget-server»/pypi/«feed-name»/simple
$ poetry config http-basic.pypy-feed api «api-key»
```

Once configured, you can install packages using:

```bash
$ poetry add «package-name» --source pypy-feed
```