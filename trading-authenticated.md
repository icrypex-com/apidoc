# Authenticated Trading Endpoints

In order to discover other topics go back to [Table of Contents](README.md)

## Place Order

That endpoint supports placing all type of orders such as limit, market, stop etc.

<pre> <b>POST</b> /sapi/v1/orders </pre>

Request body must be in JSON Format. Fields of JSON object are described below:

### Request JSON Model Fields

| Name         | Optional | Description |
| :----------- | :------- | :---------- |
| symbol       | No       | Pair Symbol, like BTCUSDT. |
| type         | No       | Takes four values: MARKET, LIMIT, STOP_MARKET, STOP_LIMIT. |
| side         | No       | Takes two values: BUY or SELL. |
| price        | Yes      | Required if order type is limit or stop limit. |
| quantity     | Yes      | Required if order side is buy and order type is market or stop market. |
| total        | Yes      | Total is used for only market or stop market buy orders. |
| triggerPrice | Yes      | For stop orders, trigger price. |
| clientId     | Yes      | You can use that value to categorize your orders. It's kept in our db and you receive that value from reporting endpoints. |


If placing order is successful returns 200 OK.
Response body is in JSON format and includes order status and trade information.

Response model fields are described below:

| Field Name   | Field Type  | Description |
| :----------- | :---------- | :---------- |
| id           | string      | Unique order id. |
| ok           | boolean     | True, if order placed successfuly. |
| pairSymbol   | string      | Pair Symbol. |
| status       | string      | Order status; Open, Filled, Canceled. |
| quantity     | string      | Order quantity. |
| leftQuantity | string      | Left order quantity. If there is no trade, equals to quantity. |
| total        | string      | Price x quantity value. For market orders, the total value you requsted. |
| matchedTotal | string      | Sum of "price x quantity" values of all trades of the order. |
| trades       | object[]    | Order's trades. For market orders, that array will always elements. |

### Example Request

```json
{
  "clientId": "web",
  "type": "market",
  "side": "buy",
  "symbol": "BTCUSDT",
  "price": "48185",
  "quantity": "0.00047732",
  "total": "23"
}
```

### Example Response

```json
{
	"id": "1551482",
	"ok": true,
	"quantity": "0.00095464",
	"leftQuantity": "0.00047732",
	"matchedTotal": "22.9996642",
	"total": "23",
	"status": "Fill",
	"pairSymbol": "BTCUSDT",
	"lastTrades": [{
		"quantity": "0.00047732",
		"price": "48185"
	}]
}
```

<br />

## Cancel Order

You can cancel your all orders from this endpoint.

<pre> <b>DELETE</b> /sapi/v1/orders </pre>

### Querystrings

| Name       | Optional | Description |
| :--------- | :------- | :---------- |
| orderId    | No       | Unique Order Id |

Order Id provided in response body on place operation. You can find your order id by using get order history and get open orders endpoints.
Successful cancel operations response 200 OK.

Extra information is included in body in JSON format.

```json
{
	"ok": true,
	"id": 1541007,
	"code": "SUCCESS"
}
```

<br />

## Get Open Orders

<pre> <b>GET</b> /sapi/v1/orders </pre>

### Querystrings

| Name    | Optional | Description |
| :------ | :------- | :---------- |
| type    | No       | LIMIT, MARKET, STOP. If you want to get all open orders, do not send that parameter. |
| page    | No       | Page number |
| limit   | No       | Page size |

Response is in JSON format.
The result is wrapped within pagination model. Both models are described below:

### Pagination Model

| Field Name      | Field Type  | Description |
| :-------------- | :---------- | :---------- |
| indexFrom       | integer     | 0 for zero based paging, 1 for regular paging |
| pageIndex       | integer     | Page number of the data |
| pageSize        | integer     | Page size |
| totalPages      | integer     | Total page count |
| totalCount      | integer     | Total item count |
| hasNextPage     | boolean     | True, if the page is not last page |
| hasPreviousPage | boolean     | True, if the page is not first page |
| items           | object[]    | The result items |

### Open Order

| Field Name   | Field Type  | Description |
| :----------- | :---------- | :---------- |
| id           | integer     | Unique order id |
| symbol       | string      | Pair Symbol |
| createdDate  | integer     | Order creation date in unix seconds |
| updatedDate  | integer     | Order last execution or cancelation date in unix seconds |
| price        | string      | Order price |
| quantity     | string      | Order quantity |
| leftQuantity | string      | Order left quantity |
| triggerPrice | string      | Trigger price if the order is stop order. Otherwise, ignore the value. |
| totalQuote   | string      | Price x Quantity value |
| side         | string      | Order side BUY or SELL |
| status       | string      | Order status OPEN, FILL or CANCEL |
| type         | string      | Order type; LIMIT, MARKET, STOP_MARKET, STOP_LIMIT |
| clientId     | string      | Client Id |

