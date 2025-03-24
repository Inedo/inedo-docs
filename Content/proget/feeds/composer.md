---
title: "Composer (PHP)"
order: 22
---

[Composer](https://getcomposer.org/) is a dependency manager for PHP that allows you to declare and manage libraries, projects, plugins, and extensions for PHP applications and libraries.

A Composer feed in ProGet acts like a private Composer repository where you can store your own packages. You can also create connectors to [Packagist.org](https://packagist.org/) and other Composer V2 repositories that let you use third-party open source packages through your Composer feed and create a curated list of approved packages.

Composer feeds are available in ProGet 2024.22+.

## Adding a Composer feed Repository
Before installing or publishing a package to your ProGet Composer feed, you will need to add your feed as a repository:

```bash
composer config repositories.proget -g "composer" "https://«proget-server»/composer/«feed-name»/"
```

If you have not configured ProGet to use SSL or you are using a self-signed certificate with ProGet, you will also need to disable the secure repositroy requirement in composer:

```bash
composer config -g secure-http false
```

:::(INFO)(Note:)
You can use either `php composer.phar` or `composer` for composer CLI commands.
:::


### Proxy All Composer Requests Through ProGet

When you add another repository to Composer, it will still use [Packagist.org](https://packagist.org/) unless you explicitly disable it. To proxy all requests through your ProGet feed instead, make sure you disable Packigist.org:

```bash
composer config -g repo.packagist false
```

### Authenticated Feeds

If your require authentication, Composer will prompt for login credentials or you can explicitly log in using the `composer config` command.

```bash
composer config -g http-basic.«proget-server» «username» «password»
```


Composer prompts for a user name and password for authentication. You may use your ProGet user name and password, or you may specify `api` as the user name and use an API key or personal access token.

#### Example

For a ProGet server located at `https://proget.my-company.com` and an API key of `my-api-key`, you would run the following command:
```bash
composer config -g http-basic.proget.my-company.com api my-api-key
```

## Creating and Publishing Packages

Generally, Composer packages are committed to a git repository and then the Composer repository, like [Packagist.org](https://packagist.org), handles converting it to a package.  Then when the packages are required in your project, it first attempts to pull the source from git and then falls back to downloading the package if it cannot be pulled. This git first approach lends to the possibility for the source code to change on git repository between builds of your Composer project, even when your dependency version doesn't change.  ProGet, on the other hand, takes a package first approach to prevent the possibility of source changing between when your package is submitted and when it is required by your project.

Creating a Composer package for ProGet does not require any more work than creating one for Packagist.  

1. Create your Composer package just like you would for any other repository by creating a `composer.json` file that contains your projects `name` and `description`.  You will also need to specify your package's `version` since it cannot determine the version from the git branch. Example:
    ```json
    {
        "name": "mycompany/mypackage",
        "description": "Description of your project",
        "version": "1.0.0"
    }
    ```
2. Add your composer dependencies using `composer require «dependency»/«name»:"«version»"` then run `composer update`.
3. Add your source code, README, content, files, etc... and run `composer validate`.
4. This is where things differ.  Instead of just adding a `.gitignore` file, you will also need to add `archive\excludes` to your composer.json for anything you want excluded from your package. Commonly excluded files are `composer.lock` and the `vendor` folder. Example:
    ```json
    {
        ...

        "archive": {
             "exclude": ["composer.lock", "vendor/*"]
        }

        ...
    }
    ```
5. Create your package file using `composer archive --format=zip`.  We suggest using the format of `«package-group»/«package-name»-«package-version»` for your package name. Example:
    ```bash
    composer archive --format=zip --file="../dist/mycompany/mypackage-1.0.0"
    ```

This will output the package as a zip file that can then be published to ProGet.

### Publishing Packages to ProGet

Once you have created a package, you can publish it to ProGet using [pgutil](/docs/proget/api/pgutil) the `packages upload` command.

```bash
pgutil packages upload --input-file="../dist/mycompany/mypackage-1.0.0.zip" --feed="«feed-name»"
```

:::(INFO)(Note:)
If the version is not included in the composer.json file, ProGet will attempt to extract the version from the file name.
:::

## Technical Limitations

 - ProGet only implements the Composer V2 API. Composer V1 will be read-only on February 1st, 2025 and then deprecated on August 1st, 2025.  See [Packagist.org's blog post](https://blog.packagist.com/shutting-down-packagist-org-support-for-composer-1-x/) for more information.
 - Due to ProGet's package first approach, ProGet does not include the source download links in the API for remote packages.
 