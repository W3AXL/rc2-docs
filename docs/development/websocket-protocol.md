# RadioConsole2 Websocket Protocol Specification

Last updated 27 June 2025

## RC2 Definitions

- **Console:** The *RC2 Console* is the GUI frontend electron app used to control any number of radio connections
- **Endpoint:** An *RC2 Endpoint* is a generic backend application which the console communicates with to transmit or receive from a radio or other related device
- **Daemon:** The *RC2 Daemon* is an endpoint application designed to control physical radio devices connected to a host computer via soundcards

## Underlying Protocols

### WebSocket Connections

Basic command & status communication between the console and an endpoint is performed over a [Websocket](https://en.wikipedia.org/wiki/WebSocket) connection. Messages are sent as human-readable JSON, and the underlying TCP socket handles all errors and dropped packets automatically.

There are two WebSocket connections for each endpoint: the root (`/`) connection which is used for control, status, and configuration messages, and the WebRTC (`/rtc`) connection, which is used to configure and initialize the WebRTC audio connection described below. The `/rtc` websocket connection is not used for any other purpose and is not described in this document.

### WebRTC Connection

Audio tranport between the console and an endpoint is performed using a [WebRTC](https://en.wikipedia.org/wiki/WebRTC) connection. Initial handshaking and instantiation of the WebRTC connection is done over the `/rtc` websocket connection, and all messaging on this socket is performed automatically by the WebRTC libraries on either end of the connection.

## WebSocket Message Format

All WebSocket messages are formatted as human-readable JSON objects. While this may not be the most efficient form, it's an easy way to encapsulate and parse data.

```json
{
    "<message type>": {
        "<key>": "<value>",
        ...
    },
    "timestamp": "<NTP timestamp>"
}
```

- `<message type>` is one of a defined set of messages detailed below
- a message can have any number of `"<key>": "<value>"` pairs specific to that message type
- `<timestamp>` is the NTP timestamp for this message. Currently, this field is unused, but is planned to allow for more precise synchronization of audio & control in the future.

## Generic Message Types

### ACK

```json
{
    "ack": {
        "type": "<message type>",
        "timestamp": "<message timestamp>"
    },
    "timestamp": "<NTP timestamp>"
}
```

The `ACK` message acknowledges a received message. Each acknowledged message is identified by its `<message type>` and the timestamp it was sent with (`<message timestamp>`).

### NACK

```json
{
    "nack": {
        "type": "<message type>",
        "timestamp": "<message timestamp>"
    },
    "timestamp": "<NTP timestamp>"
}
```

The `nack` message is identical to the `ack` message, except that it signals a message was invalid, not received correctly, or unable to be executed. If the message type was unable to be decoded, `<message type>` will be `null`.

For all message types below, it is assumed that an `ack` is sent as a response to a successfully processed message unless otherwise specified.

## Console-to-Daemon Message Types

### Query Endpoint

```json
{
    "query": {},
    "timestamp": "<NTP timestamp>"
}
```

The `query` command requests the endpoint to send back its status information in a `status` message, described below.

### Reset Endpoint

```json
{
    "reset": {},
    "timestamp": "<NTP timestamp>"
}
```

The `reset` command requests the endpoint to reset to its initial state. How this is handled depends on the specific endpoint, but for endpoints with attached radios the normal procedure is to perform a full reset of any attahed radio hardware.

### Start/Stop Transmit

```json
{
    "transmit": true/false,
    "timestamp": "<NTP timestamp>"
}
```

The `transmit` command requests a endpoint to start or stop transmitting. The endpoint will respond with a `nack` if transmit failed or the endpoint has transmit disabled in its configuration.

### Change Channel

```json
{
    "channel": "up"/"down"/<index>,
    "timestamp": "<NTP timestamp>"
}
```

The `channel` command requests a endpoint to change the currently selected channel. `"up"` or `"down"` requests the endpoint to change to the next or previous channel, while specifying an integer index >= 0 will command the endpooint to go to the specific channel index. Not all endpoints support the ability to change to specific channel indecies, and in these cases a `nack` will be sent by the endpoint.

### Softkey Press/Release/Toggle

```json
{
    "softkeyPress": "<softkey name>",
    "timestamp": "<NTP timestamp>"
}
```
```json
{
    "softkeyRelease": "<softkey name>",
    "timestamp": "<NTP timestamp>"
}
```

The `softkeyPress` command informs the endpoint that a softkey has been pressed. The endpoint will keep the softkey in the pressed state until a `softkeyRelease` message is received from the console. These two commands are useful for endpoints (such as physical radios) which handle short & long button presses as two different actions.

```json
{
    "softkeyToggle": "<softkey name>",
    "timestamp": "<NTP timestamp>"
}
```

Additionally, the `softkeyToggle` command informs the endpoint that a softkey has been pressed and released immediately.

## Daemon-to-Console Message Types