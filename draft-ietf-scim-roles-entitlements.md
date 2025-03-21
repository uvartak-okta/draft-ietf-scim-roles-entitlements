---
title: "SCIM Roles and Entitlements Extension"
abbrev: "SCIM Roles and Entitlements Extension"
docname: draft-ietf-scim-roles-entitlements-latest
category: std

ipr: trust200902
area: IETF
workgroup: SCIM
keyword: [Internet-Draft, SCIM]

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    name: Danny Zollner
    organization: Microsoft
    email: zollnerd@microsoft.com
 -
    name: Unmesh Vartak
    organization: Okta
    email: unmesh.vartak@okta.com


normative:
  RFC7643:

informative:
  RFC7642:
  RFC7644:


--- abstract

The System for Cross-domain Identity Management (SCIM) protocol's schema RFC [[RFC7643]] defines the complex core schema attributes "roles" and "entitlements". For both of these concepts, frequently only a predetermined set of values are accepted by a SCIM service provider. The values that are accepted may vary per customer or tenant based on customizable configuration in the service provider's application or based on other criteria such as what services have been purchased. This document defines an extension to the SCIM 2.0 standard to allow SCIM service providers to represent available data pertaining to roles and entitlements so that SCIM clients can consume this information and provide easier management of role and entitlement assignments.


--- middle

# Introduction

The System for Cross-domain Identity Management (SCIM) protocol's schema RFC [[RFC7643]] defines the complex core schema attributes "roles" and "entitlements". For both of these concepts, frequently only a predetermined set of values are accepted by a SCIM service provider. Available roles and entitlements may change based on a variety of factors, such as what features are enabled or what customizations have been made in a specific instance of a multi-tenant application. The core SCIM 2.0 RFC documents ([[RFC7642]], [[RFC7643]] and [[RFC7644]]) do not provide a method for retrieving the available roles or entitlements as part of the SCIM 2.0 standard.

In order to allow for SCIM clients to avoid easily predictable errors when interacting with SCIM service providers, this document aims to provide a method for SCIM service providers to provide data on what roles and/or entitlements are available so that SCIM clients can consume this data to more efficiently manage resources between directories.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Roles and Entitlements

The Roles and Entitlements SCIM Extension consists of two new resource types, `/Roles` and `/Entitlements`, as well as accompanying `ServiceProviderConfig` details to advertise support for this extension. In addition to the new resource types, service providers can use schema extensions to publish custom entitlements.


## ServiceProviderConfig Extension

SCIM endpoints that have implemented one or both of the endpoints from this extension MUST advertise which elements are implemented in the ServiceProviderConfig endpoint as defined:

    RolesAndEntitlements
        A complex type that specifies Roles and Entitlements extension
        configuration options. REQUIRED.

        roles
            A complex type that specifies configuration options
            related to the Roles resource type. REQUIRED.

            supported
                A boolean type that indicates if the SCIM service
                provider supports the /Roles endpoint defined
                in this extension. REQUIRED.

            multipleRolesSupported
                A boolean type that indicates if the SCIM service
                provider supports multiple values for the "roles"
                attribute on the User resource. OPTIONAL.

            primarySupported
                A boolean type that indicates if the SCIM service
                provider supports the "primary" sub-attribute for
                the "roles" attribute on the User resource. OPTIONAL.

            typeSupported
                A boolean type that indicates if the SCIM service
                provider supports the "type" sub-attribute for
                the "roles" attribute on the User resource. OPTIONAL.

        entitlements
            A complex type that specifies configuration options
            related to the Entitlements resource type. REQUIRED.

            supported
                A boolean type that indicates if the SCIM service
                provider supports the /Entitlements endpoint defined
                in this extension. REQUIRED.

            multipleEntitlementsSupported
                A boolean type that indicates if the SCIM service
                provider supports multiple values for the
                "entitlements" attribute on the User resource.
                OPTIONAL.

            primarySupported
                A boolean type that indicates if the SCIM service
                provider supports the "primary" sub-attribute for
                the "entitlements" attribute on the User resource.
                OPTIONAL.

            typeSupported
                A boolean type that indicates if the SCIM service
                provider supports the "type" sub-attribute for
                the "entitlements" attribute on the User resource.
                OPTIONAL.



## Roles Resource Schema