### Example Response

```json
{
	"pageIndex": 1,
	"pageSize": 25,
	"totalCount": 2,
	"totalPages": 1,
	"indexFrom": 1,
	"hasPreviousPage": false,
	"hasNextPage": false,
	"items": [{
		"id": 1124328,
		"symbol": "BTCUSDT",
		"createdDate": 1650874928318,
		"updatedDate": 1650874928318,
		"price": "47740",
		"quantity": "0.00257645",
		"leftQuantity": "0.00257645",
		"triggerPrice": "0",
		"totalQuote": "122.999723",
		"side": "BUY",
		"status": "OPEN",
		"type": "LIMIT",
		"clientId": "web"
	},
	{
		"id": 1124327,
		"symbol": "BTCUSDT",
		"createdDate": 1650874906853,
		"updatedDate": 1650874906920,
		"price": "47740",
		"quantity": "0.00092165",
		"leftQuantity": "0.00073312",
		"triggerPrice": "0",
		"totalQuote": "43.999571",
		"side": "BUY",
		"status": "OPEN",
		"type": "LIMIT",
		"clientId": "web"
	}]
}
```

<br />

## Get All Orders

<pre> <b>GET</b> /sapi/v1/orders/history </pre>

### Querystrings

| Name    | Optional | Description |
| :------ | :------- | :---------- |
| from    | No       | Minimum order creation date in unix seconds |
| to      | No       | Maximum order creation date in unix seconds |
| page    | No       | Page number |
| limit   | Yes      | Page size |
| status  | Yes      | Order Status; OPEN, FILL, CANCEL |
| symbol  | Yes      | Pair symbol, like BTCUSDT |
| type    | Yes      | Order Type; LIMIT, MARKET, STOP |
| side    | Yes      | Order Side; BUY, SELL |

Response is in JSON format.
The result is wrapped within [pagination model](#pagination-model) and order model is same with open orders endpoint described [above](#open-order).

<br />

## Get User Trades

<pre> <b>GET</b> /sapi/v1/trades </pre>

### Querystrings

| Name     | Optional | Description |
| :------- | :------- | :---------- |
| from     | No       | Minimum trade date in unix seconds |
| to       | No       | Maximum trade date in unix seconds |
| page     | Yes      | Page number |
| limit    | Yes      | Page size |
| symbol   | Yes      | Pair symbol, like BTCUSDT |
| side     | Yes      | Order Side; BUY, SELL |
| clientId | Yes      | Order client id filter |

Response is in JSON format.
The result is wrapped within pagination [pagination model](#pagination-model).
User trade model is described below:

### User Trade Model

| Name       | Optional | Description |
| :--------- | :------- | :---------- |
| date       | integer  | Trade date in unix seconds |
| fee        | string   | Fee quantity. If side is buy, fee is in base asset. If side is sell, fee is in quote asset. |
| orderId    | integer  | Order Id of the trade |
| pairSymbol | string   | Pair symbol, BTCUSDT |
| price      | string   | Trade price |
| quantity   | string   | Trade Quantity |
| side       | string   | Trade Side; BUY or SELL |
| total      | string   | Total equals "price x quantity" |

### Example Response

```json
{
	"pageIndex": 1,
	"pageSize": 25,
	"totalCount": 61,
	"totalPages": 3,
	"indexFrom": 1,
	"items": [
	{
		"date": 1653990814,
		"orderId": 1551493,
		"pairSymbol": "BTCUSDT",
		"side": "BUY",
		"quantity": "0.00012452",
		"price": "48185",
		"fee": "0.0108",
		"total": "5.999996"
	},
	{
		"date": 1653990327,
		"orderId": 1551492,
		"pairSymbol": "BTCUSDT",
		"side": "BUY",
		"quantity": "0.00024904",
		"price": "48185",
		"fee": "0.0216",
		"total": "11.999992"
	},
	{
		"date": 1653990321,
		"orderId": 1551491,
		"pairSymbol": "BTCUSDT",
		"side": "BUY",
		"quantity": "0.00016602",
		"price": "48185",
		"fee": "0.014399",
		"total": "7.999674"
	},
	...
	],
	"hasPreviousPage": false,
	"hasNextPage": true
}
```

<br />

In order to discover other topics go back to [Table of Contents](README.md)
