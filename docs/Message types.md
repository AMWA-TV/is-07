# Message types

_(c) AMWA 2018, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

This document specifies the supported message types and the strategies employed to define them.

Other sections can be accessed from the [Overview](Overview.md).

## 1. Introduction

The main concern of the Event & Tally specification is to make possible for emitters to transmit their state changes to any interested consumer.
A secondary concern is to give connected entities a mechanism by which to highlight issues in the linkage between them. In order to address this concern the `message_type` field is included in every single event message being sent.

These are the supported message types:

* state
* reboot
* shutdown
* connection status (only used by MQTT connections)
* health (only used by WebSocket connections)

For the MQTT transport, the publishing topic is specified below for each message type.

### 1.1. The state message type

The `state` message type is always sent when the emitter changes state and issues a new event, and is also used in response to a REST API query for the state via the [Events API](Event%20and%20tally%20rest%20api.md).  
These will be the predominant message types being sent in an Event & Tally system.
With the MQTT transport, this message must use the `broker_topic` parameter as the publishing topic. The *RETAIN flag* must be set.

The message structure shall have the following items:

* `identity` (mandatory)
* `event_type` (mandatory)
* `timing` (mandatory with some optional fields)
* `payload` (mandatory)
* `message_type` (mandatory)

Message structure

```json
{
  "identity": {
    "source_id": "6cbd0441-7882-44cd-9557-842243a0d618",
    "flow_id": "ff455cf3-fb17-448b-8f4c-e6c0e294f5ed"
  },
  "event_type": "boolean",
  "timing": {
    "creation_timestamp": "1531680501:280709600",
    "origin_timestamp": "1531680501:280401700",
    "action_timestamp": "1531680501:320000000"
  },
  "payload": {
    "value": true
  },
  "message_type": "state"
}
```

The `"creation_timestamp"` represents the timestamp at which an event has been created (for sensors this is the acquisition time). This field is mandatory.

The `"origin_timestamp"` represents the timestamp which led to the creation of an event due to external factors (a trigger). This field is optional.

The `"action_timestamp"` represents the timestamp at which an event should be treated or result in an action (for synchronising and dealing with delays). This field is optional.

The `"action_timestamp"` is not intended to be associated with delays of more than a few seconds, not for example to allow events scheduled in the future, it allows synchronisation of events in complex workflows.

The payload depends on the associated event type (see [Event types](Event%20types.md)).

The `flow_id` will _NOT_ be included in the response to a REST API query for the state via the Events API because the state is held by the source which has no dependency on a flow. It will, however, appear when being sent through one of the two specified transports because it will pass from the source through a flow and out on the network through the sender.

### 1.2. The reboot message type

The `reboot` message type will only be sent when the event emitter (source) is going to experience a planned restart. This message will announce any connected consumers that the emitter will momentarily be unavailable.

With the MQTT transport, this message must use the `broker_topic` parameter as the publishing topic. The *RETAIN flag* should not be set.

The message structure shall have the following items:

* `identity` (mandatory)
* `timing` (mandatory)
* `message_type` (mandatory)

Message structure

```json
{
  "identity": {
    "source_id": "6cbd0441-7882-44cd-9557-842243a0d618",
    "flow_id": "ff455cf3-fb17-448b-8f4c-e6c0e294f5ed"
  },
  "timing": {
    "creation_timestamp": "1531680501:280709600"
  },
  "message_type": "reboot"
}
```

The `"creation_timestamp"` represents the timestamp at which the emitter established that a `reboot` action will follow. This field is mandatory.

### 1.3. The shutdown message type

The `shutdown` message type will only be sent when the event emitter (sender) is going to experience a planned shutdown. This message will announce to any connected consumers that the emitter will become unavailable.

With the MQTT transport, this message must use the `broker_topic` parameter as the publishing topic. The *RETAIN flag* should not be set.

The message structure shall have the following items:

* `identity` (mandatory)
* `timing` (mandatory)
* `message_type` (mandatory)

Message structure

```json
{
  "identity": {
    "source_id": "6cbd0441-7882-44cd-9557-842243a0d618",
    "flow_id": "ff455cf3-fb17-448b-8f4c-e6c0e294f5ed"
  },
  "timing": {
    "creation_timestamp": "1531680501:280709600"
  },
  "message_type": "shutdown"
}
```

The `"creation_timestamp"` represents the timestamp at which the emitter established that a `shutdown` action will follow. This field is mandatory.

### 1.4. The connection status message type

The `connection_status` message is used to indicate the status of the connection between the MQTT client and the broker.
It is only used with the [MQTT transport](Transport%20-%20MQTT.md)).
Support for connection status messages is recommended.
The `connection_status_broker_topic` parameter is `null` if connection status messages are not used.

For a sender that indicates a non-null `connection_status_broker_topic`, the sender client must publish connection status messages.
This message must use the `connection_status_broker_topic` parameter as the publishing topic. The *RETAIN flag* must be set.

The message structure shall have the following items:

* `active` (mandatory)
* `message_type` (mandatory)

Message structure

```json
{
  "active": true,
  "message_type": "connection_status"
}
```

The `"active"` flag indicates to subscribers whether the client has a connection to the broker.

The client must publish this message with `"active"` set to true, after opening the connection to the broker.

The client must publish this message with `"active"` set to false, before closing the connection to the broker normally with a *DISCONNECT packet*.
In order that this message is published even if the client's connection is not closed normally, the client must specify a retained *Will Message* of this type, in the initial *CONNECT packet*.
(With MQTT Version 5.0, the client can just request the broker to publish the *Will Message* after closing the connection normally, by using *Disconnect wth Will Message* in the *DISCONNECT packet*.)

For more information about *Will Messages* consult the MQTT specification and other MQTT materials:

* [MQTT Version 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)
* [MQTT Version 5.0](http://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html)
* [MQTT Essentials Part 9: Last Will and Testament](https://www.hivemq.com/blog/mqtt-essentials-part-9-last-will-and-testament) (informative)

### 1.5. The health message type

The `health` message will only be sent on a WebSocket connection as a response to a `health` command see [WebSocket transport](Transport%20-%20Websocket.md).

Receivers can detect missing `health` responses (no `health` response is sent back within 5 seconds) and after a 12 seconds timeout (two consecutive missed `health` responses plus 2 seconds to allow for latencies) may attempt to reconnect to the WebSocket and re-initialise the subscriptions list.

The message structure shall have the following items:

* `timing` (mandatory with some optional fields)
* `message_type` (mandatory)

Message structure

```json
{
  "timing": {
    "origin_timestamp": "1441974485:12300000",
    "creation_timestamp": "1441974485:23400000"
  },
  "message_type": "health"
}
```

The `"creation_timestamp"` represents the health timestamp introduced by the sender. This field is mandatory.

The `"origin_timestamp"` represents the original health timestamp sent by the receiver. This field is optional.