The `/Roles` resource type has a schema consisting of most of the attributes defined for the User resource's complex attribute "roles" in [[RFC7643]], as well as an additional "supported" attribute so that SCIM service providers can indicate if the role is currently enabled and intended for use in their service. The following singular attributes are defined:

    id
      A unique identifier for the role as defined by the service
      provider.  If present, each representation of the resource MUST include a
      non-empty "id" value. It MUST be a stable, non-reassignable 
      identifier that does not change when the same resource is 
      returned in subsequent requests.  The value of the "id" attribute 
      is always issued by the service provider and MUST NOT be specified 
      by the client. This attribute is OPTIONAL.

    value
        The value of a role. REQUIRED.

    display
        A human-readable name, primarily used for display purposes.
        OPTIONAL.

    type
        A label indicating the role's function.  OPTIONAL

    supported
        A boolean type that indicates if the role is supported and usable
        in the SCIM service provider's system.  REQUIRED.

    limitedAssignmentsPermitted
        A boolean type that indicates if a limited number of users may
        be assigned this role. A value of false should be interpreted
        as no numerical restriction on the number of users that may
        hold this role. Other restrictions may exist.  OPTIONAL.

    totalAssignmentsPermitted
        An integer type that indicates how many users may be
        assigned this role, either directly or inherited.
        OPTIONAL, but RECOMMENDED if assignments are restricted
        to a certain number.

    totalAssignmentsUsed
        An integer type that indicates how many users are currently
         assigned this role, either directly or inherited.
         OPTIONAL, but RECOMMENDED if assignments are restricted
         to a certain number.

Additionally, the following multi-valued attributes are defined:

    containedBy
        A list of "parent" roles that contain a superset of
        permissions including those granted by this role.
        OPTIONAL.

    contains
        A list of "child" roles that this role grants the rights of.
        OPTIONAL.

## Entitlements Resource Schema

The `/Entitlements` resource type has a schema consisting of most of the attributes defined for the User resource's complex attribute "entitlements" in [[RFC7643]], as well as an additional "supported" attribute so that SCIM service providers can indicate if the entitlement is currently enabled and intended for use in their service.

The following singular attributes are defined:

    id
        A unique identifier for the entitlement as defined by the service
        provider.  If present, each representation of the resource MUST include a
        non-empty "id" value. It MUST be a stable, non-reassignable 
        identifier that does not change when the same resource is 
        returned in subsequent requests.  The value of the "id" attribute 
        is always issued by the service provider and MUST NOT be specified 
        by the client. This attribute is OPTIONAL.

    value
        The value of an entitlement. REQUIRED.

    display
        A human-readable name, primarily used for display purposes.
        OPTIONAL.

    type
        A label indicating the entitlement's function. OPTIONAL.

    supported
        A boolean type that indicates if the entitlement is enabled
        and usable in the SCIM service provider's system. REQUIRED.

    limitedAssignmentsPermitted
        A boolean type that indicates if a limited number of users may
        be assigned this entitlement. A value of false should be
        interpreted as no numerical restriction on the number of users
        that may hold this entitlement. Other restrictions may exist.
        RECOMMENDED.

    totalAssignmentsPermitted
        An integer type that indicates how many users may be assigned
        this entitlement, either directly or inherited.  OPTIONAL, but
        RECOMMENDED if limitedAssignmentsPermitted is true.

    totalAssignmentsUsed
        An integer type that indicates how many users are currently
        assigned this entitlement, either directly or inherited.
        OPTIONAL, but RECOMMENDED if limitedAssignmentsPermitted is true.

Additionally, the following multi-valued attributes are defined:

    containedBy
        A list of "parent" entitlements that contain a superset of
        permissions including those granted by this entitlement.
        OPTIONAL.

    contains
        A list of "child" entitlements that this entitlement grants
        the rights of.  OPTIONAL.

## Resource Type Representation

### <base>/scim/v2/ResourceTypes

Sample Role and entitlement resourceTypes with permission and license as custom schema extensions

    [{
        "schemas":["urn:ietf:params:scim:schemas:core:2.0:ResourceType"],
        "id": "Role",
        "name": "Role",
        "endpoint": "/Roles",
        "description": "Role",
        "schema": "urn:ietf:params:scim:schemas:core:1.0:Role",
        "schemaExtensions": [
           {
             "schema":
               "urn:<isvname>:scim:schemas:extension:appname:1.0:RoleExample",
             "required": true
           }
         ],
         "meta": {
           "location": "https://example.com/v2/ResourceTypes/Role",
           "resourceType": "ResourceType"
         }
        },
        {
            "schemas":["urn:ietf:params:scim:schemas:core:2.0:ResourceType"],
            "id": "License",
            "name": "License",
            "endpoint": "/Licenses",
            "description": "License",
            "schema": "urn:ietf:params:scim:schemas:core:1.0:Entitlement",
            "schemaExtensions": [
               {
                 "schema":
                   "urn:<isvname>:scim:schemas:extension:appname:1.0:License",
                 "required": true
               }
             ],
             "meta": {
               "location": "https://example.com/v2/ResourceTypes/Entitlement",
               "resourceType": "ResourceType"
             }
    }]

