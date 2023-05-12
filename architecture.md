# API Architecture

In order to discover other topics go back to [Table of Contents](README.md)

## Authentication

We support API Key Authentication for API endpoints. In order to use authentication, you should create a new API key in your [account page](https://www.icrypex.com/account/api-management).

Each HTTP Request must include these headers:

- **ICX-API-KEY :** Public API Key
- **ICX-SIGN :** Signature
- **ICX-TS :** Timestamp in milliseconds
- **ICX-NONCE :** Timestamp tolerance in milliseconds

Example Request:

```
curl https://api.icrypex.com/v1/sapi/order
--header "ICX-API-KEY: "
--header "ICX-SIGN: "
--header "ICX-TS: "
--header "ICX-NONCE: " \
```

### Public API Key

The public key you created in your [account page](https://www.icrypex.com/account/api-management).

### Signature

Signature is an HMAC-SHA256 encoded message. The HMAC-SHA256 code must be generated using a private key that contains a timestamp as nonce and your API key.

### Timestamp

Timestamp is an integer value. It must be current timestamp in millliseconds.

### Nonce

Nonce is a tolerance for timestamp value. If you application's time is different from the server time. Maximum allowed value is 60 seconds (60000 in milliseconds). For example, if your nonce value is 15000, your signature will be valid for 15 seconds.

### Creating Signature

Here are some examples in some programming languages about creating signature.

#### Python

```python
apiKey = YOUR_API_PUBLIC_KEY
apiSecret = YOUR_API_SECRET
apiSecret = base64.b64decode(apiSecret)
stamp = str(int(time.time())*1000)
data = "{}{}".format(apiKey, stamp).encode('utf-8')
signature = hmac.new(apiSecret, data, hashlib.sha256).digest()
signature = base64.b64encode(signature)
```

#### PHP

```php
$apiKey = YOUR_API_PUBLIC_KEY;
$apiSecret = YOUR_API_SECRET;
$message = $apiKey.(time()*1000);
$signatureBytes = hash_hmac('sha256', $message, base64_decode($apiSecret), true);
$signature = base64_encode($signatureBytes);
```

#### C#

```cs
var apiKey = YOUR_API_PUBLIC_KEY;
var apiSecret = YOUR_API_SECRET;
long stamp = Convert.ToInt64((DateTime.UtcNow - DateTime.UnixEpoch).TotalMilliseconds);
string message = apiKey + stamp;
using (HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(apiSecret)))
{
   byte[] signatureBytes = hmac.ComputeHash(Encoding.UTF8.GetBytes(message));
   string signature = Convert.ToBase64String(signatureBytes));
}
```

<br />

## Status Codes

### Successful Status Codes

- **200 OK** Request proceed successfuly
- **201 Created** New object is created successfuly
- **204 No Content** The object is deleted

### Client Side Errors

- **400 Bad Request** Request has invalid or missing parameters.
- **401 Unauthorized** Authentication is invalid.
- **403 Forbidden** You do not have permission to reach that endpoint.
- **404 Not Found** The data is not found.
- **422 Unprocessable Entity** The operation could not completed because of some reasons detailed in response body.
- **429 Too Many Requests** You requests are being rate limited

### Server Side Errors

- **500 Internal Server** Error Something went wrong in server.
- **503 Service Unavailable** Your connection is lost or the server is unavailable because of some reasons.

<br />

## Fields

All Responses are in JSON Format

### Asset

Crypto currencies are represented as asset. The fields in JSON models with name asset, assetSymbol etc, corresponds to a crypto currency such as BTC, ETH

### Quantity

The fields represent money are named as quantity. Quantity values are represented in string format. You should be careful about decimals and precisions. Quantity values never includes thousand seperators. And only dot (.) is used as seperator. You should not use comma for decimal seperators.

Here are some examples:

```json
{
    "quantity1": "0.0374024",
    "quantity2": "24.4024",
    "quantity3": "12",
    "quantity4": "72642.027836",
}
```

### Timestamp

All times in API endpoints are in unix milliseconds format. You should send time values in unix time milliseconds in your requests. And be aware, all responses includes time values in unix milliseconds formats.

### Enums

Enums are in string format and always UPPER_SNAKE_CASE. Here are some examples:
- Order Statuses; OPEN, FILL, CANCEL
- Order Types; LIMIT, MARKET, STOP_LIMIT, STOP_MARKET
- Order/Trade Sides; BUY, SELL
- Market Types; SPOT

<br />

## Errors

When something went wrong in your request, server sends 4xx HTTP Response message.

**400 Bad Request responses** are returned if your request model is invalid or missing some parameters. Details are included in body as JSON error model.

**422 Unprossable Entity** responses are returned if a condition blocks your operation. For example, when you send a valid model for placing an order to a cancel only market. Details are included in body as JSON error model.

**Other 4xx responses** does not include JSON model in their bodies.

Error Model Example:

```json
{
    "code": "market_disabled",
    "message": "BTCUSDT market is in Cancel Only mode"
}
```

### Error Message Table

| Code | Message |
| --- | ----------- |
| EXAMPLE_CODE | Example Message |

<br />

## Rate Limits

Base API URL : https://api.icrypex.com

* Action: Specifices action of the endpoint
* Method: HttpVerb (GET, POST, DELETE)
* Period: Specifies the period of requests.
* Limit: Speficies request limit of the rule.
* Rejected: Rejecting period if you are getting 429 HTTP status code.
* Policy: IP or IP and Account reject policies in the rejecting period.

| Action | URI | Method | Period (sec) | Limit (req) | Rejected (sec) | Policy |
| ------ | --- | ------ | ------------ | ----------- | -------------- | ------ |
| Order Insert | /sapi/v1/orders | POST | 60 | 300 | 60 | IP, Account |
| Order Cancel | /sapi/v1/orders/{orderId} | DELETE | 60 | 300 | 60 | IP, Account |
| Open Orders | /sapi/v1/orders/{symbol} | GET | 60 | 300 | 60 |  IP, Account |
| Order History | /sapi/v1/orders/history | GET | 60 | 300 | 60 | IP, Account |
| Trades of the Order | /sapi/v1/orders/trades/{orderId} | GET | 60 | 300 | 60 | IP, Account |
| Orderbook | /sapi/v1/orderbook | GET | 60 | 240 | 60 | IP |
| Ticker | /sapi/v1/tickers | GET | 60 | 300 | 60 | IP |
| Account Trades | /sapi/v1/trades | GET | 60 | 180 | 60 | IP, Account |
| Account Spot Wallet | /sapi/v1/wallet/spot | GET | 60 | 120 | 60 | IP, Account |
| Klines | /sapi/v1/trades/kline | GET | 60 | 120 | 60 | IP |
| Kline History | /sapi/v1/trades/kline/history | GET | 60 | 120 | 60 | IP |
| OHLC | /sapi/v1/trades/ohlc | GET | 60 | 120 | 60 | IP |
| Last Trades by Symbol | /sapi/v1/trades/last?symbol={symbol} | GET | 60 | 240 | 60 | IP |


Please [contact us](https://www.icrypex.com/corporate/contact-form) to get more information about rate limiting rules.

<br />

In order to discover other topics go back to [Table of Contents](README.md)
