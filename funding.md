# Funding Endpoints

In order to discover other topics go back to [Table of Contents](README.md)

## Spot Balance

This endpoint requires authentication.
Returns array of spot wallet objects.
Each object represents an asset spot balance.

<pre> <b>GET</b> /sapi/v1/wallet </pre>

Spot wallet object fields and values are described below.

### Spot Wallet Model

| Field Name | Field Type  | Description |
| :--------- | :---------- | :---------- |
| asset      | string      | Asset Symbol, BTC, ETH... |
| available  | string      | Available balance. This is a computed field. available = total - others. |
| blocked    | string      | Operational blocked balance. Open order and withdrawal reqest blocks are not included that value. |
| order      | string      | Open orders total. |
| request    | string      | Processing withdrawal total. |
| total      | string      | Total balance. |

### Example Response

```json
[
  {
	"asset": "BTC",
	"order": "0.00013044",
	"request": "0.1125",
	"blocked": "0",
	"total": "17.572943277045",
	"available": "17.460312837045"
  },
  {
	"asset": "ETH",
	"order": "0",
	"request": "0",
	"blocked": "0",
	"total": "668.095214056212",
	"available": "668.095214056212"
  },
  {
	"asset": "LINK",
	...
  }
]
```

<br />

## Withdraw Funds

This feature is not enabed yet.

<br />


## Withdrawals

This feature is not enabed yet.

<br />


## Deposits

This feature is not enabed yet.

<br />


## Addresses

This feature is not enabed yet.

<br />

In order to discover other topics go back to [Table of Contents](README.md)
