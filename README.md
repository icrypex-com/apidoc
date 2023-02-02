# Icrypex API Documentation

Welcome to Icrypex developer documentation.<br />
This documentation details use of Icrypex REST API for spot exchange.

- The base endpoint is: **https://v2api.icrypex.com**
- All endpoints return either a JSON object or array if the status code is not 204 No Content.
- Some endpoints are authenticated and will require and API Key
- Once API key is created, it is recommended to set IP restrictions on the key for security reasons.
- **Never share your API key/secret key to ANYONE.**

<br />

### API Architecture

- [Authentication](architecture.md#authentication) - HTTP Authentication with HMAC
- [Status Codes](architecture.md#status-codes) - Commonly used HTTP Status Codes in Icrypex API
- [Fields](architecture.md#fields) - Field name, type and formats
- [Errors](architecture.md#errors) - Commonly used error codes and error messages
- [Rate Limits](architecture.md#rate-limits) - API Rate limiting rules

<br />

### Public Trading Endpoints

- [Exchange Info](trading-public.md#exchange-info) - Useful information about trading and funding operations
- [Tickers](trading-public.md#tickers) - Exchange pair tickers data
- [Order Book](trading-public.md#order-book) - Exchange pair order book data
- [Last Trades](trading-public.md#last-trades) - Last trades data for each pair
- [K-Line Data](trading-public.md#k-line-data) - K-Line data for tradingview and trade history

<br />

### Authenticated Trading Endpoints

- [Place Order](trading-authenticated.md#place-order) - Place (insert) a new order
- [Cancel Order](trading-authenticated.md#cancel-order) - Cancel an existing order
- [Get Open Orders](trading-authenticated.md#get-open-orders) - Get open order with some criteria
- [Get All Orders](trading-authenticated.md#get-all-orders) - Get all order history
- [Get User Trades](trading-authenticated.md#get-user-trades) - Get all trade history

<br />

### Funding Endpoints

- [Spot Balance](funding.md#place-order) - Get spot balances for each asset
- [Transfer Funds](funding.md#transfer-funds) - Transfers funds between wallets
- [Withdraw Funds](funding.md#withdraw-funds) - Withdraws funds to blockchain networks
- [Withdrawals](funding.md#withdrawals) - Withdrawal history and reports
- [Deposits](funding.md#deposits) - Deposit history and reports
- [Addresses](funding.md#addresses) - User's active cryptocurrency addresses in Icrypex

<br />

### WebSockets

- [Introduction](websockets.md#introduction) - Introduction to Icrypex websocket system
- [Authentication](websockets.md#authentication) - Authentication over websocket protocol
- [Channel Subscriptions](websockets.md#channel-subscriptions) - Subscribe to websocket channels and unsubscribe
- [Tickers Channel](websockets.md#tickers-channel) - Websocket ticker channels and receiving ticker data
- [Order Book Channel](websockets.md#order-book-channel) - Websocket orderbook channels and receiving orderbook and difference data
- [Tradingview Channel](websockets.md#tradingview-channel) - Websocket tradingview channels and receiving last tradingview candle data
- [Order Status & Trade](websockets.md#order-status--trade) - Receiving notifications about order status changes and new trade data over websockets
- [Balance Changes](websockets.md#balance-changes) - Receiving balance change notifications over websockets
