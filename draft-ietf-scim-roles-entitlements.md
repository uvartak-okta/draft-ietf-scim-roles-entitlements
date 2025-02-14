---
title: "SCIM Roles and Entitlements Extension"
abbrev: "SCIM Roles and Entitlements Extension"
docname: draft-ietf-scim-roles-entitlements-00
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


normative:

informative:


--- abstract

The System for Cross-domain Identity Management (SCIM) protocol's schema RFC [RFC7643](https://datatracker.ietf.org/doc/html/rfc7643) defines the complex core schema attributes "roles" and "entitlements". For both of these concepts, frequently only a predetermined set of values are accepted by a SCIM service provider. The values that are accepted may vary per customer or tenant based on customizable configuration in the service provider's application or based on other criteria such as what services have been purchased. This document defines an extension to the SCIM 2.0 standard to allow SCIM service providers to represent available data pertaining to roles and entitlements so that SCIM clients can consume this information and provide easier management of role and entitlement assignments.


--- middle

# Introduction

The System for Cross-domain Identity Management (SCIM) protocol's schema RFC [RFC7643](https://datatracker.ietf.org/doc/html/rfc7643) defines the complex core schema attributes "roles" and "entitlements". For both of these concepts, frequently only a predetermined set of values are accepted by a SCIM service provider. Available roles and entitlements may change based on a variety of factors, such as what features are enabled or what customizations have been made in a specific instance of a multi-tenant application. The core SCIM 2.0 RFC documents (RFC7642, RFC7643 and RFC 7644) do not provide a method for retrieving the available roles or entitlements as part of the SCIM 2.0 standard.

In order to allow for SCIM clients to avoid easily predictable errors when interacting with SCIM service providers, this document aims to provide a method for SCIM service providers to provide data on what roles and/or entitlements are available so that SCIM clients can consume this data to more efficiently manage resources between directories.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# IANA Considerations

(To-Do)

# Roles and Entitlements

The Roles and Entitlements SCIM Extension consists of two new resource types, /Roles and /Entitlements, as well as accompanying ServiceProviderConfig details to advertise support for this extension.


## ServiceProviderConfig Extension
SCIM endpoints that have implemented one or both of the endpoints from this extension MUST advertise which elements are implemented in the ServiceProviderConfig endpoint as defined:

    RolesAndEntitlements
        A complex type that specifies Roles and Entitlements extension
        configuration options. REQUIRED.

        roles
            A complex type that specifies configuration options
            related to the Roles resource type. REQUIRED.

            enabled
                A boolean type that indicates if the SCIM service
                provider supports the /Roles endpoint defined
                in this extension. REQUIRED.

            multipleRolesSupported
                A boolean type that indicates if the SCIM service
                provider supports multiple values for the "roles"
                attribute on the User resource. REQUIRED.

            primarySupported
                A boolean type that indicates if the SCIM service
                provider supports the "primary" sub-attribute for
                the "roles" attribute on the User resource. REQUIRED.

            typeSupported
                A boolean type that indicates if the SCIM service
                provider supports the "type" sub-attribute for
                the "roles" attribute on the User resource. REQUIRED.

        entitlements
            A complex type that specifies configuration options
            related to the Entitlements resource type. REQUIRED.

            enabled
                A boolean type that indicates if the SCIM service
                provider supports the /Entitlements endpoint defined
                in this extension. REQUIRED.

            multipleEntitlementsSupported
                A boolean type that indicates if the SCIM service
                provider supports multiple values for the
                "entitlements" attribute on the User resource.
                REQUIRED.

            primarySupported
                A boolean type that indicates if the SCIM service
                provider supports the "primary" sub-attribute for
                the "entitlements" attribute on the User resource.
                REQUIRED.

            typeSupported
                A boolean type that indicates if the SCIM service
                provider supports the "type" sub-attribute for
                the "entitlements" attribute on the User resource.
                REQUIRED.



## Roles Resource Schema

