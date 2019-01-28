---
title: CEIP Overview
keywords: ceip,buildmaster,proget,otter,diagnostics
show-headings-in-nav: true
sequence: 800
---

Inedo has partnered with [Gibraltar Software](https://gibraltarsoftware.com) in order to integrate [Loupe](https://onloupe.com){target="_blank"} with our products as part of our customer success initiative. This program is known as the *Customer Experience Improvement Program (CEIP)*, and this opt-in program offers the following benefits:

- Improved logging and performance metrics
- Detailed error reporting
- Centralized, aggregate feature usage analysis
- Optional integration with customer-owned Loupe instances

These features allow both Inedo and our customers to: {#data-usage}

- Provide superior support through improved logging and reduced customer effort
- Determine and prioritize improvements for popular features
- Analyze performance data to reduce page load and deployment times

### Product Integration {#product-integration data-title="Product Integration"}

CEIP functionality will be available in the following product versions going forward:

- BuildMaster 6.1
- ProGet 5.2
- Otter 2.2

### How do I opt-in? {#how-do-i-opt-in data-title="How do I opt-in?"}

CEIP is disabled by default on new and existing installations. To opt-in, a product administrator may visit the Diagnostic Center of the Inedo product, and select the *Customer Experience & Improvement* tab. This page will allow customers to enable the Loupe integration.

### How does the CEIP work? {#how-does-the-ceip-work data-title="How does the CEIP work?"}

Once enabled, captured CEIP telemetry is constantly buffered to memory, then flushed to a highly-compressed local file, and periodically transmitted in aggregate to Loupe Server.

### What data is collected? {#what-data-is-collected data-title="What data is collected?"}

When CEIP is enabled, some personally-identifiable information may be collected, including (but not limited to):

- Any system name, including application, user, pipeline, plan, operation, feed, server, and environment names
- Error log and exception messages, including stack traces
- Time durations and metric counts for whole executions and individual operations
- Database stored procedure calls, counts, and time durations

### What data is not collected? {#what-data-is-not-collected data-title="What data is not collected?"}

Any data that could be deemed secure is not collected, including:

- API keys
- Secret values of resource credentials including passwords, tokens, etc.
- Server or agent configuration AES tokens, passwords, etc.
- Debug/information/warning execution log messages
- Custom operation properties marked `[Persistent(Encrypted = true)]` or of `SecureString` type

### How is the CEIP data handled? {#how-is-the-ceip-data-handled data-title="How is the CEIP data handled?"}

This data is strictly used for the purposes mentioned in the [overview section](#data-usage) of this documentation. We will never share or sell information we collect with any third-parties other than Gibraltar Software in the context of the Loupe Server product, unless explicitly instructed by an authorized representative of the customer organization. For more information on how Loupe handles this data, visit the [Loupe Service Privacy Policy](https://onloupe.com/about/loupe-service-privacy-policy){target="_blank"}.
