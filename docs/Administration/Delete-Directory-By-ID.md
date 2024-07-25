---
layout: page
title: Delete Directory By ID
hide:
  #- navigation
  - toc
---

This endpoint is responsible for deleting a directory by ID. This includes the deletion of all associated OAuth 2.0 clients, sync client configurations, users, groups and the directory.

## Request

### URL

<span style="color:red">DELETE</span> https://{api_host}/usergroup/broker/directories/<span style="color:purple">{id}</span>


### Path Parameters

> String
> 
> **id**    <span style="color:red">Required</span>

The ID of the directory


## Authentication

This operation uses the following authentication methods.

[admin](../Authentication.md#admin)

### Response
`202 Accepted`

The directory was successfully deleted.

!!!
    Operation doesn't return any data structure.

### Errors
`404`

The directory was not found.

## Code Samples

### cURL Command

```sh
# Copy and paste this cURL command into your terminal
curl -X DELETE -H 'Authorization: Bearer <JWT token>' https://{api_host}/usergroup/broker/directories/{id}
```
