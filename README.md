
# BringAuto daemon

As a BringAuto we need to publish/receive context data like

- location of the platform
- platform HW status - "all sensors are ok", "" 
- commands from Industrial Portal like "go to the next stop", "park yourself", ...

Because security, redundancy and easy to integrate approach for The Autonomy we provide the BringAuto Daemon - called BAD which acts as a bridge between
The Autonomy system, and the BringAuto cloud infrastructure.

The Autonomy system communicates with BAD by stable communication protocol which does not
depend on our cloud implementation.

# Communication protocol

We use [ProtoBuf] v3 library for message format and serialization/deserialization - protocol specification
can be found at the [CarStateProtocol] and [IndustrialPortalProtocol] files.

Each message must be prefixed with four bytes long (uint32_t data type) header which holds
information about size  of the ProtoBuf message.

As a transfer layer the TCP/IP is chosen for comunication between modules and daemon and mqtt for communication between daemon and fleet management. Daemon listens on localhost network under port 1636 if port was not changed.

**In order to receive data from BAD you must send CarStatus message first. If you do not send CarStatus message
no data will be sent from BAD to Client!**

detailed description at [Car State Protocol] and [Industrial Portal Protocol]

## Protocol messages

Messages are described by ProtoBuff v3.

If the message filed is not mandatory then it's marked as OPTIONAL by "OPTIONAL"
as the last comment in documentation for the given field.
Optional fields has defaults as described in [ProtoBuf] v3 doc.

### CarStatus

Message from Client to the BringAuto Daemon

The Autonomy system should send this message in rate of one per minute at least.

Data specified in CarStatus message are

- Car state
- Telemetry (speed, position, ...)
- next stop identified by order from CarCommand::stops

For detailed message specification look at [BringAutoDaemon.proto]

### CarCommand

Message from BAD to the Client.

The Autonomy system must receive this message.

- plan route according to received commands,
- change/drive car state according to commands which it receives


[CarStateProtocol]: ./CarStateProtocol.proto
[IndustrialPortalProtocol]: ./IndustrialPortalProtocol.proto
[ProtoBuf]: https://developers.google.com/protocol-buffers
[Car State Protocol]: https://docs.google.com/document/d/1cW5t_ue0wQmp-InI-M2fug6mxXvLrYcfVjTRHR4et_c/edit?usp=sharing
[Industrial Portal Protocol]: https://docs.google.com/document/d/1sjIE4_c9NrQCpUvlgOwejVMWf6U-QSh_9qobpMqOIRU/edit?usp=sharing