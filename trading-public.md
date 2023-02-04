# Public Trading Endpoints

In order to discover other topics go back to [Table of Contents](README.md)

## Exchange Info

Exchange Info endpoint  returns all assets and pairs in Icrypex.

<pre> <b>GET</b> /v1/exchange/info </pre>

Response data is in JSON format. Asset data and pair data are described below.

```
{
    "assets": [
        { .. asset data .. },
        { .. asset data .. },
        ...
    ],
    "pairs: [
        { .. pair data .. },
        { .. pair data .. },
        ...
    ]
}
```

### Asset Data

In response model, assets field contains an asset data array. Each asset data includes these fields:

| Field Name          | Field Type  | Description |
| :------------------ | :---------- | :---------- |
| symbol              | string      | Asset symbol, BTC. |
| name                | string      | Asset name, Bitcoin. |
| description         | string      | Asset description. |
| categories          | string[]    | Asset category names as array. |
| type                | string      | Asset type, CRYPTO or FIAT. |
| isEnabled           | boolean     | Asset status. |
| isNew               | boolean     | True, if asset newly added. |
| isWithdrawalEnabled | boolean     | Asset withdrawal status. |
| isDepositEnabled    | boolean     | Asset deposit status. |
| precision           | integer     | Asset withdrawal and deposit precision. |
| displayPrecision    | integer     | Asset display precision for user balance pages etc. |
| minDeposit          | string      | Minimum deposit quantity. |
| minWithdrawal       | string      | Minimum withdrawal quantity. |
| limitWithdrawal24h  | string      | 24-hour withdrawal limit |
| limitWithdrawal30d  | string      | 30-day withdrawal limit |
| limitDeposit24h     | string      | 24-hour deposit limit |
| limitDeposit30d     | string      | 30-day deposit limit |
| updatedDate         | int64       | Asset updated date in epoch time |
| createdDate         | int64       | Asset created date in epoch time |

<br />

### Pair Data

In response model, pairs field contains an pair data array. Each pair data includes these fields:

| Field Name          | Field Type  | Description |
| :------------------ | :---------- | :---------- |
| symbol              | string      | Pair Symbol, BTCUSDT. |
| base                | string      | Base asset symbol, BTC. |
| quote               | string      | Quote asset symbol, USDT. |
| minExchangeValue    | string      | Min order total in quote, such as "5" in USDT. |
| minPrice            | string      | Minimum price in quote asset. |
| maxPrice            | string      | Maximum price in quote asset. |
| quantityPrecision   | integer     | Maximum quantity precision. For example 8 for BTCUSDT. |
| pricePrecision      | integer     | Maximum price precision. |
| totalPrecision      | integer     | Total precision. Used for market buy orders. |
| commissionPrecision | integer     | Fee precision. Used for ui and display. |
| isEnabled           | boolean     | Pair system status. |
| displayOrder        | integer     | Pair recommended display order in websites, mobile applications etc. |
| status              | string      | Pair matching engine status. |
| marketTypes         | string[]    | Supported market types such as SPOT |
| orderTypes          | string[]    | Supported order types such as MARKET, LIMIT, STOP_MARKET. |
| tickSize            | string      | Tick increment/decrement quantity in base for UI +/- buttons. |

<br />

## Tickers

Returns all ticker data.

<pre> <b>GET</b> /v1/tickers </pre>

Ticker object fields and values are described below:

### Ticker Object

| Field Name | Field Type | Description |
| :--------- | :--------- | :---------- |
| avg        | string     | Average price for last 24 hours. |
| ask        | string     | Best open ask order price. |
| bid        | string     | Best open bid order price. |
| change     | string     | Price change percentage between now and 24 hours ago. |
| qty        | string     | Price change quantity between now and 24 hours ago. |
| high       | string     | Highest price in last 24 hours. |
| last       | string     | Last trade's price value. |
| low        | string     | Lowest price in last 24 hours. |
| symbol     | string     | Pair Symbol, BTCUSDT |
| volume     | string     | 24-hour volume in base asset. |

Here is a sample ticker value:

```
[{
	"symbol": "BTCUSDT",
	"last": "48179",
	"ask": "48179",
	"bid": "48024",
	"high": "48179",
	"low": "48024",
	"avg": "48101.5",
	"change": "2.35",
	"qty": "123",
	"volume": "12345"
},
{
	"symbol": "ETHUSDT",
	...
}]
```

<br />

## Order Book

Order book endpoints returns best ask and bid orders of a pair. Returns maximum 50 rows for each side.

<pre> <b>GET</b> /v1/orderbook </pre>

### Querystrings

| Name    | Optional | Description |
| :------ | :------- | :---------- |
| symbol  | No       | Pair symbol in BTCUSDT format |

To reduce the network usage, frequently recurring field names are minified.
- Field name **p**, represents **Price** as **string**.
- Field name **q**, represents **Quantity** as **string**.

Here an example response:

```
{
	"pairSymbol": "BTCUSDT",
	"asks": [{
		"p": "48179",
		"q": "0.18574314"
	},
	{
		"p": "48180",
		"q": "0.64521519"
	}],
	"bids": [{
		"p": "48076",
		"q": "1.02465343"
	},
	{
		"p": "48010",
		"q": "0.1"
	}]
}
```

<br />

## Last Trades

Last Trades endpoint returns last trades of a pair. That endpoint returns maximum last 50 trades.

<pre> <b>GET</b> /v1/trades/last </pre>

### Querystrings

| Name    | Optional | Description |
| :------ | :------- | :---------- |
| symbol  | No       | Pair symbol in BTCUSDT format |

To reduce the network usage, frequently recurring field names are minified.
- Field name **d**, represents **Date** in unix seconds
- Field name **s**, represents **Side** BUY or SELL
- Field name **p**, represents **Price** in string
- Field name **q**, represents **Quantity** in string

Here an example response:

```
{
	"pairSymbol": "BTCUSDT",
	"trades": [
	{
		"d": 1653900040,
		"p": "48179",
		"q": "0.00269827",
		"s": "BUY"
	},
	{
		"d": 1653900034,
		"p": "48024",
		"q": "0.01",
		"s": "SELL"
	},
	...
	]
}
```

<br />


## K-Line Data

K-line data is a public endpoint. It requires some parameters to get the data.

<pre> <b>GET</b> /v1/trades/kline </pre>

### Querystrings

| Name       | Optional | Description |
| :--------- | :------- | :---------- |
| symbol     | No       | Pair symbol in BTCUSDT format |
| from       | No       | Start time in unix seconds |
| to         | No       | End time in unix seconds |
| resolution | No       | Resolution for the data. For minutes; 1,5,15,60,240. For daily 1D, for weekly 1W. |

### Example Request

```GET /sapi/v1/trades/kline?symbol=BTCUSDT&from=1645959477&to=1646200676&resolution=60```

Returns hourly kline data. Response data seems like:

```
{
	"s": "ok",
	"t": [1646526190],
	"h": [35563],
	"o": [35563],
	"l": [35467],
	"c": [35471],
	"v": [2771.6414367800016]
}
```

Each index represents a candle.
for example for index 2. close price is c[2] and open price is o[2] and the time is t[2].
Another index represents another candle.

- **s :** if request proceed succesfuly, the value is always "ok"
- **t :** time series in unix seconds
- **h :** highest price
- **l :** lowest price
- **o :** open price
- **c :** closed price
- **v :** candle volume in base asset

<br />

In order to discover other topics go back to [Table of Contents](README.md)
