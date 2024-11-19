---
layout: page
title: Audit API
hide:
  #- navigation
  - toc
---

This endpoint allows to retrieve audit information from Omnissa Access. This information is typically used to generate information about login activity with Omnissa Access, along with retrieving various audit information about SaaS Omnissa Access instance.

## HTTP Request

`GET https://[tenant location]/analytics/reports/audit/`

**Input parameters**

| Parameter | Required | Description |
| --- | --- | --- |
| tenant location | Yes (URL) | Hostname of your tenant |
| fromMillis | No (URL) | Filter events no older than this time, milliseconds since epoch, defaults to 3 days ago (now-96 hours) |
| toMillis | No (URL) | filter events no newer than this time, milliseconds since epoch, defaults to now. |
| pageSize | No (URL) | Max page size of the results, max allowed value is 5000, which is default |
| startIndex | No (URL) | Use offset to page through the results |
| objectType | No (URL)| Filter specific types of audit events or affected objects. For details, please see the table below |
| action | No (URL) | Filter event types even further by specifying what action was taken when the event was generated |

!!! Note
    To convert time to epoch milliseconds you can use various wed tools. For example, https://www.epochconverter.com/

## To query Audit API via shell

In this example I am retrieving all audit events of type LOGIN and I am restricting the time with toMillis parameter.

```shell
curl -H 'authorization: Bearer <your token here>' \
<your tenant name here>/analytics/reports/audit?/analytics/reports/audit?fromMillis=1559499975000\&objectType=LOGIN| json_pp
```

Omnissa Access will return the following as a response:

In this example we see 2 events of LOGIN type for the username admin the auth method used is password against local directory.

```json
{
   "header" : [
      "reports.dateAndTime",
      "reports.userDomain",
      "reports.Event",
      "reports.object",
      ""
   ],
   "data" : [
      [
         "1561399981109",
         "admin (System Domain)",
         "LOGIN (Password (Local Directory))",
         null,
         "{\"baseType\":\"Action\",\"uuid\":\"fe0944c3-5e69-425c-92ee-9db0b099d1aa\",\"timestamp\":1561399981109,\"organizationId\":252221,\"tenantId\":\"<your tenant id>\",\"actorId\":5723506,\"actorUserName\":\"admin\",\"actorDomain\":\"System Domain\",\"actorUuid\":\"35ac32d1-1565-4ab4-ad1a-191120540590\",\"clientId\":null,\"deviceId\":\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36\",\"workspaceId\":null,\"sourceIp\":\"208.91.2.2\",\"objectType\":\"LOGIN\",\"objectId\":null,\"objectName\":null,\"values\":{\"deviceType\":\"browser\",\"success\":\"true\",\"authMethods\":\"Password (Local Directory)\",\"actorExternalId\":null}}"
      ],
      [
         "1561357380574",
         "admin (System Domain)",
         "LOGIN (Password (Local Directory))",
         null,
         "{\"baseType\":\"Action\",\"uuid\":\"314ee065-744f-4188-b595-0aa961ff00b7\",\"timestamp\":1561357380574,\"organizationId\":252221,\"tenantId\":\"<your tenant id>\",\"actorId\":5723506,\"actorUserName\":\"admin\",\"actorDomain\":\"System Domain\",\"actorUuid\":\"35ac32d1-1565-4ab4-ad1a-191120540590\",\"clientId\":null,\"deviceId\":\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36\",\"workspaceId\":null,\"sourceIp\":\"66.170.99.1\",\"objectType\":\"LOGIN\",\"objectId\":null,\"objectName\":null,\"values\":{\"deviceType\":\"browser\",\"success\":\"true\",\"authMethods\":\"Password (Local Directory)\",\"actorExternalId\":null}}"
      ]
   ],
   "_links" : {
      "self" : {
         "href" : "/analytics/reports/audit?fromMillis=1561142523041&toMillis=1561401723041&objectType=LOGIN&pageSize=5000&startIndex=0"
      }
   },
   "headerArg" : [
      "",
      "",
      "",
      "",
      ""
   ]
}
```

## Types and actions for audit events

There are various types of audit events that can be retrieved. The table below provides a list of most common event types.

