---
title: "Customizing Content & Webhooks"
order: 1
---

[Notifications & Webhooks](/docs/proget/administration/proget-notifications-webhooks) is a powerful feature that goes beyond basic email, Slack, and Teams notifications. With customizable content and custom webhooks, you can connect ProGet with other tools to trigger automated workflows, audit certain events, etc.  The tools may not even need webhook functionality, as ProGet can [authenticate to their API](#authentication-api) and send customized content (payloads).

:::(Info) (🔒 ProGet Enterprise Feature)
ProGet Notifiers and Webhooks are not available in ProGet Free Edition, and ProGet Basic Edition supports only custom webhooks (not emails). See [License Restrictions](/docs/proget/administration/license) to learn more.
:::


## Customizing Email & Webhook Content

Notifiers use [OtterScript Text Templating](/docs/executionengine/overview/text-templating), which allows you to combine plain text, variables, and code to render the output you'd like.

As a simplistic example, if you wanted to spice up the default Package Event email body, you could customize the text with something like this:

```
Great news, $PackageId $PackageVersion has just been promoted to $FeedName!!
Thanks to $UserName for pressing the Promote button.
```

More practically, you can use these Custom Webhooks to integrate into just about any tool that can receive webhooks or has an HTTP-based API. Simply format the Custom Webhook's header/content to match that tool's specification and let ProGet

See the [Defaults and Examples](#examples) to learn how you can customize content. Also, keep in mind that you may need to escape certain characters (see [Escaping and Unresolvable Variables](#escaping)).


## Variables & Expressions

Variables & Expressions can be incorporated into email and webhook content to reuse notifiers across feeds, extract specific information about packages and vulnerabilities, and create advanced data structures or reports. They can also be used in *advanced expressions* to control whether a notification should be dispatched.

![proget-notifiers-variables](/resources/docs/proget-notifiers-variables.png){height="" width="50%"}

There are three categories of Variables & Expressions, and they will vary from version to version of ProGet as we add to the list from user feedback. You can see the full list under Admin > Notifications & Webhooks > Variables & Expressions.

### Built-in Variables & Expressions

Most of the Built-in Variables & Expressions provide access to context-specific data (e.g. `$UserName`, `$EventName`, `$FeedName`, etc.) and package-specific data (e.g. `$PackageName`, `$PackageVersion`). Some act as functions that take in a number of parameters and return a value, such as  `$PackageHash(format, algorithm)` and `$PackageProperty(propName, defaultValue)`.

A handful of these built-ins will return a list of items (e.g. `@AffectedPackages` and `@BuildIssues(includeClosed)`), and require using an OtterScript expression to display.

### Custom Variables

You can also create your own variables for static configuration information like third-party API keys. These can be defined either globally, or scoped to a feed. If you define a feed-level variable with the same name as a global variable, then the feed-level variable will be used when events occur on that feed.

#### Variable Naming

Custom variable names must be no more than fifty characters: numbers (`0-9`), upper- and lower-case letters (`a-Z`), dashes (`-`), and underscores (`_`); must start with a letter, and may not start or end with a dash or underscore.

::: (Info) (⚙ Best Practice for Naming)
Although you can create custom variables that "override" a built-in, we don't recommend it because it's confusing; for example, when `$FeedName` doesn't return the actual feed name, other users (or your future self) may get confused. 
:::

Note that you can always explicitly reference a built-in variable with a parameter list (such as `$FeedName()`).

#### List and Map Variable Types

You can *technically* create list- and map-type variables (e.g. `@(foo,bar)` and `%(key1: foo, key2: bar)`), but we really can't think of a use case for doing so. Please let us know if you've got any ideas, so we can document and explain it better.

### OtterScript Variables & Expressions

If you select "Everything" on the "Display" filter on the Variables & Expressions page, you'll see a *lot* more Variables & Expressions. These are mostly  "variable functions" in OtterScript, and you can technically write your own with an [Inedo Extension](/docs/proget/administration/extensions). [Let us know](https://inedo.com/support) if you plan to do that - we'd love to hear about your use case!

You probably won't find these very useful in ProGet, but some are used behind-the-scenes to create the default content for emails and webhooks. They're also used to implement "Run conditionally" on a notifier.

You'll see `$ToJson(Data)` used extensively to convert a "map expression" into JSON, mostly because it means less `{` and `"` characters and is easier to expand lists. You'll also see `<% foreach %>` used to  iterate over vulnerability and issue lists.

Examples and Default Content <a name="examples"></a>  
<!--
* default-email-new-vulnerability-detected
* default-email-package-events
* default-email-issues-opened-on-build
* default-webhook-new-vulnerability-detected
* default-webhook-package-events
* default-webhook-issues-opened-on-build
-->

<h3 id="default-conditional-expressions">Sample Advanced Expressions (Conditions)</h3>

Behind-the-scenes, the Conditions you can select for a notifier are implemented with OtterScript. Following are examples that would be generated based on data entered in the fields.

* `$Compare($VulnerabilityScore,>=,7.5)`
* `$MatchesRegex($PackageName, "^MyPackage\.*$")`
* `$MatchesRegex($PackageName, "^MyPackage\.*$") && $MatchesRegex($PackageVersion, "^3\.0\.*$")`

<h3 id="default-email-new-vulnerability-detected">Default Email Content: New Vulnerability Detected</h3>

<table><tr><th>Subject</th><td><pre>
$PackageId $PackageVersion: Vulnerability $VulnerabilityId
</pre></td></tr><tr><th>Body</th><td><pre>
A new vulnerability has been published: $VulnerabilityId $VulnerabilitySummary

Affected packages:

<% foreach %p in @AffectedPackages { %>
  \* $(%p.Name) $(%p.AffectedVersions)
<% } %>
</pre></td></tr></table>

<h3 id="default-email-package-events">Default Email Content: Package Events</h3>

<table><tr><th>Subject</th><td><pre>
$PackageId $PackageVersion $PackageEvent to $FeedName by $UserName
</pre></td></tr><tr><th>Body</th><td><pre>
$PackageId $PackageVersion $PackageEvent to $FeedName by $UserName
</pre></td></tr></table>

<h3 id="default-email-noncompliant-package">Default Email Content: Noncomplaint Package Detected</h3>

<table><tr><th>Subject</th><td><pre>
$PackageId $PackageVersion is $PackageComplianceResult
</pre></td></tr><tr><th>Body</th><td><pre>
$PackageId $PackageVersion is $PackageComplianceResult in $FeedName.

Reason: $PackageComplianceDetails
</pre></td></tr></table>

<h3 id="default-email-issues-opened-on-build">Default Email Content: Issues Opened on Build</h3>

<table><tr><th>Subject</th><td><pre>
Issues detected in $ProjectName $BuildNumber
</pre></td></tr><tr><th>Body</th><td><pre>
Issues were detected in $ProjectName $BuildNumber

<% foreach %i in @BuildIssues { %>
 \* $(%i.Sequence) $(%i.Detail)
<% } %>

To view issues, see $WebBaseUrl/projects/build/issues?projectBuildId=$BuildId&resolved=Unresolved
</pre></td></tr></table>
    
<h3 id="default-webhook-new-vulnerability-detected">Default Webhook Content: New Vulnerability Detected</h3>

<table><tr><th>OtterScript</th><th>Example Output</th></tr>
<tr><td><pre>
$ToJson(%(
    packages: @AffectedPackages,
    vulnerabilityId: $VulnerabilityId,
    summary: $VulnerabilitySummary,
    severity: $VulnerabilitySeverity,
    score: $VulnerabilityScore
))
</pre></td><td><pre>
{
  "packages": [ {"Name": "nuget:Newtonsoft.Json", "AffectedVersions":"&lt; 13.0.1." } ]
  "vulnerabilityId": "PGV-2245804",
  "summary": "Improper Handling of Exceptional Conditions in Newtonsoft.Json",
  "severity": "High",
  "score": "7.5"
}
</pre></td></tr></table>

<h3 id="default-webhook-package-events">Default Webhook Content: Package Events</h3>

<table><tr><th>OtterScript</th><th>Example Output</th></tr>
<tr><td><pre>
$ToJson(%(
    package: $PackageId,
    version: $PackageVersion,
    event: $PackageEvent,
    user: $UserName,
    feed: $FeedName
))  
</pre></td><td><pre>
{
  "package": "MyCorp.CoolUtils",
  "version": "3.2.0-pre.1",
  "event": "Promoted",
  "user": "API",
  "feed": "internal-utils"
}
</pre></td></tr></table>
    
<h3 id="default-webhook-noncompliant-package">Default Webhook Content: Noncompliant Package Detected</h3>
    
<table><tr><th>OtterScript</th><th>Example Output</th></tr>
<tr><td><pre>
$ToJson(%(
    package: $PackageId,
    version: $PackageVersion,
    result: $PackageComplianceResult,
    details: $PackageComplianceDetails,
    feed: $FeedName
))
</pre></td><td><pre>
{
  "package": "MyCorp.CoolUtils",
  "version": "3.2.0-pre.1",
  "result": "Noncompliant",
  "details": "Vulnerability (PGV-2358804); Package is Unlisted",
  "feed": "internal-utils"
}
</pre></td></tr></table>

<h3 id="default-webhook-issues-opened-on-build">Default Webhook Content: Issues Opened on Build</h3>

<table><tr><th>OtterScript</th><th>Example Output</th></tr>
<tr><td><pre>
$ToJson(%(
    issues: @BuildIssues,
    buildNumber: $BuildNumber,
    releaseNumber: $ReleaseNumber,
    projectName: $ProjectName
))
</pre></td><td><pre>
{
  "issues": [ 
    {"Sequence": "1", "Detail":"Noncompliant package (PGV-892418)" }, 
    {"Sequence":"4", "Detail":"Noncompliant package (MIT License)"}  ] ,
  "buildNumber": "25.4.2",
  "releaseNumber": "v25-Q4",
  "projectName": "XAPUtil.Web"
  
}
</pre></td></tr></table>

## TIP: Workflow Automation & Orchestration <a name="workflows"></a>      

When a basic HTTP request isn't enough to perform a particular task, you can use third-party workflow automation services to act as a sort of bridge between ProGet and the tools you want to automate.

:::(INFO) (⚙ Best Practices: Basic Notification & Triggering Only)
Custom webhooks are intended for basic notification and triggering purposes only. Do not attempt to "chain" multiple webhooks to orchestrate a series of sequential events; it may just happen to work in testing, but the order of webhook invocation is not predictable and may occur in parallel. Some may also not fire due to temporary outages, network issues, etc.
:::

There are lots of hosted and on-prem workflow automation services on the market – Zapier, IFTT, Microsoft Flow, elastic.io, and so on – and while they all function in slightly different ways, they'll all integrate with ProGet in just about the same way: ProGet will send an HTTP request to the automation service, and the automation service will trigger some workflow that may dispatch additional requests to other APIs.

### Example: Zapier and ProGet

In Zapier, automation workflows are called Zaps. When you create a new Zap with a webhook-enabled trigger, you'll receive a new URL that looks something like this:

```
https://zapier.com/hooks/catch/n/Lx2RH/

```

Zappier prides itself on making things "just work" when it comes to webhooks, and since ProGet's [default payload](#default-payload) is a simple JSON-based key/value pair, you can use that to get started.

After entering the Zap URL on a webhook for a feed, ProGet will start sending data to Zapier when the selected event occurs. Zappier will parse the fields (feed, group, name, version, etc.) automatically.

From there, you can use those values for outbound integrations. See [Zapier's Webhook Documentation](https://zapier.com/help/webhooks/) for more details and troubleshooting.


## TIP: Handling Authentication to Other Services <a name="authentication"></a>  

While all APIs manage authentication a bit differently, you're usually provided with at least one of two options: using an API key, or basic access authentication. Both of these authentication methods can be done within a custom webhook, though you might need to use a custom payload.

API keys are often sent on the query string, message body, or in an HTTP header. You can add these wherever appropriate in a webhook.

[Basic access authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) is actually just an HTTP header. You can include it in the headers section of the webhook, as a line like this:

```
Authorization: Basic $EncodeBasicAuth(username,password)
```

In both cases, it's a good practice to use [variables](#variables) to store keys, names, and passwords. Although this doesn't secure them (they are always sent as plain text over HTTP/S), it will often obscure them from causal viewing and make it easier to reuse in different webhooks.

## TIP: Reading Package Metadata <a name="package-metadata"></a>     

One of the most powerful built-ins is the `$PackageProperty()` function. This allows you to read any arbitrary metadata from the package in context.

For example, when `$PackageProperty(_sourceTarget)` is used on a universal package, the `_sourceTarget` property is returned. If there is no such property, then an error will be logged and the notification will not be dispatched.

However, if you specify the second argument `(defaultText)` to the `PackageProperty` function, then it will succeed. That is, `$PackageProperty(_sourceTarget, unspecified)` will return the value of *_sourceTarget* or *unspecified*.

## TIP: Escaping and Unresolvable Variables <a name="escaping"></a>
    
Anything that "looks" like a variable – i.e. text that starts with a `$`, then a character – will be parsed as a variable expression, and evaluated. If a custom or built-in variable cannot be found, then an error will be logged and the notification will not be dispatched complete. This is usually what you'd want, and will help you track down a typo like `$PackagName`.

However, if you actually want a `$`-character somewhere in your content, you'll need to escape it using a grave apostrophe (<code>\`</code>) like this: <code>\`$</code>.  If you want to use a grave apostrophe, then you'll also need to escape it like this: <code>\`\`</code>. Whitespace character expansion is also available with <code>\`r</code>, <code>\`n</code>, and <code>\`t</code>.

