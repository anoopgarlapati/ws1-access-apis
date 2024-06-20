---
layout: page
title: Getting Started
hide:
  #- navigation
  - toc
---

Welcome to the VMware Identity Services API Reference documentation. The documentation provides Getting Started information about how to set up an OAuth 2.0 client and obtain access tokens, a comprehensive API reference that includes code samples, and a list of all data structures available through the APIs.

The VMware Identity Services APIs are designed for easy configuration and provide a seamless experience for users and applications regardless of the identity provider that you use.

## About VMware Identity Services

VMware Identity Services provides centralized user management for Workspace ONE cloud services. The service provisions users and groups from cloud identity providers such as Microsoft Entra ID or Okta to Workspace ONE services and enables federated authentication to the identity provider. VMware Identity Services is only available for new Workspace ONE tenants.

VMware Identity Services supports integration with the following Workspace ONE cloud services:

- Workspace ONE Access Cloud service
- Workspace ONE UEM 2212 or later

Take a look at some use cases:

- Configure a provisioned directory of users and groups using the System for Cross-domain Identity Management (SCIM 2.0) protocol.
- Manage identity provider configuration for federated authentication into Workspace ONE services.
- Leverage centralized user management and authentication across Workspace ONE Access and Workspace ONE UEM.

## Getting Started with VMware Identity Services REST APIs

To get you started quickly, let’s dive into the necessary steps required to configure an external identity provider (IdP) with VMware Identity Services REST API for the cloud.

### Before You Start

VMware Identity Services can be integrated with the following cloud-based identity providers:

- Microsoft Entra ID
- Okta
- Any generic SCIM 2.0 identity source
- VMware Identity Services supports federated authentication against identity providers that follow OIDC (OpenID Connect) or SAML (Security Assertion Markup Language) standards.

Before you start using the API, make sure you meet the following requirements:

- You have a new Workspace ONE tenant.
- You have an administrator account in the Workspace ONE portal.
- You can log into your tenant’s Workspace ONE portal.
- You have completed the VMware Identity Services setup in the Workspace ONE portal under Accounts > End User Management > Get started. This includes enabling VMware Identity Services, configuring the integration with your third-party identity provider, and selecting the Workspace ONE services to use with the identity provider. For more information, see [Configuring User Provisioning and Identity Federation with VMware Identity Services](https://docs.vmware.com/en/VMware-Workspace-ONE/services/vmware_identity_services_ws1/GUID-520C5F74-AF32-47BF-996D-44B3891E7F4D.html).

#### Step 1 - Create OAuth 2.0 Client from the UI

See [Enabling API Access for VMware Identity Services](https://docs.vmware.com/en/VMware-Workspace-ONE/services/vmware_identity_services_ws1/GUID-65FFBE68-44E3-46F2-91BA-C094F494A81E.html).

You will need the client ID and client secret to acquire the access token in the next step.

#### Step 2 - Get access token for the OAuth 2.0 Client

An access token is required for authorization of the HTTP calls to the VMware Identity Services APIs.

Once the OAuth 2.0 client is created, obtain an access token for the client using the client credentials flow defined in [OAuth 2.0 RFC 6749, section 4.4](https://tools.ietf.org/html/rfc6749#section-4.4).

```sh
curl --location --request POST 'https://{baseURL}/acs/token' \
--header 'Authorization: Basic {base64_encoded_client_credentials}' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials'
```

Encode the client id and client secret from Step 1 in the format “{client_id}:{client_secret}” to Base64 format and use it in the “Authorization” header in the request.

The response contains the generated client’s access token (“access_token”) used for authorization in the subsequent API calls, and the number of seconds the access token is valid. When you make calls to a REST API, include the access token in the “Authorization” header with the type “Bearer”.

The access token can be reused until it expires. The token time to live (“access_token_ttl”) is set on client creation. When your access token expires, call the above API again to request a new one.

#### Step 3 - Create OAuth 2.0 Client with Identity Provider and Directory Admin RuleSet

Next, create an OAuth 2.0 client for the tenant with the IDP_AND_DIRECTORY_ADMIN rule set. A client with the IDP_AND_DIRECTORY_ADMIN rule set is allowed to perform identity provider and directory CRUD operations.

```sh
curl --location --request POST 'https://{baseURL}/acs/broker/oauth2-clients' \
--header 'Content-Type: application/vnd.vmware.horizon.manager.accesscontrol.broker.oauth2client.with.rule.sets+json' \
--header 'Accept: application/vnd.vmware.horizon.manager.accesscontrol.broker.oauth2client.with.rule.sets+json' \
--header 'Authorization: Bearer {access_token}' \
--data-raw '{
"client_id": "my-auth-grant-client1",
"grant_types": [
"client_credentials"
],
"scope": [
"admin"
],
"secret": "my-auth-grant-client1-secret",
"access_token_ttl": 10080,
"refresh_token_ttl": 525600,
"refresh_token_idle_ttl": 525600,
"rule_set_names": [ "IDP_AND_DIRECTORY_ADMIN" ]
}'
```

Replace {baseURL} with your Workspace ONE Access tenant URL and {access_token} with the access token that you obtained in Step 2 for authorization. You can set the token time to live (“access_token_ttl”) field which determines the lifespan for all access tokens acquired by the client.

#### Step 4 - Get access token for the OAuth 2.0 Client

Use this access token for subsequent API calls. Include the token in the “Authorization” header with the type “Bearer”. When the token expires, you can request a new one.

#### Step 5 (Optional) - Delete the OAuth 2.0 Client that you created from the UI

To delete the OAuth 2.0 client, follow step 4 from [Enabling API Access for VMware Identity Services](https://docs.vmware.com/en/VMware-Workspace-ONE/services/vmware_identity_services_ws1/GUID-65FFBE68-44E3-46F2-91BA-C094F494A81E.html).

Congratulations! You have set up an OAuth 2.0 client to integrate your application with VMware Identity Services.

### Request Failures

Errors are reported using standard HTTP response codes. The API returns `HTTP 4XX` status codes when the request is invalid and `HTTP 5XX` status codes when something is wrong with the server.

For authorization errors, the API returns `HTTP 401 (Unauthorized)` or `HTTP 403 (Forbidden)`. These kinds of errors are usually related to an access token and can be cleared by making sure that the token is present and hasn’t expired.

The API will fail and returns `HTTP 444 (No Response)` (with no response body) when the request `Host` header doesn’t match the server FQDN.

For all errors, the API returns an error response body that includes the error code and additional error message in the format below:

```json
"errors": [
    {
        "code": "oauth2.client.with.client.id.already.exists",
        "message": "OAuth2 Client with client id {client_id} already exists.",
        "parameters": {
            "oauth2_client_id": "{client_id}"
        }
    }
]
```

### For More Information

- [VMware Workspace ONE, VMware Identity Services](https://docs.vmware.com/en/VMware-Workspace-ONE/services/vmware_identity_services_ws1/GUID-520C5F74-AF32-47BF-996D-44B3891E7F4D.html). Overview of VMware Identity Services for Workspace ONE (SaaS).