| Type | Description | Action |
| --- | --- | --- |
| LOGIN | Allows to see information about who logged in when and what auth method was used as well as details about IP address, user agent string, etc. failureMessage - the reason if the login attempt failed. authMethods - the authentication methods used. | No |
| Group | Allows to see information about group activity in IDM | Yes |
| LAUNCH | Allows to see information about who launched what applications and when. Values: success - if the launch attempt was successful. failureMessage - the reason if the launch attempt failed. resourceType - the type of application launched. | No |
| LOGIN_ERROR | Allows to see information about any launch failures. Use this action type only if the login did not fail because of wrong user input. It could be a failure because of configuration or system errors. | No |
| DyrectorySyncProfile | Allows to see information about directory sync | No |
| AppEntitlement | Allows to see information about what applications are entitled to what groups and users | Yes |
| LAUNCH_ERROR | User launched an application with an invalid request or if we could not get resource name, UUID or type. Values: failureMessage - the reason if the launch attempt failed.|   |

!!! Important
    Types are case sensitive!

For the actions, there are several types of action filters available:

| Action | Description |
| --- | --- |
| Create | Object was created |
| Update | Object was updated |
| Delete | Object was deleted |
| Link | object was linked |
| Unlink | Object was unlinked |

**Output parameters**

Depending on the objectType retrieved and the action filter specified, API can return various parameters. The table below provides the information about the most used parameters.

| Output parameter | Description |
| --- | --- |
| actorId | The userID of the user who performed the event. In the case of a system event, this the value of this attribute will be null. ActorIds are presented in integer format in the case of a user. In the case of a system service, the return will be null. |
| actorUserName | The username of the user who performed the event. In the case of a system event, the value of this attribute will be null. Expected values include both usernames of user accounts in vIDM, as well as system services. Ex: `jdoe`. |
| actorDomain | The domain of the user who performed the event. In the case of a system event, the value of this attribute will be null. In the case of a user generated event, the domain of the user will be returned. Ex: acme.com |
| actorUUid | The UUID of the user who performed the event. Return values will always be in standard UUID format. Ex:`8c2a83a2-a546-4571-b16c-ed11a8f51725` |
| objectType | ObjectType describes the category of event that was recorded. There are many object types that span both end user activities such as authentication or application launch, and admin activities like entitlements. Please, see the table above the describes various object types. |
| objectId | ObjectID represents the unique internal identifier of the object being modified or interacted with. Depending on the object name and type, different ID formats may be returned. |
| objectName | ObjectName describes the object being modified or interacted with. This can take the form of a specific user, application, configuration, or tenant. |
| objectAction | ObjectAction defines which action was taken against the object in an Audit baseType event. As an example, when updating a user or user entitlements, you would find the objectAction "UPDATE". However, in the case of baseType=Action, this attribute will not appear as the action is defined in objectType. |
| baseType | BaseType represents the type of record being returned. There are two possibilities, **Audit** and **Action**. Audit events indicate changes to the database as a result of the event. Action events are not directly related to database changes. |
| timeStamp | Time stamp for event in milliseconds UTC since Jan 1, 1970.
| uuid | Uuid for the specific audit record. This identifier is unique for each record, including multiple instances of the same type of event. Output value is standard UUID format. This value is not particularly useful except when searching for a specific, already known event record. Example UUID: `07b6c947-f025-422f-be2c-3c87b0f3c017` |
| organizationId | Unique numerical identifier for the Omnissa Access tenant. |
| tenantId | This attribute is the customers tenant identifier. The value should match the prefix of the environment URL in the following format `{tenantId}.in.wss.workspaceone.com` for IN region tenants. |
| sourceIP | This attribute outputs the sourceIP for the machine which issued the request. Values can take on either the IPv4 format. Example IPv4: `168.175.34.12` |
| authMethods | This attribute can return one or more authentication methods used for the request. The authentication methods returned correspond directly with the auth adapter labels in the Omnissa Access admin console. Multiple auth methods will be listed in comma delimited format. Example: "method1", "method2". |

The available Auth Methods that can be returned as part of Audit API response are listed below:

* Password (AirWatch Connector)
* Device Compliance (with AirWatch)
* Mobile SSO (for iOS)
* Password (Local Directory)
* Mobile SSO (for Android)
* Certificate (Cloud Deployment)
* Password - Displayed when user authenticates with an external directory using the Access connector
