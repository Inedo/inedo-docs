---
title: "Show User"
order: 4
---

*Show User* is available as a `pgutil` command, and will return account details of a specified User.

:::(Info) (🚀 Quick Example: Showing a User with pgutil)
This example returns the details of a User `"John Smith"`:

```bash
pgutil security users show --username="John Smith"
```
:::

## Command Specification (CLI)
The `security users show` command is used to show the details of a specified User's account.

The `--username` option is always required.

**Showing a User** requires the user name (e.g. `"John Smith"`)

```bash
pgutil security users show --username="John Smith"
```

Example output:

```plaintext
User name: John Smith
Display name: jsmith
Email: jsmith@kramerica.com
Group membership: Developers
```

Note: The User's password details will be omitted from the output.

:::(Info) (💡 Showing a user via HTTP Request)
"Show Users" is only available as a `pgutil` command, but returning the details of a user via HTTP request can be performed using the "[List Users](/docs/proget/api/security/users/list)" endpoint and filtering by the specified user. For example, to return account details of a user `"John Smith"`:

```bash
GET /api/security/users/list?search-term=John%20Smith
```
:::