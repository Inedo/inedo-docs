---
title: "Search Universal Package"
order: 10
---

The *Search Packages* Endpoint is not part of the Universal Feed Specification, and should not be used for anything other than querying ProGet servers in limited cases.

:::(info)
`GET /upack/«feed-name»/search?query=«search-query»&count=«max-items»`
:::

This is the what the ProGet Web Application uses to show results to users, and returns a list of packages similar to the list packages endpoint.

You're welcome to use it, but it's unsupported because its behavior is not at all documented, and we really can't think of a use-case outside of the ProGet Web Application. Don't hesitate to [contact us](https://inedo.com/contact) if this is an endpoint you would find helpful, and we can either document it better or make it more useful outside of the ProGet UI.

