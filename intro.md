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

