---
title: "CEIP Overview"
order: 2
---

Our software products are driven by our users. We rely on your feedback to not only improve quality, reliability, and performance, but to understand the features you are actually using (and how you're using them) so we can build better software for you.

The easiest way to have your voice heard is by opting-in to our Customer Experience Improvement Program (CEIP). We partnered with the CEIP experts at [Gibraltar Software](https://gibraltarsoftware.com), and use their [Loupe](https://onloupe.com){target="_blank"} products to offer you the the following benefits:
- Improved logging and performance metrics
- Detailed error reporting
- Centralized, aggregate feature usage analysis
- Optional integration with customer-owned Loupe instances

These features allow both Inedo and our customers to: {#data-usage}

- Provide superior support through improved logging and reduced customer effort
- Determine and prioritize improvements for popular features
- Analyze performance data to reduce page load and deployment times

### Product Integration 

CEIP functionality will be available in the following product versions going forward:

- BuildMaster 6.1
- ProGet 5.2
- Otter 2.2

### How do I opt-in? 

CEIP is disabled by default on new and existing installations. To opt-in, a product administrator may visit the Diagnostic Center of the Inedo product, and select the *Customer Experience & Improvement* tab. This page will allow customers to enable the Loupe integration.

### How does the CEIP work? 

Once enabled, captured CEIP telemetry is constantly buffered to memory, then flushed to a highly-compressed local file, and periodically transmitted in aggregate to Loupe Server.

### What data is collected?

When CEIP is enabled, some personally-identifiable information may be collected, including (but not limited to):


- Any system name, including application, user, pipeline, plan, operation, feed, server, and environment names
- Error log and exception messages, including stack traces
- Time durations and metric counts for whole executions and individual operations
- Database stored procedure calls, counts, and time durations

### What data is not collected?

Any data that could be deemed secure is not collected, including:


- API keys
- Secret values of resource credentials including passwords, tokens, etc.
- Server or agent configuration AES tokens, passwords, etc.
- Debug/information/warning execution log messages
- Custom operation properties marked `[Persistent(Encrypted = true)]` or of `SecureString` type

### How is the CEIP data handled?

This data is strictly used for the purposes mentioned in the [overview section](#data-usage) of this documentation. We will never share or sell information we collect with any third-parties other than Gibraltar Software in the context of the Loupe Server product, unless explicitly instructed by an authorized representative of the customer organization. For more information on how Loupe handles this data, visit the [Loupe Service Privacy Policy](https://onloupe.com/about/loupe-service-privacy-policy){target="_blank"}.