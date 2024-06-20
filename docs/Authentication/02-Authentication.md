---
layout: page
title: Authentication
hide:
  #- navigation
  - toc
---

The VMware Identity Services Workspace ONE API supports the following methods to authenticate requests. Individual operations in the documentation will include their specific authentication types.

## admin

A Bearer token created from OAuth2 client having an admin scope

**Type** : http  
**Scheme**: bearer  
**BearerFormat** : JWT


## basic_auth

The HTTP Basic authentication scheme. The ‘Authorization’ header is formed using ‘Basic ’ + base64Encode(client_id + ‘:’ + client_secret)

**Type** : http  
**Scheme**: basic  
