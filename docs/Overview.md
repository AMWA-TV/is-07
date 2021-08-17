# AMWA NMOS Event & Tally Specification: Overview

_(c) AMWA 2018, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## 1. Documentation

The documents included in this directory provide additional details and recommendations for implementations of the defined API, and its consumers.

## 2. Introduction

The purpose of the Event & Tally specification is to provide a mechanism by which to emit and consume states and state changes issued by sources (sensors, actuators etc). This is by no means a control API and controlling or sending commands to a receiver is out of scope. States sent need to be for general consumption which is to say they should not be sent with a specific receiver in mind (all states need to be consumable by any compatible receiver).

The specification is split into the following sections:

* [Message types](2.0.%20Message%20types.md)
* [Event types](3.0.%20Event%20types.md)
* [Core models](4.0.%20Core%20models.md)
* [Transports](5.0.%20Transports.md)
  * [MQTT](5.1.%20Transport%20-%20MQTT.md)
  * [WebSocket](5.2.%20Transport%20-%20Websocket.md)
* [Events API](6.0.%20Event%20and%20tally%20rest%20api.md)
* [Measurement units guidelines](7.0.%20Measurement%20units%20guidelines.md)
