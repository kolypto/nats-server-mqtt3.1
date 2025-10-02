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

# QoS 2 downgrade to QoS 1

This branch contains an additional patch that downgrades QoS 2 subscriptions to QoS 1.

This simulates RabbitMQ behavior that [downgrades QoS 2 subscriptions to QoS 1](https://www.rabbitmq.com/docs/mqtt#qos-2).



## Build Me

```console
$ docker build . -t 'kolypto/nats-server-mqtt31-qos1:2.12.0-alpine' -f docker/Dockerfile
$ docker push kolypto/nats-server-mqtt31-qos1:2.12.0-alpine
```


## Test me with MQTT 3.1 Client

Prepare config:

```
listen: 127.0.0.1:4222
server_name: test
jetstream: enabled

mqtt {
    listen: 127.0.0.1:1883
}

accounts {
  MQTT: {
    jetstream {}
    users [
      { user: "user", password: "pass" }
    ]
  }
}
```

Start NATS:

```console
$ go run main.go -c mqtt.conf
```

Use Mosquitto CLI:

```console
$ mosquitto_sub -v -h localhost -p 1883 -u "user" -P "pass" -t "device/123/to" -V mqttv31 -q 2
(it won't print QoS from SUBACK)
but strace will show:
read(5, "\0\1\1", 3) = 3
$ mosquitto_pub -h localhost -p 1883 -u "user" -P "pass" -t "device/123/to" -m 'hoy' -V mqttv31 -q 2
```
