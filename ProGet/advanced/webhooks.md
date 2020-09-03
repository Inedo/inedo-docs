---
title: Webhooks
subtitle: Webhooks
sequence: 500
keywords: proget, webhooks
---

Webhooks let you connect ProGet with other services to notify users, trigger automated workflows, or audit certain events. For example, you could

{.docs}
- Send an instant message when a package is added to a feed
- Deploy a package with [BuildMaster](/docs/buildmaster) once it's promoted to a different feed
- Log a message in a third-party auditing tool when a package is deleted

This is done by creating webhooks that will send HTTP-based notifications (payloads) when certain events like package publishing, deployment, and deletion occur. These payloads can be adapted to fit into nearly any other tool's API, including workflow automation services such as Zappier, Microsoft Flow, etc.

This feature is available in paid and trial ProGet editions. {.info}

## Configuring Webhooks {#configuring data-title="Configuring Webhooks"}

Webhooks are configured via the Administration > Webhooks page, and can be configured for all feeds or individual feeds. Multiple webhooks can be specified, but the order of invocation is not guaranteed.

:::attention{.analogy}
Usage Note: Webhooks are intended for basic notification and triggering purposes only. Do not attempt to "chain" multiple webhooks to orchestrate a series of sequential events; it may just happen to work in testing, but the order of webhook invocation is not predictable and may occur in parallel.
:::

A webhook may be associated with a feed, and has the following properties:

