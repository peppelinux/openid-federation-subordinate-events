%%%
title = "OpenID Federation Subordinate Events Endpoint 1.0 - draft 01"
abbrev = "openid-federation-subordinate-events"
ipr = "none"
workgroup = "OpenID Connect A/B"
keyword = ["security", "openid", "federation"]

[seriesInfo]
name = "Internet-Draft"
value = "openid-federation-subordinate-events"
status = "standard"

[[author]]
initials="G."
surname="De Marco"
fullname="Giuseppe De Marco"
organization="Dipartimento per la trasformazione digitale"
    [author.address]
    email = "gi.demarco@innovazione.gov.it"

[[author]]
initials="M.B."
surname="Jones"
fullname="Michael B. Jones"
organization="Self-Issued Consulting"
    [author.address]
    email = "michael_b_jones@hotmail.com"
    uri = "https://self-issued.info/"

%%%

.# Abstract

This specification defines the `federation_subordinate_events_endpoint` for the OpenID Federation. It provides a mechanism for Trust Anchors and Intermediates to publish historical events related to their Immediate Subordinates, such as registration, revocation, and updates of their Federation Entity Keys.

{mainmatter}

# Introduction

The `federation_subordinate_events_endpoint` is introduced to allow Trust Anchors and Intermediates to maintain and publish a track of registration events about their Immediate Subordinates. This endpoint is useful for transparency and accountability within a federation, by providing a historical record of significant events.


# Terminology

This specification uses the terms
"Entity" as defined by OpenID Connect Core [@!OpenID.Core],
"Client" as defined by [@!RFC6749],
and "Trust Mark", "Federation Entity", "Federation Entity Key", "Trust Anchor",
"Intermediate", and "Subordinate Statement" defined in [@!OpenID.Federation].

## Endpoint Description

The `federation_subordinate_events_endpoint` is an optional endpoint that MAY be published by Trust Anchors and Intermediates. It MUST use the `https` scheme and MAY include port, path, and query parameter components encoded in `application/x-www-form-urlencoded` format. It MUST NOT contain a fragment component.

### Endpoint Location

The location of the `federation_subordinate_events_endpoint` is published in the `federation_entity` metadata, using the `federation_subordinate_events_endpoint` parameter.

## Subordinate Historical Events Request

### Request Format

The request to the `federation_subordinate_events_endpoint` MUST be an HTTP GET request with the following query parameters, encoded in `application/x-www-form-urlencoded` format:

- **sub**: (REQUIRED) The Entity Identifier of the subject for which the historical track is being requested.

When client authentication is used, the request must be an HTTP POST request, with the parameters passed in the POST body.

#### Example Request

```http
GET /federation_subordinate_events_endpoint?sub=https%3A%2F%2Frp%2Eexample%2Eorg HTTP/1.1
Host: immediate-superior.example.org
```

## Subordinate Historical Events Response

### Response Format

A successful response MUST use the HTTP status code 200 and the content type `application/entity-events-statement+jwt`. The response includes relevant events from the federation's perspective of the Entity. If it is a negative response, it will be a JSON object and the content type must be `application/json`.

The response is a signed JWT that is explicitly typed by setting the `typ` header parameter to `entity-events-statement+jwt` to prevent cross-JWT confusion. It is signed with a Federation Entity Key.

### JWT Claims

The claims in the Subordinate events statement response are:

- **iss**: (REQUIRED) String. Entity Identifier of the issuer of the response.
- **sub**: (REQUIRED) String. Entity Identifier of the subject of the response.
- **iat**: (REQUIRED) Number. Time when this response was issued, expressed as Seconds Since the Epoch.
- **exp**: (REQUIRED) Number. Time when this resolution is no longer valid, expressed as Seconds Since the Epoch.
- **federation_events**: (REQUIRED) Array of JSON objects, each representing an event of particular interest from the federation's perspective.

#### Event Object Parameters

- **iat**: (REQUIRED) Time when the event is related to, using the time format defined for the `iat` claim.
- **event**: (REQUIRED) String that identifies the event, such as `registration`, `jwks_update`, `metadata_policy_update`, `metadata_update`, or `revocation`.
- **event_description**: (OPTIONAL) String that may offer additional information about the event.

#### Example Response

```json
{
  "iss": "https://edugain.org",
  "sub": "https://rp.example.edu",
  "iat": 1590000000,
  "exp": 1621536000,
  "events": [
    {
      "iat": 1590000000,
      "event": "registration"
    },
    {
      "iat": 1590000000,
      "event": "jwks_updates"
    },
    {
      "iat": 1600000000,
      "event": "revocation",
      "event_description": "compromised node"
    },
    {
      "iat": 1610000000,
      "event": "registration"
    }
  ]
}
```

# Federation Entity Property

In order for Entities to advertise the `federation_subordinate_events_endpoint`, a new property has been defined adding to the existing set of Federation Entity Metadata as defined in [@!OpenID.Federation].

| **Metadata**                      | **Availability** | **Description**                                                                                                                                                                                                                                                                         |
|-----------------------------------|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| federation_subordinate_events_endpoint | OPTIONAL         | The Federation Subordinate Events Endpoint as described above. All constraints and restrictions on the listing of this endpoint are identical to that defined for the `federation_list_endpoint` as defined in OpenID Federation 1.0 

# Acknowledgements

We would like to thank the following individuals for their contributions to this specification:

- Vladimir Dzhuvinov
- Roland Hedberg

# Document History

[[ To be removed from the final specification ]]

-01

* Initial version