Important:

The root schema are `urn:ietf:params:scim:schemas:core:1.0:Role` and `urn:ietf:params:scim:schemas:core:1.0:Entitlement`.

`schemaExtensions` denote the specific schema extensions for service provider with urns:

`urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample`, `urn:isvname:scim:schemas:extension:appname:1.0:License`, etc.

### Schema samples

#### <base>/scim/v2/Schemas/urn:ietf:params:scim:schemas:core:1.0:Role

Sample schema for a Role property

    {
        "id":"urn:ietf:schemas:core:1.0:Role",
        "name":"Role",
        "description":"Role schema",
        "attributes":[
            {
                "name" : "id",
                "type" : "string",
                "multiValued" : false,
                "description" : "The unique id for role.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "value",
                "type" : "string",
                "multiValued" : false,
                "description" : "Role value.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "displaye",
                "type" : "string",
                "multiValued" : false,
                "description" : "display name for role.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "type",
                "type" : "string",
                "multiValued" : false,
                "description" : "Role type.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "supported",
                "type" : "boolean",
                "multiValued" : false,
                "description" : "supported value for role",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "type",
                "type" : "string",
                "multiValued" : false,
                "description" : "The unique id for role.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            }
        ]
    }

#### <base>/scim/v2/Schemas/urn:isvname:scim:schemas:extension:appname:1.0:RoleExample

Sample schema extension for a Role property

    {
        "id":"urn:isvname:scim:schemas:extension:appname:1.0:RoleExample",
        "name":"P",
        "description":"RoleExample schema extension",
        "attributes":[
            {
                "name" : "permission",
                "type" : "string",
                "multiValued" : false,
                "description" : "custom property in role",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readwrite",
                "returned" : "default",
                "uniqueness" : "none"
            }
            ]
        },
        "meta": {
            "resourceType": "schema",
            "location":"/v2/Schemas/urn:isvname:scim:schemas:extension:appname:1.0:RoleExample"
        }
    }

#### <base>/scim/v2/Schemas/urn:ietf:scim:schemas:core:1.0:Entitlement

Sample schema for entitlement property

    {
        "id":"urn:ietf:schemas:core:1.0:Entitlement",
        "name":"Entitlement",
        "description":"Entitlement schema",
        "attributes":[
            {
                "name" : "id",
                "type" : "string",
                "multiValued" : false,
                "description" : "The unique id for Entitlement.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "value",
                "type" : "string",
                "multiValued" : false,
                "description" : "Role value.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "displaye",
                "type" : "string",
                "multiValued" : false,
                "description" : "display name for role.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "type",
                "type" : "string",
                "multiValued" : false,
                "description" : "Role type.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "supported",
                "type" : "boolean",
                "multiValued" : false,
                "description" : "supported value for role",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            },
            {
                "name" : "type",
                "type" : "string",
                "multiValued" : false,
                "description" : "The unique id for role.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "none"
            }
        ]
    }

#### <base>/scim/v2/Schemas/urn:isvname:scim:schemas:extension:appname:1.0:License

Sample schema extension for an Entitlement property

    {
        "id":"urn:isvname:scim:schemas:extension:appname:1.0:License",
        "name":"License",
        "description":"License entitlement schema extension",
        "attributes":[
            {
                "name" : "licensecount",
                "type" : "string",
                "multiValued" : false,
                "description" : "custom property in license entitlement",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readwrite",
                "returned" : "default",
                "uniqueness" : "none"
            }
            ]
        },
        "meta": {
            "resourceType": "schema",
            "location":"/v2/Schemas/urn:isvname:scim:schemas:extension:appname:1.0:License"
        }
    }



### Sample schema response

#### Retrieving all roles