The /Roles resource type has a schema consisting of most of the attributes defined for the User resource's complex attribute "roles" in [RFC7643](https://datatracker.ietf.org/doc/html/rfc7643), as well as an additional "Enabled" attribute so that SCIM service providers can indicate if the role is currently enabled and intended for use in their service.

The following singular attributes are defined:

    value
        The value of a role. REQUIRED.

    display
        A human-readable name, primarily used for display purposes.
        OPTIONAL.

    type
        A label indicating the role's function.  OPTIONAL

    enabled
        A boolean type that indicates if the role is enabled and usable
        in the SCIM service provider's system.  REQUIRED.

    limitedAssignmentsPermitted
        A boolean type that indicates if a limited number of users may
        be assigned this role. A value of false should be interpreted
        as no numerical restriction on the number of users that may
        hold this role. Other restrictions may exist.  RECOMMENDED.

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

The /Entitlements resource type has a schema consisting of most of the attributes defined for the User resource's complex attribute "entitlements" in [RFC7643](https://datatracker.ietf.org/doc/html/rfc7643), as well as an additional "Enabled" attribute so that SCIM service providers can indicate if the entitlement is currently enabled and intended for use in their service.

The following singular attributes are defined:

    value
        The value of an entitlement. REQUIRED.

    display
        A human-readable name, primarily used for display purposes.
        OPTIONAL.

    type
        A label indicating the entitlement's function. OPTIONAL.

    enabled
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

Author's note: Above descriptions for contains and containedBy need work to make clearer, and probably an explanatory section as well.

## Sample Requests

### Retrieving all roles

#### Request
~~~
GET /Roles
Host: example.com
Accept: application/scim+json
Authorization: Bearer 123456abcd
~~~

#### Response

~~~
HTTP/1.1 200 OK
Content-Type: application/scim+json

