NATS with MQTT 3.1 patch
========================

The original NATS doesn't support old MQTT 3.1 protocol:
your clients just won't connect, and your NATS will say:

> Client connection created
> older protocol "MQIsdp" not supported
> Client connection closed: Protocol Violation

The old 3.1 protocol is still used by some old devices:
e.g. [Quectel BG95 modem](https://www.quectel.com/product/lpwa-bg95-cat-m1-cat-nb2-egprs-series/)
uses 3.1, unless explicitly switched to 3.1.1 with `AT+QMTCFG="version",0,4`.

Use this patch if you want to use NATS with old 3.1 devices.

## Releases

Check out:

* [Releases](https://github.com/kolypto/nats-server-mqtt3.1/releases)
* [Branches](https://github.com/kolypto/nats-server-mqtt3.1/branches)
