---
layout: page
title: Authentication
hide:
  #- navigation
  - toc
---

In order to start querying Omnissa Access APIs, you need to obtain access token. To do that, you need to perform the following steps:

1. Register API access to obtain Client Secret
2. Obtain Access Token

Let's look into these steps in detail.

## 1. Register API access

Before accessing the API you need to have a valid API access token. In order to obtain the access token, you first need to create a service client in Omnissa Access.

* In the Omnissa Access admin console, go to `Settings` -> `OAuth 2.0 Management`.  
* Click on the `Clients` menu and click `Add Client`.
* Select `Service Client Token` as the Access type.  
* Enter a client ID, select `Admin` scope and select appropriate Admin Roles required for accessing the APIs. To be able to execute all APIs, select `Super Admin` role.  
* Click `Save`

![OAuth2.0 Management Interface](oauth2-client-for-api-access.png)

You will get the Shared Secret that you can save to obtain the access token.  
Example of client secret - `fFjs8tGiloQD5ze4pL42EV6s0mufGrOG`

## 2. Obtain Access Token

Once you have Client Secret, you can generate access token. Access token will then let you query various Omnissa Access API endpoints.

**HTTP request**

```
POST https://[tenant_url]/SAAS/auth/oauthtoken
```

**Input parameters**

| Parameter                         | Required     | Description                                                               |
|-----------------------------------|--------------|---------------------------------------------------------------------------|
| tenant_url                        | Yes (URL)    | Hostname of your tenant                                                   |
| grant_type=client_credentials     | Yes (Body)   | Required to receive the access token                                      |
| application/x-www-form-urlencoded | Yes (Header) | Format of the encoding                                                    |
| Authorization: Basic              | Yes (Header) | You need to pass the Base64 encoded string of the client_id:Client_secret |

For Authorization header, you need to Base64 encode the value of your client_id:client_secret. As an example, let's say:

You have `my_client:fFjs8tGiloQD5ze4pL42EV6s0mufGrOG`
Then Base64 encoded value is `bXlfY2xpZW50OmZGanM4dEdpbG9RRDV6ZTRwTDQyRVY2czBtdWZHck9H`
To generate Base64 encoded values, you can use the following online resource [Base 64 Encoding Tool](https://www.base64encode.org/).

> Note: The access token will have a time to live value of 3 hours by default. Once the token expires, you will need to get new access token.

To request an access token via shell:

```shell
curl –X POST <your tenant name here> \
-H 'authorization: Basic Y3VybDpmRmpzOHRHaWxvUUQ1emU0cEw0MkVWNnMwbXVmR3JPRw==' \
-H 'content-type:application/x-www-form-urlencoded' \
-d 'grant_type=client_credentials'
```

Omnissa Access will return the following as a response:

```json
{
    "access_token":"<your token here>",
    "token_type":"Bearer",
    "expires_in":10799,
    "refresh_token":"<your token here>",
    "scope":"admin"
}
```
## 3. Refresh Access Token

The Access Token has limited lifetime. If your Token expires, you should request a new Access Token using the Refresh Token.

**HTTP Request**

`POST https://[tenant location]/SAAS/auth/oauthtoken`

**Input parameters**

The following parameters need to be specified in the of the request, using the `application/x-www-form-urlencoded` format:

| Parameter | Required | Description |
| --- | --- | --- |
| **grant_type=refresh_token** | Yes (Body) | Required parameter |
| **refresh_token** | Yes (Body) | The Refresh Token sent back by Identity Manager during the first Access Token Request. |
| **scope** | No (Body) | The list of scopes separated by space and URL encoded. |
| **Authorization: Basic [base64Encode(clientId + ":" + secret)]** | Yes (Header) | To create your Basic Credentials, create a string with your app’s Client ID and Client Secret, separated by a colon ( : ) |
| **Content_Type** | Yes (Header) | Content-Type: application/x-www-form-urlencoded |

For **Authorization header**, you need to Base64 encode the value of your `client_id:client_secret`. As an example, let's say:

* You have `curl:fFjs8tGiloQD5ze4pL42EV6s0mufGrOG`
* Then Base64 encoded value is `Y3VybDpmRmpzOHRHaWxvUUQ1emU0cEw0MkVWNnMwbXVmR3JPRw==`

To refresh the access token via shell:

```shell
curl –X POST <your tenant name here> \
-H 'authorization: Basic <your token here>' \
-H 'content-type:application/x-www-form-urlencoded' \
-d 'grant_type=client_credentials' \
-d 'refresh_token=<your token here>'
```

Omnissa Access will return the following in response:

```json
{"access_token":"<your token here>",
"token_type":"Bearer",
"expires_in":10799,
"refresh_token":"<your token here>",
"scope":"admin"}
```
