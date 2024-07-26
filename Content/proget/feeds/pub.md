---
title: "pub (Dart/Flutter)"
order: 18
---

[pub.dev](https://pub.dev) is the main repository for Dart or Flutter packages. Since version 2024.11, ProGet supports pub feeds to host pub packages.

### Installing Packages

Pub packages are added as dependencies to a `pubspec.yaml` file. To use a package from a ProGet feed, add an entry like this to `pubspec.yaml` in the `dependencies` section:

```
{package-name}:
 hosted: {proget-server}/pub/{feed-name}
 version: ^{package-version}
```

#### Authenticated Feeds

If you've configured your feed to require authentication, you must add a token to dart/flutter. First, [create any API key](/docs/proget/reference-api/proget-apikeys) (such as a personal API key) with the desired permissions in ProGet.

Next, add that API key to dart/flutter as a token using the `pub token add` command:

```
dart pub token add {proget-server}/pub/{feed-name}
```

or

```
flutter pub token add {proget-server}/pub/{feed-name}
```

Dart/Flutter will prompt for the API key/token. You may paste it into the console or type it in manually.

### Creating Packages

To learn how to create a pub package that can be hosted by ProGet, see [the official documentation](https://dart.dev/guides/libraries/create-packages).

### Publishing Packages

To publish a package to ProGet using Dart or Flutter, add a `publish_to` field to your `pubspec.yaml` file:

```
publish_to: {proget-server}/pub/{feed-name}
```

See [Publishing to a custom package repository](https://dart.dev/tools/pub/custom-package-repositories#publishing-to-a-custom-package-repository) in the official documentation for more information.

### Connecting to External Repositories

You can create a connector to any pub repository (including `https://pub.dev`). This will allow you to proxy the external feed through ProGet, but browsing through the web application is limited, since pub repositories do not provide a search API, and do not expose certain metadata such as a package's README file.

### Proxying pub.dev

If you have a connector to [pub.dev](https://pub.dev) in your feed, you may want to override the default package repository on your system to point to your ProGet feed instead of pub.dev. See [Overriding the default package repository](https://dart.dev/tools/pub/custom-package-repositories#default-override) for instructions on configuring this.
