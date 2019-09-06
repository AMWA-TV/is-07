# Changelog
This document provides an overview of changes between released versions of this specification.

## Release v1.0.1

API changes:

* Fixed event.json reference in the RAML
* Changed the file naming of RAML examples to match changes in other NMOS projects
* Documented the subscription command
* Improved the descriptions and titles for some schemas
* Changed the regex for event type to allow for more characters in the type names
* Introduced parent schemas for command and message types
* Replaced the broken "connection_lost" message with "connection_status" message
* Enforced the minimum value of 1 for the optional number denominator

Documentation changes:

* New wording to support the API changes above
* Enforced uniform document formatting and references to some terms (WebSocket, Events API, REST API)
* Clarifications around the use of the retain flag and reboot, shutdown and connection status messages in MQTT
* Clarifications around case sensitivity of event types
* Addition of event type information to flows and receivers (IS-04 API change)
* New parameters added to the MQTT transport parameters (IS-05 API change)
* Clarifications of the connection/disconnection process for the IS-07 transports
* Description of the DNS-SD TXT record for MQTT brokers
* References to the authorization process
* Various clean-up and clarifications of the wording across the documentation

## Release v1.0
*   Initial release
