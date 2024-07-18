---
title: "Webhook Management API"
order: 4
---

The Webhook API offers a simple mechanism for creating, modifying, and deleting webhooks in ProGet.

This API endpoint should be used instead of the [Native API Methods](/docs/proget/reference-api/proget-api-http) when possible, as they are much easier to use and will likely not change.

For security and simplicity, these endpoints require that an [API Key](/docs/proget/reference-api/proget-apikeys) is created and passed into each request.

### Webhook Object Data Specification

| Property | Format |
| --- | --- |
| `name` | A _string_ of the user-defined name of the webhook. |
| `id` | An _integer_ of the system-assigned webhook Id |
| `feedId` | An _integer_ that the webhook is associated with, or null if the webhook applies to all feeds |
| `url` | A _string_ containing the URL that the webhook will interact with when the event occurs. |
| `event` | A _string_ of the event to listen to; one of: `added`, `promoted`, `deployed`, `deleted`, or `purged` |
| `condition` | A _string_ containing the predicate expression that will be evaluated prior to running, or null if the webhook should always fire |
| `payload` | An _object_ with the following properties: <ul><li> `method` - a _string_ of the type of HTTP request to be sent; one of `GET`, `DELETE`, `POST`, `PUT`, `HEAD`, `OPTIONS`, or `PATCH`</li><li>`headers` - an _object_ with properties representing key/value pairs of the headers to be sent with the request; the values of these properties must be _string_</li><li>`content` - a _string_ that will be sent as the body of the request</li></ul> |



### List Webhooks Endpoint

GET /api/webhooks/list?key=«api-key»\[&feedId=«feed-id»\]

Lists webhooks, optionally filtering by feed. This endpoint returns a status of 200 (on success) or 403 (invalid key).

On success, an array of _Webhooks_ objects will be returned, filtered by the specified feed id.

### Delete Webhook Endpoint

DELETE or POST /api/webhooks/delete?key=«api-key»&id=«webhook-id»

Deletes the specified webhook. This endpoint returns a status of 200 (success), 400 (invalid webhook id), 403 (invalid key).

On success, the webhook is deleted.

### Create Webhook Endpoint

PUT or POST /api/webhooks/create?key=«api-key»

Creates a webhook based on the webhook JSON object specified in the body of the request. Note that if you supply a `id` property in the webhook object, it will be ignored and a new webhook will be created. This endpoint returns a status of 201 (on success), 400 (invalid webhook object), or 403 (invalid key).

On success, the specified Webhook will be created, and the system-assigned webhook id will be written to the body of the request.

### Edit Webhook Endpoint

PUT or PATCH or POST /api/webhooks/edit?key=«api-key»\[&id=«webhook-id»\]

Updates properties of a webhook based on properties specified in a webhook JSON object sent as the body of the request. A webhook id must be specified, either in the query string or as an `id` property in the webhook object. This endpoint returns a status of 200 (on success), 400 (invalid webhook object), 404 (id not found), or 403 (invalid key).

On success, the properties specified for the webhook will be updated.