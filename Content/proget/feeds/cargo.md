---
title: "Cargo (Rust)"
order: 19
---

[Cargo](https://doc.rust-lang.org/cargo/index.html) is the package manager for the [Rust](https://www.rust-lang.org/) programming language and uses a package format called a *[crate](https://doc.rust-lang.org/cargo/appendix/glossary.html#crate)*.  *[Crates](https://doc.rust-lang.org/cargo/appendix/glossary.html#crate)* are a way to package your rust libraries and executables.

A Cargo feed in ProGet acts a private Cargo registry that allows you to store your own crates.  You can also create connectors to [crates.io](https://crates.io) and other Cargo registries that let you use third-party crates through your Cargo feed and create a curated list of approved Cargo crates.

Cargo feeds are available in ProGet 2024.20+.

## Adding a Cargo feed as a Registry
Before installing or publishing a crate to your ProGet Cargo feed, you will need to register your feed in your `.cargo/config.toml` file.  To do this, edit your `.cargo/config.toml` file, or create one if it does not exist, and add the following lines to the top of the file:

```toml
[registries]
proget = {index = "sparse+https://«proget-server»/cargo/«feed-name»/", credential-provider = "cargo:token" }
```

Alternatively, you can specify this using an environment variable on Windows using:
```plaintext
CARGO_REGISTRIES_PROGET_INDEX=sparse+https://«proget-server»/cargo/«feed-name»/
```

By default, this will require to add `--registry=proget` on all your cargo commands.  To avoid this, you can add the following to your `.cargo/config.toml`:

```toml
[registry]
default = "proget"
```

### Proxy All Cargo Requests Through ProGet
By default, Cargo will first use [crates.io](https://crates.io) for all dependency resolution.  To proxy all requests through your ProGet feed instead, you will need to add the following to your `.cargo/config.toml`:

```toml
[source]
[source.proget]
registry = "sparse+https://«proget-server»/cargo/«feed-name»/"

[source.crates-io]
replace-with = "proget"
```

This will then tell cargo to use your ProGet Cargo feed for all requests that would typically be made to [crates.io](https://crates.io).

### Authenticated Feeds

If your feed is not configured for anonymous access, then you will need to specify an authentication token for your Cargo feed.  The easiest was to do this is using an API key.  Once you [create a API key](/docs/proget/reference-api/proget-apikeys#creating-and-managing-api-keys), you will need to run the following command:

```bash
cargo login --registry proget «your-api-key»
```

If you would prefer to use a username and password.  You will need to first Base64 encode your username and password in format of `«username»:«password»` and then run the following command:

```bash
cargo login --registry proget "Basic «your-Base64-encoded-username-and-password»"
```

## Connecting to Another Cargo Registry

Cargo supports [two different protocols](https://doc.rust-lang.org/cargo/reference/registries.html#registry-protocols) for Registries; git and sparse.  ProGet **only supports** registries that implement the sparse protocol.  When creating a connector for your feed, you do not need to include `sparse+` before your URL, only include your URL (ex: `https://crates.io`).

### Connector Limitations
While `crates.io` support licenses in their Web API, not all third-party registries include license information.  The minimum requirements for the sparse protocol only requires a registry index that does not include license information.  This may prevent OSS Metadata updating and caching from finding licenses on third-party feeds.  

If your third-party registry requires authentication, Cargo connectors only supports the use basic authentication.

## Creating and Publishing Crates
Creating Cargo crates are [well documented](https://doc.rust-lang.org/cargo/guide/creating-a-new-project.html), but to get started, the easiest way is to use the `cargo new` command:

```bash
cargo new hello_world --bin
```

*\*`--bin` will create a program (binary) and `--lib` will create a library*

To publish this package to ProGet, there are two ways; `cargo publish` and `pgutil packages upload`.  If you have already configured cargo to authenticate your feeds, then use `cargo publish`.  If not, use `pgutil packages upload`.

To publish packages using cargo:

```bash
cargo publish --registry=proget
```

:::(Info)(Note:)
If you configured your default registry in your `.cargo/config.toml`, then you do not need to specify `--registry=proget`.
:::

To publish packages using `pgutil`:
```bash
cargo package
pgutil packages upload --feed=«feed-name» --input-file=./target/package/«package-name»-«package-version».crate
```


## Adding Packages to You Rust Program or Library
To add a cargo dependency to your Rust program or library, you will need to add the dependency to your `Cargo.toml`.

Example:

```toml
[package]
name = "my-rust-program"
version = "0.1.0"
edition = "2021"

[dependencies]
bitflags = "2.6.0"
```

If you are not using [proxying all cargo requests through ProGet](#proxy-all-cargo-requests-through-proGet), you will also need to specify to use your feed for that dependency:

```toml
[package]
name = "my-rust-program"
version = "0.1.0"
edition = "2021"

[dependencies]
bitflags = { version = "2.6.0", registry = "proget" }
```

## Installing Rust Binaries
Crates are typically implemented as libraries or programs (binaries).  If you are looking to install a program (binary) using cargo, you can do so by specifying `--registry` (if you did not configure the default registry) or by specifying the feed API url with the `--index` parameter.

Example using `--registry`:

```bash
cargo install --registry="proget" ripgrep
```

Example using `--index`:

```bash
cargo install --index="https://«proget-server»/cargo/«feed-name»" ripgrep
```
