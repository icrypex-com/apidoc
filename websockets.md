# WebSockets

In order to discover other topics go back to [Table of Contents](README.md)

## Introduction

- Websocket endpoint is wss://v2stream.icrypex.com.
- Authentication is not mandatory. Guest connections can receive public data.
- One time authentication is enough for each connection.

### Supported Features

Icrypex websocket supports these features:

- Public last trades
- Public ticker values
- Public orderbook values
- Last tradingview candle by different resolutions
- Balance change notifications for authenticated users
- Order execution informations for authenticated users

### Message Format

All sending and receiving messages are in same format.

```
message-type | { json message body }
```

An example to subscribe tickers channel:

```
subscribe|{"c":"tickers","s":true}
```

<br />

## Authentication

Authentication is not mandatory for connecting to websocket and receiving the public trading data.
But receiving order status or balance change event data, the guest websocket connection must send successful authentication message at least one time.
For each TCP connection, one-time authentication is enough.

WebSocket authentication is similar to HTTP authentication. Same parameters are required:

- Public API Key
- Current Timestamp in Unix milliseconds
- Signature (HMAC)
- Nonce in unix milliseconds (timestamp tolerance duration)

### Authentication with API Key Model

Model type is **api-login**. JSON Model fields are:

| Field Name | Field Type | Description |
| :--------- | :--------- | :---------- |
| pk         | string     | Public Key |
| s          | string     | Signature |
| ts         | integer    | Timestamp |
| n          | integer    | Nonce |

Your full string message should seem like this:

```api-login|{"pk":"Fc3x...Geq1s","s":"jM3...s8e","ts":1234567890,"n":15000}```

### Authentication Response

After an authentication message is sent, server process the message and sends a response message to the client. If the response message tells the authentication is succesful, the TCP connection will be authenticated connection until it closed.

Response model type is api-auth. And it includes only one field with name v and boolean type. If the value is true, authentication is successful.

Here is a sample response message:

```api-auth|{"v":true}```

<br />

## Channel Subscriptions

Public trading data streams over websocket channels. In order to receive public trading data, you need to subscribe to a channel. 

- You can subscribe to a channel only one time. If you send same subscription message twice, subscription will not be doubled.
- All channel names are lowercase.
- Seperator symbol is **@** for parametered channels.
- Channel subscription message type is subscribe.
- Subscription model requires two fields:
  - **c :** channel name in string format.
  - **s :** in boolean format, true for subscribe, false for unsubscribe.

The Sample subscription message below is used for subscribing to tickers channel:

```subscribe|{"c":"tickers","s":true}```

To unsubscribe from a channel requires same message. The only thing you need to do is sending **s** value **false**.

### Verifying Channel Subscription

When you subscribe to a channel, you receive the same message from server with subscribe-response message type.

Sample message:

```subscribe-response|{"c":"tickers","s":true}```

### Parametered Channels

Some channels require parameters, usually pair symbol.
For example to subscribe order book of BTCUSDT pair, channel name should be specified with **@** seperator, like **orderbook@btcusdt**.

Sample message:

```subscribe|{"c":"orderbook@btcusdt","s":true}```

<br />

## Tickers Channel

### All Tickers

Tickers channel name is **tickers**. If you do not know how to subscribe to channel, channel subscriptions described [above](#channel-subscriptions).

After subscribed to tickers channel, server starts to send you tickers message every second. The message type is tickers. The model includes only one property with name t. It's array of ticker objects. That wrapping operation is for ensuring the message model is always single object. Each ticker object in array includes some fields described below:

| Field Name | Field Type | Description |
| :--------- | :--------- | :---------- |
| a          | string     | Best Ask Price |
| b          | string     | Best Bid Price |
| cp         | string     | 24-hour change percentage. 10 means 10%. |
| cq         | string     | 24-hour change quantity in quote asset. |
| g          | string     | 24-hour average price |
| h          | string     | 24-hour high price |
| l          | string     | 24-hour low price |
| p          | string     | Current Price |
| ps         | string     | Pair Symbol |
| v          | string     | 24-hour volume |

Sample message:

```tickers|{"t":[{"ps":"BTCUSDT","p":"40000",...}]}```

### Ticker

If you want to receive only one pair's ticker information, you can subscribe to parametered single ticker channel. Channel name is **ticker** and **parameter must be pair symbol in lower case.**

Sample message:

```subscribe|{"c":"ticker@btcusdt","s":true}```

After subscribed to ticker channel, server starts to send you ticker message each second. Message type is ticker and the JSON message is ticker object described above.

Sample message:

```ticker|{"ps":"BTCUSDT","p":"4000",...}```

<br />

## Order Book Channel

Order Book is a parametered channel. Parameter is pair symbol in lower case. Server sends two kind of orderbook message to it's subscribers. One of them is full orderbook snapshot, other message is difference data.

### Change Sets

When a client subscribe to orderbook channel, full snapshot of the current orderbook is sent to the client with a change set number. That message is sent only one time right after client subscription. Change set number is always incremental and increases one by one. That change set number should be checked by the client. If a change set number is missing, the orderbook in client might be wrong. In this situation client should resubscribe to the channel.

### Full Orderbook Message

Message type is orderbook. The JSON model is below:

| Field Name | Field Type | Description |
| :--------- | :--------- | :---------- |
| ps         | string     | Pair Symbol, BTCUSDT |
| cs         | integer    | Change set number |
| a          | object[]   | Ask orders |
| b          | object[]   | Bid Orders |

Change set number is described above. Pair symbol is the same with channel parameter, but upper case.

Ask orders and bid orders are same kind of objects. They include two string fields:

- **p :** Price
- **q :** Quantity

Each object represents a row in orderbook, not an order. A row may include multiple orders with same price. Quantity value will be sum of all orders' quantities.

Sample message:

```orderbook|{"ps":"BTCUSDT","cs":26354,"a":[{"p":"40000","q":"1.034"},...],"b":[...]}```

### Difference Message

Different message is sent to the client if there is a change in orderbook. But that operation has an interval. If there are multiple changes in 100 milliseconds, only one difference message is sent to clients.

Difference message type is **obd**. The JSON model is very similar to full orderbook model with two differences:

- Ask orders and bid orders arrays includes only changed rows. If there is no change in ask orders, ask orders array will be empty. If only two rows are changed in bid orders, bid orders array length will be 2.
- Each row object includes one more field with name t as integer.. This value represents type of change. The values and meanings are;
  - **1: The row is inserted.** There was no row with that price in previous snapshot.
  - **2: The row is updated.** There was a row with same price in previous snapshot and it's quantity value has changed now.
  - **3: The row is removed.** There was a row with same price in previous snapshot but now all orders are canceled or filled.

Sample message:

```obd|{"ps":"BTCUSDT","cs":26355,"a":[],"b":[{"p":"39650","q":"0.376","t":1}]}```

<br />

## Tradingview Channel

This feature is not enabed yet.

<br />

## Order Status & Trade

This feature is not enabed yet.

<br />

## Balance Changes

This feature is not enabed yet.

<br />

In order to discover other topics go back to [Table of Contents](README.md)
