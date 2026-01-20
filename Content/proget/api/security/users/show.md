---
title: "Show User"
order: 4
---

*Show User* is available as a `pgutil` command, and will return account details of a specified User.

:::(Info) (🚀 Quick Example: Showing a User with pgutil)
This example returns the details of a User `jsmith`:

```bash
pgutil security users show --username=jsmith
```
:::

## Command Specification (CLI)
The `security users show` command is used to show the details of a specified User's account.

The `--username` option is always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Showing a User** requires the user name (e.g. `jsmith`)

```bash
pgutil security users show --username="jsmith"
```

Example output:

```plaintext
User name: jsmith
Display name: John Smith
Email: jsmith@kramerica.com
Group membership: Developers
```

Note: The User's password details will be omitted from the output.

:::(Info) (💡 Showing a user via HTTP Request)
"Show Users" is only available as a `pgutil` command, but returning the details of a user via HTTP request can be performed using the "[List Users](/docs/proget/api/security/users/list)" endpoint and filtering by the specified user. For example, to return account details of a user `jsmith`:

```bash
GET /api/security/users/list?search-term=jsmith
```
:::