`GET /Roles`

    {
        "schemas":["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
        "totalResults":"3",
        "itemsPerPage":100,
        "startIndex":1,
        "Resources":[
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Role",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample"
                ],
                "id":"rl3456",
                "value":"global_lead",
                "display":"Global Team Lead",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample":{
                    "permission":"1"
                },
                "contains":["teamlead"],
                "containedBy":[]
            },
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Role",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample"
                ],
                "id":"rl5873",
                "value":"us_team_lead",
                "display":"U.S. Team Lead",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample":{
                    "permission":"2"
                },
                "contains":["regional_lead"],
                "containedBy":["global_lead"]
            },
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Role",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample"
                ],
                "id":"rl9057",
                "value":"nw_regional_lead",
                "display":"Northwest Regional Lead",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:RoleExample":{
                    "permission":"1"
                },
                "contains":[],
                "containedBy":["us_team_lead"]
            }
        ]
    }


#### Retrieving all entitlements

`GET /Entitlements`

    {
        "schemas":["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
        "totalResults":"5",
        "itemsPerPage":100,
        "startIndex":1,
        "Resources":[
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Entitlement",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License"
                ],
                "id":"en9057",
                "value":"1",
                "display":"Printing",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License":{
                    "licensecount":"10"
                },
                "enabled":true,
                "contains":[],
                "containedBy":["5"]
            },
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Entitlement",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License"
                ],
                "id":"en9907",
                "value":"2",
                "display":"Scanning",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License":{
                    "licensecount":"100"
                },
                "contains":[],
                "containedBy":["5"]
            },
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Entitlement",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License"
                ],
                "id":"en38476",
                "value":"3",
                "display":"Copying",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License":{
                    "licensecount":"1000"
                },
                "contains":[],
                "containedBy":["5"]
            },
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Entitlement",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License"
                ],
                "id":"en2257",
                "value":"4",
                "display":"Collating",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License":{
                    "licensecount":"10"
                },
                "contains":[],
                "containedBy":["5"]
            },
            {
                "schemas": [
                    "urn:ietf:params:scim:schemas:core:1.0:Entitlement",
                    "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License"
                ],
                "id":"en33097",
                "value":"5",
                "display":"All Printer Permissions",
                "urn:<isvname>:scim:schemas:extension:<appname>:1.0:License":{
                    "licensecount":"10"
                },
                "contains":["1","2","3","4"],
                "containedBy":[]
            }
        ]
    }