{
    "schemas":["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults":3",
    "itemsPerPage":100,
    "startIndex":1,
    "Resources":[
        {
            "value":"global_lead"
            "display":"Global Team Lead"
            "enabled":true,
            "contains":["teamlead"],
            "containedBy":[],
            "limitedAssignmentsPermitted":true,
            "totalAssignmentsPermitted":5,
            "totalAssignmentsUsed":4
        },
        {
            "value":"us_team_lead"
            "display":"U.S. Team Lead"
            "enabled":true
            "contains":["regional_lead"],
            "containedBy":["global_lead],
            "limitedAssignmentsPermitted":false
        }
        {
            "value":"nw_regional_lead"
            "display":"Northwest Regional Lead"
            "enabled":true,
            "contains":[],
            "containedBy":["us_team_lead"],
            "limitedAssignmentsPermitted":false
        },
    ]
}
~~~

### Retrieving all entitlements

#### Request
~~~
GET /Entitlements
Host: example.com
Accept: application/scim+json
Authorization: Bearer 123456abcd
~~~

#### Response

~~~
HTTP/1.1 200 OK
Content-Type: application/scim+json

{
    "schemas":["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults":5",
    "itemsPerPage":100,
    "startIndex":1,
    "Resources":[
        {
            "value":"1"
            "display":"Printing"
            "enabled":true,
            "contains":[],
            "containedBy":["5"],
            "limitedAssignmentsPermitted":false
        },
        {
            "value":"2"
            "display":"Scanning"
            "enabled":True
            "contains":[],
            "containedBy":["5"],
            "limitedAssignmentsPermitted":false
        },
        {
            "value":"3"
            "display":"Copying"
            "enabled":True
            "contains":[],
            "containedBy":["5"],
            "limitedAssignmentsPermitted":false
        },
        {
            "value":"4"
            "display":"Collating"
            "contains":[],
            "containedBy":["5"],
            "limitedAssignmentsPermitted":false
        },
        {
            "value":"5",
            "display":"All Printer Permissions"
            "enabled":true,
            "contains":["1","2","3","4"],
            "containedBy":[],
            "limitedAssignmentsPermitted":false
        }
    ]
}
~~~

# Roles Schema BNF

~~~
[
    {
        "id" : "urn:ietf:params:scim:schemas:2.0:Roles",
        "name" : "Role",
        "description" : "Roles available for use with the User
        resource's 'roles' attribute",
        "attributes" : [
            {
                "name" : "value",
                "type" : "string",
                "multiValued" : false,
                "description" : "The value of a role",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "server"
            },
            {
                "name" : "display",
                "type" : "string",
                "multiValued" : false,
                "description" : "A human-readable name, primarily
                used for display purposes.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "server"
            },
            {
                "name" : "type",
                "type" : "string",
                "multiValued" : false,
                "description" : "A label indicating the role's
                function.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "server"
            },
            {
                "name" : "enabled",
                "type" : "boolean",
                "multiValued" : false,
                "description" : "A boolean type that indicates if the
                role is enabled and usable in the SCIM service
                provider's system.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "contains",
                "type" : "string",
                "multiValued" : true,
                "description" : "A complex type that shows what other
                 roles this role indirectly grants - values can be
                 considered the child role in a parent/child
                 relationship.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "containedBy",
                "type" : "string",
                "multiValued" : true,
                "description" : "A complex type that shows what other
                 roles grant this role indirectly - values can be
                  considered the parent role in a parent/child
                  relationship.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "limitedAssignmentsPermitted",
                "type" : "boolean",
                "multiValued" : false,
                "description" : "A boolean type that indicates if the
                role has a numerical limit to how many users it may
                be assigned.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
             {
                "name" : "totalAssignmentsPermitted",
                "type" : "integer",
                "multiValued" : false,
                "description" : "An integer that specifies how many
                resources in total may be granted this role.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "totalAssignmentsUsed",
                "type" : "integer",
                "multiValued" : false,
                "description" : "An integer that specifies how many
                resources in total have been granted this role.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            }
        ]
    }
]
~~~

# Entitlements Schema BNF

~~~
[
    {
        "id" : "urn:ietf:params:scim:schemas:2.0:Entitlements",
        "name" : "Entitlement",
        "description" : "Entitlements available for use with the User
        resource's 'entitlements' attribute",
        "attributes" : [
            {
                "name" : "value",
                "type" : "string",
                "multiValued" : false,
                "description" : "The value of an entitlement",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "server"
            },
            {
                "name" : "display",
                "type" : "string",
                "multiValued" : false,
                "description" : "A human-readable name, primarily
                used for display purposes.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "server"
            },
            {
                "name" : "type",
                "type" : "string",
                "multiValued" : false,
                "description" : "A label indicating the entitlement's
                function.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default",
                "uniqueness" : "server"
            },
            {
                "name" : "enabled",
                "type" : "boolean",
                "multiValued" : false,
                "description" : "A boolean type that indicates if the
                entitlement is enabled and usable in the SCIM service
                provider's system.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "contains",
                "type" : "string",
                "multiValued" : true,
                "description" : "A complex type that shows what other
                 entitlements this entitlement indirectly grants -
                 values can be considered the child entitlement in a
                 parent/child relationship.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "containedBy",
                "type" : "string",
                "multiValued" : true,
                "description" : "A complex type that shows what other
                entitlements grant this entitlement indirectly -
                values can be considered the parent entitlement in a
                parent/child relationship.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "limitedAssignmentsPermitted",
                "type" : "boolean",
                "multiValued" : false,
                "description" : "A boolean type that indicates if the
                entitlement has a numerical limit to how many users
                it may be assigned.",
                "required" : false,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "totalAssignmentsPermitted",
                "type" : "integer",
                "multiValued" : false,
                "description" : "An integer that specifies how many
                resources in total may be granted this entitlement.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            },
            {
                "name" : "totalAssignmentsUsed",
                "type" : "integer",
                "multiValued" : false,
                "description" : "An integer that specifies how many
                resources in total have been granted this
                entitlement.",
                "required" : true,
                "caseExact" : false,
                "mutability" : "readOnly",
                "returned" : "default"
            }
        ]
    }
]

~~~
--- back

# Change Log

v00 - December 2022 - Adopted by SCIM WG.


# Acknowledgments
{:numbered="false"}

TODO acknowledge.
