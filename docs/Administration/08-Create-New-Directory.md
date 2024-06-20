---
layout: page
title: Create New Directory
hide:
  #- navigation
  - toc
---

This endpoint is responsible for creating a directory for users and groups.

Request
URL
POST
https://{api_host}/usergroup/broker/directories
COPY
Request Body
BrokerDirectoryMedia of mimetype application/vnd.vmware.vidm.usergroup.broker.directory+jsonRequired
{
    "_links": {
        "self": {
            "href": "https://example.com/path-to-self"
        }
    },
    "default_domain": "domain1",
    "delete_in_progress": false,
    "domains": [
        "domain1",
        "domain2"
    ],
    "id": "string",
    "name": "my_dir 1",
    "source": "AZURE",
    "type": "PROVISIONED"
}
Map Of Link
_linksOptional
The resource HATEOAS links. Usually includes a “self” link for this resource

String
default_domainOptional
The default domain is required when users and groups are provisioned from an external directory without a domain. If the default domain is not set when the directory is created and the domain name is not synced from the external directory, user records in the directory will not have the domain attribute associated with the record and underlying services that rely on the domain attribute may fail. Must be one of the list of domain names. Note that the field is not returned in list directories API.

Boolean
delete_in_progressOptional
If true, the directory is marked for deletion and will be deleted soon.

Array Of String
domainsOptional
List of directory domain names

String As Uuid
idOptional
The unique identifier of the directory

String
nameOptional
User provided directory name. This must be unique. The allowed symbols are letters in any language, digits (0-9), space and -_

String
sourceOptional
The type of the directory source

Possible values are: AZURE , PING , OKTA , ACCESS , GENERIC

String
typeOptional
The type of the directory

Possible values are: PROVISIONED , JIT

Authentication
This operation uses the following authentication methods.
admin
Response
Response Body
201 Created
Returns BrokerDirectoryMedia of type application/vnd.vmware.vidm.usergroup.broker.directory+json
The directory has been created.

{
    "_links": {
        "self": {
            "href": "https://example.com/path-to-self"
        }
    },
    "default_domain": "domain1",
    "delete_in_progress": false,
    "domains": [
        "domain1",
        "domain2"
    ],
    "id": "string",
    "name": "my_dir 1",
    "source": "AZURE",
    "type": "PROVISIONED"
}
Map Of Link
_linksOptional
The resource HATEOAS links. Usually includes a “self” link for this resource

String
default_domainOptional
The default domain is required when users and groups are provisioned from an external directory without a domain. If the default domain is not set when the directory is created and the domain name is not synced from the external directory, user records in the directory will not have the domain attribute associated with the record and underlying services that rely on the domain attribute may fail. Must be one of the list of domain names. Note that the field is not returned in list directories API.

Boolean
delete_in_progressOptional
If true, the directory is marked for deletion and will be deleted soon.

Array Of String
domainsOptional
List of directory domain names

String As Uuid
idOptional
The unique identifier of the directory

String
nameOptional
User provided directory name. This must be unique. The allowed symbols are letters in any language, digits (0-9), space and -_

String
sourceOptional
The type of the directory source

Possible values are: AZURE , PING , OKTA , ACCESS , GENERIC

String
typeOptional
The type of the directory

Possible values are: PROVISIONED , JIT

Errors
400
The directory definition contains invalid input.

409
The directory or domain name already exists.

Code Samples
cURL Command
 COPY# Copy and paste this cURL command into your terminal
curl -X POST -H 'Authorization: Bearer <JWT token>' -H "Content-Type: application/vnd.vmware.vidm.usergroup.broker.directory+json" -d '{}' https://{api_host}/usergroup/broker/directories