#### Sample user representation with role and entitlement

    {
        "schemas":
          ["urn:ietf:params:scim:schemas:core:2.0:User",
            "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
        "id": "2819c223-7f76-453a-919d-413861904646",
        "externalId": "701984",
        "userName": "bjensen@example.com",
        "name": {
          "formatted": "Ms. Barbara J Jensen, III",
          "familyName": "Jensen",
          "givenName": "Barbara",
          "middleName": "Jane",
          "honorificPrefix": "Ms.",
          "honorificSuffix": "III"
        },
        "displayName": "Babs Jensen",
        "nickName": "Babs",
        "profileUrl": "https://login.example.com/bjensen",
        "emails": [
          {
            "value": "bjensen@example.com",
            "type": "work",
            "primary": true
          },
          {
            "value": "babs@jensen.org",
            "type": "home"
          }
        ],
        "addresses": [
          {
            "streetAddress": "100 Universal City Plaza",
            "locality": "Hollywood",
            "region": "CA",
            "postalCode": "91608",
            "country": "USA",
            "formatted": "100 Universal City Plaza\nHollywood, CA 91608 USA",
            "type": "work",
            "primary": true
          },
          {
            "streetAddress": "456 Hollywood Blvd",
            "locality": "Hollywood",
            "region": "CA",
            "postalCode": "91608",
            "country": "USA",
            "formatted": "456 Hollywood Blvd\nHollywood, CA 91608 USA",
            "type": "home"
           }
        ],
        "phoneNumbers": [
          {
            "value": "555-555-5555",
            "type": "work"
          },
          {
            "value": "555-555-4444",
            "type": "mobile"
          }
        ],
        "ims": [
          {
            "value": "someaimhandle",
            "type": "aim"
          }
        ],
        "photos": [
          {
            "value":
              "https://photos.example.com/profilephoto/72930000000Ccne/F",
            "type": "photo"
          },
          {
            "value":
              "https://photos.example.com/profilephoto/72930000000Ccne/T",
            "type": "thumbnail"
          }
        ],

        "userType": "Employee",
        "title": "Tour Guide",
        "preferredLanguage": "en-US",
        "locale": "en-US",
        "timezone": "America/Los_Angeles",
        "active":true,
        "password": "t1meMa$heen",
        "groups": [
          {
            "value": "e9e30dba-f08f-4109-8486-d5c6a331660a",
            "$ref": "../Groups/e9e30dba-f08f-4109-8486-d5c6a331660a",
            "display": "Tour Guides"
          },
          {
            "value": "fc348aa8-3835-40eb-a20b-c726e15c55b5",
            "$ref": "../Groups/fc348aa8-3835-40eb-a20b-c726e15c55b5",
            "display": "Employees"
          },
          {
            "value": "71ddacd2-a8e7-49b8-a5db-ae50d0a5bfd7",
            "$ref": "../Groups/71ddacd2-a8e7-49b8-a5db-ae50d0a5bfd7",
            "display": "US Employees"
          }
        ],
        "x509Certificates": [
          {
            "value":
             "MIIDQzCCAqygAwIBAgICEAAwDQYJKoZIhvcNAQEFBQAwTjELMAkGA1UEBhMCVVMx
              EzARBgNVBAgMCkNhbGlmb3JuaWExFDASBgNVBAoMC2V4YW1wbGUuY29tMRQwEgYD
              VQQDDAtleGFtcGxlLmNvbTAeFw0xMTEwMjIwNjI0MzFaFw0xMjEwMDQwNjI0MzFa
              MH8xCzAJBgNVBAYTAlVTMRMwEQYDVQQIDApDYWxpZm9ybmlhMRQwEgYDVQQKDAtl
              eGFtcGxlLmNvbTEhMB8GA1UEAwwYTXMuIEJhcmJhcmEgSiBKZW5zZW4gSUlJMSIw
              IAYJKoZIhvcNAQkBFhNiamVuc2VuQGV4YW1wbGUuY29tMIIBIjANBgkqhkiG9w0B
              AQEFAAOCAQ8AMIIBCgKCAQEA7Kr+Dcds/JQ5GwejJFcBIP682X3xpjis56AK02bc
              1FLgzdLI8auoR+cC9/Vrh5t66HkQIOdA4unHh0AaZ4xL5PhVbXIPMB5vAPKpzz5i
              PSi8xO8SL7I7SDhcBVJhqVqr3HgllEG6UClDdHO7nkLuwXq8HcISKkbT5WFTVfFZ
              zidPl8HZ7DhXkZIRtJwBweq4bvm3hM1Os7UQH05ZS6cVDgweKNwdLLrT51ikSQG3
              DYrl+ft781UQRIqxgwqCfXEuDiinPh0kkvIi5jivVu1Z9QiwlYEdRbLJ4zJQBmDr
              SGTMYn4lRc2HgHO4DqB/bnMVorHB0CC6AV1QoFK4GPe1LwIDAQABo3sweTAJBgNV
              HRMEAjAAMCwGCWCGSAGG+EIBDQQfFh1PcGVuU1NMIEdlbmVyYXRlZCBDZXJ0aWZp
              Y2F0ZTAdBgNVHQ4EFgQU8pD0U0vsZIsaA16lL8En8bx0F/gwHwYDVR0jBBgwFoAU
              dGeKitcaF7gnzsNwDx708kqaVt0wDQYJKoZIhvcNAQEFBQADgYEAA81SsFnOdYJt
              Ng5Tcq+/ByEDrBgnusx0jloUhByPMEVkoMZ3J7j1ZgI8rAbOkNngX8+pKfTiDz1R
              C4+dx8oU6Za+4NJXUjlL5CvV6BEYb1+QAEJwitTVvxB/A67g42/vzgAtoRUeDov1
              +GFiBZ+GNF/cAYKcMtGcrs2i97ZkJMo="
          }
        ],
        "entitlements":{
            "value":"ent1234",
            "display":"entitlement 1"
        },
        "roles":{
            "value":"rl4567",
            "display":"role 1"
        },

        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
          "employeeNumber": "701984",
          "costCenter": "4130",
          "organization": "Universal Studios",
          "division": "Theme Park",
          "department": "Tour Operations",
          "manager": {
            "value": "26118915-6090-4610-87e4-49d8ca9f808d",
            "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
            "displayName": "John Smith"
          }
        },
        "meta": {
          "resourceType": "User",
          "created": "2010-01-23T04:56:22Z",
          "lastModified": "2011-05-13T04:42:34Z",
          "version": "W\/\"3694e05e9dff591\"",
          "location": "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
        }
      }
    }

~~~
--- back

# IANA Considerations

(To-Do)


# Change Log

v00 - December 2022 - Adopted by SCIM WG.


# Acknowledgments
{:numbered="false"}

TODO acknowledge.