{.docs}
- **Id** – a system-generated unique identifier
- **Name** - a non-unique identifier used to distinguish webhooks in the UI and logs
- **Event Type** - one of the [event types](#event-types)
- **URL** - the URL to send the request to
- **Conditional** - an expression that is evaluated to see whether the webhook should run
- **Custom payload** - customize the request that is sent instead of the [default payload](#default-payload)
  - **Method** - one of GET, DELETE, POST, PUT, HEAD, OPTIONS, PATCH
  - **Headers** - a new-line separate list of HTTP headers
  - **Body** - the body of the request that will be sent

### Event Types {#event data-title="Event Types"}

Webhooks can be configured for the following events:

{.docs}
- **added** - a package is added to a feed using the API, web interface, or a drop-path
- **promoted** - a package is promoted using the API or web interface
- **deployed** - a deployment record is added; note that this occurs when certain HTTP headers are submitted during an API-based package download
- **deleted** - a package is deleted from a feed using the API or web interface
- **purged** - a package was deleted because of a retention policy

### Conditional {#conditional data-title="Conditional"}

The conditional is an optional expression that is evaluated prior to a webhook being invoked. This takes the same format as an [OtterScript predicate expression](/docs/various/execution-engine/otterscript/formal-specification#predicate-statement), which has a standard unary operator (<code>!</code>),equality operators (<code>==</code> and <code>!=</code>), boolean comparison (<code>&amp;&amp;</code> and <code>||</code>), and parenthesis.

Following are some example expressions and their results.

| Expression | Result |
|---|---|
| `$FeedType == NuGet` | If the feed type is NuGet, the webhook will execute; note that `nuget` will always evaluate to false; because it's a case sensitive comparison |
| `true` | The webhook will always execute |
| `!true` | The webhook will never execute |
| `true && false` | The webhook will never execute |
| `$CustomVar == true` | If `$CustomVar` is a defined variable, and it's "true", then the webhook will execute; otherwise, an error will occur if variable is not defined

### Default Payload {#default data-title="Default Payload"}

By default, ProGet will POST a simple JSON-based object, with key/values containing package information and event type. Note that these use [variables](#variables) to send package-specific data.

#### Headers: {#headers: data-title="Headers:"}

```
  Content-Type=application/json
```

#### Content {#content data-title="Content"}

```
$ToJson(%(
    feed: $FeedName,
    package: $PackageId,
    version: $PackageVersion,
    hash: $PackageHash,
    packageType: $FeedType,
    event: $WebhookEvent,
    user: $UserName
))
```

### Workflow Automation Services {#workflow data-title="Workflow Automation Services"}

When a basic HTTP request isn't enough to perform a particular task, you can use third-party workflow automation services to act as a sort of bridge between ProGet and the tools you want to automate.

There are lots of hosted and on-prem workflow automation services on the market – Zapier, IFTT, Microsoft Flow, elastic.io, and so on – and while they all function in a slightly different way, they'll all integrate with ProGet in just about the same way: ProGet will send an HTTP request to the automation service, and the automation service will trigger some workflow that may dispatch additional requests to other APIs.

#### Example: Zapier {#example: data-title="Example: Zapier"}

In Zapier, automation workflows are called Zaps. When you create a new Zap with a webhook-enabled trigger, you'll receive a new URL that looks something like this:

```
https://zapier.com/hooks/catch/n/Lx2RH/

```

Zappier prides itself on making things "just work" when it comes to webhooks, and since ProGet's [default payload](#default-payload) is a simple JSON-based key/value pair, you can use that to get started.

After entering the Zap URL on a webhook for a feed, ProGet will start sending data to Zapier when the selected event occurs. Zappier will parse the fields (feed, group, name, version, etc.) automatically.

From there, you can use those values for outbound integrations. See [Zapier's Webhook Documentation](https://zapier.com/help/webhooks/) for more details and troubleshooting.

### Authentication to Other Services {#authentication data-title="Authentication to Other Services"}

While all APIs manage authentication a bit differently, you're usually provided with at least one of two options: using an API key, or basic access authentication. Both of these authentication methods can be done within a Webhook, though you might need to use a custom payload.

API keys are often sent on the query string, message body, or in an HTTP header. You can add these wherever appropriate in a webhook.

[Basic access authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) is actually just an HTTP header. You can include it in the headers section of the webhook, as a line like this:

```
Authorization: Basic $EncodeBasicAuth(username,password)
```

In both cases, it's a good practice to use [variables](#variables) to store keys, names, and passwords. Although this doesn't secure them (they are always sent as plain text over HTTP/S), it will often obscure them from causal viewing and make it easier to reuse in different webhooks.

### Variables {#variables data-title="Variables"}

You can use variables to combine static configuration information (such as API keys), context-specific data (like user name), and package metadata (such as version numbers) to create all sorts of custom payloads.

#### Configuration Variables {#configuration data-title="Configuration Variables"}

Configuration variables are static, key/value pairs that can be defined either globally, or on a feed. If you define a feed-level variable with the same name as a global variable, then the feed-level variable will be used when events occur in the context of that feed.

Variable names must be no more than fifty characters: numbers (`0-9`), upper- and lower-case letters (`a-Z`), dashes (`-`), and underscores (`_`); must start with a letter, and may not start or end with a dash or underscore.

#### Variable Functions {#variable data-title="Variable Functions"}

You may have seen expressions like `$JSEncode($PackageGroup)` in the [default payload](#default-payload); these are variable functions, which take in a number of parameters and return a value. All of the "built-in variables" like `$FeedName`, `$PackageVersion`, and `$Date` are implemented as variable functions.

You can create configuration variables with the same name of a variable function, but we don't recommend it because it can get quite confusing when things like `$FeedName` don't return the actual feed name. If there is a configuration variable of the same name, that value will be used instead, unless you explicitly reference the parameter list (such as `$FeedName()`).

Variable functions are extensible, so you can write your own with an [Inedo Extension](/docs/proget/administration/extensions).

#### Reading Package Metadata {#reading data-title="Reading Package Metadata"}

Variable functions are used to extract metadata from packages. There are several built-in functions that allow you to access common metadata across all packages: `$PackageName`, `$PackageVersion`, etc. You can also use the `$PackageProperty()` function.

For example, when `$PackageProperty(_sourceTarget)` is used on a universal package, the `_sourceTarget` property is returned. If there is no such property, then an error will be logged and the webhook execution will not occur.

However, if you specify the second argument `(defaultText)` to the `PackageProperty` function, then it will succeed. That is, `$PackageProperty(_sourceTarget, unspecified)` will return the value of *_sourceTarget* or *unspecified*.

#### Escaping `$` and Unresolvable Variables {#escaping data-title="Escaping $ and Unresolvable Variables"}

Anything that "looks" like a variable – i.e. text that starts with a `$`, then a character – will be parsed as a variable expression, and evaluated. If a configuration variable or variable function cannot be found, then an error will be logged and the request will not complete. This is usually what you'd want, and will help you track down a typo like `$PackagName`.

However, if you actually want a `$`-character somewhere in your payload, you'll need to escape it using a grave apostrophe (\`) like this: `` `$ ``. If you want to use a grave apostrophe, then you'll also need to escape it like this: ` `` `. Whitespace character expansion is also available with `` `r `` , `` `n ``, and `` `t ``.

### Built-in Variables {#built-in data-title="Built-in Variables"}

| Variable | Result |
|---|---|
| `$WebhookName` | returns the name of the currently running webhook |
| `$WebhookId` | returns the id of the currently running webhook |
| `$WebhookEvent` | returns the name of the event |
| `$FeedId` | returns the id of the feed in context |
| `$FeedName` | returns the name of the feed in context |
| `$FeedType` | returns the type of the feed in context; NuGet, Chocolatey, PowerShell, {…} |
| `$Date(format)` | same description |
| `$PackageGroup` | returns the package's group name, or empty |
| `$PackageName` | returns the package's name or empty if it only has an Id |
| `$PackageId` | returns the package's Id |
| `$PackageVersion` | returns the package version |
| `$PackageHash` | returns the package hash, if it had one computed already |
| `$PackageProperty(name, default)` | returns an arbitrary property from the metadata; when default is specified, that text is returned in place of an error occurring when a property doesn't exist |
| `$Username` | returns the name of the user who initiated the event; this will be SYSTEM if it was triggered by the service (a drop path, for example) or Anonymous |
| `$EncodeBasicAuth(username,password)` | returns the base64 string used in basic auth requests |
| `$JSEncode(s)` | returns a value that has been encoded for use in a javascript/JSON string |
| `$Coalesce(s, …)` | returns the first non-whitespace value from the arguments |

### Error Handling and Debugging {#error data-title="Error Handling and Debugging"}

ProGet will not wait for a response from the external server to continue. When an error does occur (either from bad HTTP status codes or network-level issues), it will be saved to ProGet's Diagnostics Center.  You can view these errors by navigating to _Administration -> Diagnostics Center_ and then click the `View Messages` button.  There is also a Category filter to only show webhook messages.

Note that, some errors -- such as variables being unable to be replaced, or an invalid URL -- will cause a HTTP request to not be made.

Webhook requests may be made from either the web or service, depending where the event occurred. For example, a package promote request will always come from the web node that actually received the request, whereas a package add request may come from the web node (web ui or API) or the service (drop path).
