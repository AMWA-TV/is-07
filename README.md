# AMWA IS-07 NMOS Event & Tally Specification

[![Lint Status](https://github.com/AMWA-TV/is-07/workflows/Lint/badge.svg)](https://github.com/AMWA-TV/is-07/actions?query=workflow%3ALint)
[![Render Status](https://github.com/AMWA-TV/is-07/workflows/Render/badge.svg)](https://github.com/AMWA-TV/is-07/actions?query=workflow%3ARender)

<!-- INTRO-START -->

### What does it do?

- Provides an IP-friendly mechanism to carry time-sensitive information
  - For example: camera tally information, audio levels, control panel button presses and status

### Why does it matter?

- ST 2110 does not provide an equivalent to GPI functionality
  - This leads to the danger of multiple proprietary approaches
- Consistency with other NMOS specifications

### How does it work?

- Media Nodes emit and consume state and state change info
- Lightweight messages sent using WebSockets or MQTT
- Message flows connected using IS-05

<!-- INTRO-END -->

## Getting started

There is more information about the NMOS Specifications and their GitHub repos at <https://specs.amwa.tv/nmos>.
