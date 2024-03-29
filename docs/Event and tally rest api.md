# Events API

_(c) AMWA 2018, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

This document describes the use of the Events API.

Other sections can be accessed from the [Overview](Overview.md).

## 1. Introduction

The Events API is meant as a protocol-agnostic means by which a consumer (receiver) can get in sync with the last known state of an event emitter. The API *has to be* used only together with other event-based transports to minimize the load on the device allowing for better scalability. The main purpose of the API is resolving the problem of late joiners but it can be also used for low-frequency sanity checks of the states for signals that change states very rarely.
The API also allows for a consumer to find the type definition associated with a source event type.

## 2. NMOS Resource

The Events API should be advertised as a 'control' endpoint under an IS-04 NMOS device in the `controls` array using the `urn:x-nmos:control:events` type.
For consistency the `href` URL offered should always end with a trailing slash.

Example:

```json
{
    "senders": [
        "a65c15a4-a52e-4960-8cd2-e05c31196e5f",
        "68f519a3-5523-4b2c-b72d-ec23cc80207d"
    ],
    "receivers": [
        "8a7bb1c1-4a82-4fd9-a4fb-96f68f560831",
        "ab450c07-ce54-44da-9ea9-c3e62e7b06d0"
    ],
    "controls": [
        {
            "type": "urn:x-nmos:control:sr-ctrl/v1.0",
            "href": "http://hostname/x-nmos/connection/v1.0/"
        },
        {
            "type": "urn:x-nmos:control:events/v1.0",
            "href": "http://hostname/x-nmos/events/v1.0/"
        }
    ],
    "tags": {},
    "type": "urn:x-nmos:device:generic",
    "label": "Events Device",
    "version": "1529676926:000000000",
    "node_id": "d1713110-7343-4d9e-b3f4-456c8f6ce765",
    "id": "58f6b536-ca4c-43fd-880a-9df2501fc125",
    "description": "Events Engine Device"
}
```

## 3. Usage

The API base will return the following upon a successful GET request:

```json
[
    "sources/"
]
```

The API `sources/{sourceId}` resource will return the following upon a successful GET request

```json
[
    "state/",
    "type/"
]
```

The `state` resource allows for consumers to retrieve the latest state of the source whereas the `type` resource allows for the retrieval
of the *type definition object* associated with the event type of the source.

Example of retrieving the `state` of a source:

`GET http://hostname/x-nmos/events/v1.0/sources/a65c15a4-a52e-4960-8cd2-e05c31196e5f/state`

Example response:

```json
{
  "identity": {
    "source_id": "a65c15a4-a52e-4960-8cd2-e05c31196e5f"
  },
  "timing": {
    "creation_timestamp": "1529323551:016020406",
    "action_timestamp": "1529323551:100000000"
  },
  "event_type": "boolean",
  "payload": {
    "value": true
  }
}
```

Example of retrieving the `type` definition for a source:

`GET http://hostname/x-nmos/events/v1.0/sources/a65c15a4-a52e-4960-8cd2-e05c31196e5f/type`

Example response:

```json
{
  "type": "boolean"
}
```

## 4. Type definition changes

The `type` definitions are deemed as static in version 1.0 of this specification. This means the `type` definition must not change throughout the event life cycle.

## 5. Cases to avoid

This API is only meant for consumer late joiners to synchronise with emitters and shall not be used for regular high intensity polling of states.

## 6. Cross-Origin Resource Sharing (CORS)

In order to permit web-based control interfaces to be hosted remotely, all NMOS APIs MUST implement valid CORS HTTP headers in responses to all requests.

In addition to this, where highlighted in the API specifications servers MUST respond to HTTP pre-flight OPTIONS requests. Servers MAY additionally support HTTP OPTIONS requests made to any other API resource.

In order to simplify development, the following headers may be returned in order to remove these restrictions as far as possible. Note that these are very relaxed and may not be suitable for a production deployment.

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, PUT, POST, PATCH, HEAD, OPTIONS, DELETE
Access-Control-Allow-Headers: Content-Type, Accept
Access-Control-Max-Age: 3600
```

To ensure compatibility, the response 'Access-Control-Allow-Headers' could be set from the request's 'Access-Control-Request-Headers'.

## 7. Trailing slashes

For consistency and in order to adhere to how these APIs are specified in RAML, the 'primary' path for every resource has the trailing slash omitted.

For example, an `ext_is_07_rest_api_url` set to `http://hostname/x-nmos/events/v1.0/` will have the following resource paths which _DO NOT_ have a trailing slash:

* `http://hostname/x-nmos/events/v1.0/sources`
* `http://hostname/x-nmos/events/v1.0/sources/a65c15a4-a52e-4960-8cd2-e05c31196e5f`
* `http://hostname/x-nmos/events/v1.0/sources/a65c15a4-a52e-4960-8cd2-e05c31196e5f/type`
* `http://hostname/x-nmos/events/v1.0/sources/a65c15a4-a52e-4960-8cd2-e05c31196e5f/state`

Clients performing GET requests using these primary paths MUST use URLs with no trailing slash present.
