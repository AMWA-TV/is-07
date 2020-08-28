# MQTT Transport

_(c) AMWA 2018, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

This document describes the use of the MQTT transport.

Other sections can be accessed from the [Overview](1.0.%20Overview.md).

## 1. Introduction

* Simple and lightweight
* Broker based
* Can be used on top of TCP or WebSockets
* Can use encryption
* Supports various QOS settings for message delivery
* Inherently solves the problem of late joiners using retained messages
* Events filtering is handled by the broker with topic filtering mechanics

## 2. NMOS Resources transport

NMOS resources using MQTT will use the `urn:x-nmos:transport:mqtt` transport.

## 3. Connection management

Only IS-05 connection management is to be used for connection management of resources defined in this specification.

### 3.1 destination_host, destination_port, source_host, source_port

These two pairs of parameters define the address of the MQTT broker for senders and receivers respectively.
As specified in IS-05, if the parameters are set to "auto" the sender or receiver should establish for itself which broker it should use, based on a discovery mechanism or its own internal configuration. 
This specification recommends setting the default based on the mechanism described in [Broker discovery](#7-broker-discovery).

For both senders and receivers, an empty constraint object may be appropriate for these parameters.

### 3.2 broker_topic

The `broker_topic` parameter holds the MQTT topic for the source associated with the sender. To facilitate filtering, the recommended format is `x-nmos/events/{version}/sources/{sourceId}`, where `{version}` is the version of this specification, e.g. `v1.0`, and `{sourceId}` is the associated source id.

For a sender, this value is intended to be static. This means the IS-05 constraints should list the static value in an 'enum' for this parameter.
For a receiver, an empty constraint object may be appropriate for this parameter.

Message types (see [Message types](2.0.%20Message%20types.md)) which use this parameter as the publishing topic:

* State
* Reboot
* Shutdown

### 3.3 connection_status_broker_topic

Support for connection status messages is recommended.

The `connection_status_broker_topic` parameter holds the sender's MQTT connection status topic, or `null` if connection status messages are not used.
The recommended string format is `x-nmos/events/{version}/connections/{connectionId}`.  
The `{version}` is the version of this specification, e.g. `v1.0`.
The `{connectionId}` can be one of the following:

* sender id (if the MQTT connection on the sending side is unique to each sender)
* device id (if the MQTT connection on the sending side is shared across a device)
* node id (if the MQTT connection on the sending side is shared across the whole node)
* any other persistent unique id as long as it creates a unique topic (for more on identifier persistence and generation, see the IS-04 specification)

For a sender, this value is intended to be static. This means the IS-05 constraints should list the static value in an 'enum' for this parameter.
For a receiver, an empty constraint object may be appropriate for this parameter.

Message types (see [Message types](2.0.%20Message%20types.md)) which use this parameter as the publishing topic:

* Connection status

### 3.4 broker_protocol

The `broker_protocol` parameter provides an indication of whether TLS is used for communication with the broker.
As specified in IS-05:
* "mqtt" indicates operation without TLS, and "secure-mqtt" indicates use of TLS.
* If the parameter is set to "auto" the sender or receiver should establish for itself which protocol it should use, based on a discovery mechanism or its own internal configuration.
This specification recommends setting the default based on the `api_proto` value described in [Broker discovery](#7-broker-discovery).

For senders and receivers that do not support both protocols, the IS-05 constraints should list the supported value in an 'enum' for this parameter.

### 3.5 broker_authorization

The `broker_authorization` parameter is a boolean value which indicates whether authorization is used for communication with the broker.
As specified in IS-05:
* If the parameter is set to "auto" the sender or receiver should establish for itself which protocol it should use, based on a discovery mechanism or its own internal configuration.
This specification recommends setting the default based on the `api_auth` value described in [Broker discovery](#7-broker-discovery).

For senders and receivers that do not support both modes, the IS-05 constraints should list the supported value in an 'enum' for this parameter.

### 3.6 ext_is_07_rest_api_url

The `ext_is_07_rest_api_url` parameter represents the URL to the Events API resource which offers the current state and type of an event emitter (source) (see [Events API](6.0.%20Event%20and%20tally%20rest%20api.md))

For a sender, this value is intended to be static. This means the IS-05 constraints should list the static value in an 'enum' for this parameter.
For a receiver, an empty constraint object may be appropriate for this parameter.

The use of "auto" is not defined for this parameter.

Example of IS-05 PATCH request on a receiver:

```json
{
    "sender_id": "9f463872-9621-4939-aa3a-dc3c82d8578b",
    "master_enable": true,
    "activation": {
        "mode": "activate_immediate",
        "requested_time": null
    },
    "transport_params": [
        {
            "broker_topic": "x-nmos/events/v1.0/sources/9f463872-9621-4939-aa3a-dc3c82d8578b",
            "connection_status_broker_topic": "x-nmos/events/v1.0/connections/a9c3cc7a-36f1-429c-b480-87b9d7e26b83",
            "ext_is_07_rest_api_url": "http://hostname/x-nmos/events/v1.0/sources/9f463872-9621-4939-aa3a-dc3c82d8578b/"
        }
    ]
}
```

### 3.7 Disconnecting/Parking

A disconnection IS-05 PATCH request should always trigger the receiver to unsubscribe from the associated `broker_topic` and `connection_status_broker_topic`.
If multiple receivers share a connection to the broker then it is up to the implementer to only unsubscribe from the `connection_status_broker_topic` when it is no longer needed by any managed receivers.

### 3.8 Connection logic flow

#### Step 1

If the client does not currently have a connection to the MQTT broker indicated by `destination_host` and `destination_port` (for senders) or `source_host` and `source_port` (for receivers), it opens a connection.
The `broker_protocol` and `broker_authorization` indicate how the connection should be negotiated.

For a sender that indicates a non-null `connection_status_broker_topic`, the sender client must publish connection status messages:
* It must specify a `connection_status` message with `"active"` set to false, as a retained *Will Message* in the *CONNECT packet*, using the `connection_status_broker_topic`.
* It must then publish a retained `connection_status` message with `"active"` set to true, using the `connection_status_broker_topic`.

If a receiver client does not already do so, it should subscribe to the `connection_status_broker_topic` if non-null.

#### Step 2

A sender should then start publishing `state` messages (and `reboot` or `shutdown` messages as required) to the `broker_topic`.

A receiver should subscribe to the `broker_topic`.

#### Step 3

The client receives the initial (retained) state for the `broker_topic` thus solving the problem of late joiners.

#### Step 4

The client continues to receive events from all the sources to which it is subscribed.

#### Step 5

If the client published a connection status message when connecting, it must publish a retained `connection_status` message with `"active"` set to false when disconnecting from the broker, using the `connection_status_broker_topic`.
(With MQTT Version 5.0, the client can just request the broker to publish the *Will Message* after closing the connection normally, by using *Disconnect wth Will Message* in the *DISCONNECT packet*.)

## 4. QoS Settings

MQTT publishers are recommended to use the QoS (Quality of Service) level `exactly once (2)`.

## 5. Late Joiners

MQTT publishers are required to set the *RETAIN flag* with every state message in order to solve the problem of late joiners.

Consumers (receivers) have a choice to either use the retained message or query the late joiners API using `ext_is_07_rest_api_url` to access the latest state of an emitter in order to get in sync.

## 6. MQTT Will Message

All event emitters (MQTT publishers) that publish `connection_status` messages must specify a retained *Will Message* using the `connection_status_broker_topic` parameter as the publishing topic, as described in [Message types - The connection status message type](2.0.%20Message%20types.md#14-the-connection-status-message-type).

## 7. Broker discovery

The default MQTT broker should be advertised via the DNS-SD service type `_nmos-mqtt._tcp`.

**api\_proto**

Each DNS-SD advertisement of this service type must be accompanied by a TXT record of name `api_proto` to indicate the transport protocol underlying MQTT.
A value of `mqtt` indicates MQTT over TCP/IP, while a value of `secure-mqtt` indicates that the broker is using TLS on the port.

**api\_auth**

Each DNS-SD advertisement of this service type must be accompanied by a TXT record of name `api_auth` to indicate whether authorization is required in order to interact with the broker.
The value must be either the string `true` or the string `false`.
