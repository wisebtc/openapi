# Spot

### Terminology

* `base asset` refers to the asset that is the `quantity` of a symbol.
* `quote asset` refers to the asset that is the `price` of a symbol.

**Symbol status:**

* TRADING
* HALT
* BREAK

**Order status:**

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* PENDING_CANCEL
* REJECTED

**Order types:**

* LIMIT
* MARKET

**Order side:**

* BUY
* SELL

**Time in force:**

* GTC
* IOC
* FOK

**Kline/Candlestick chart intervals:**

  m -> minutes; h -> hours; d -> days; w -> weeks; M -> months

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**Rate limiters (rateLimitType)**

* REQUESTS_WEIGHT
* ORDERS

**Rate limit intervals**

* SECOND
* MINUTE
* DAY

For example:
```javascript
{
  "rateLimitType": "ORDERS",
  "interval": "SECOND",
  "limit": 20
}
```

---

### Public

Security Type: **None**

Endpoints under **Public** section can be accessed freely without requiring any API-key or signatures.

#### Test connectivity

```shell
GET /openapi/v1/ping
```

Test connectivity to the Rest API.

**Weight:**
0

**Parameters:**
NONE

**Response:**

```javascript
{}
```

#### Check server time

```shell
GET /openapi/v1/time
```

Test connectivity to the Rest API and get the current server time.

**Weight:**
0

**Parameters:**
NONE

**Response:**

```javascript
{
  "serverTime": 1538323200000
}
```

#### Cryptoasset trading pairs

```shell
GET /openapi/v1/pairs
```
a summary on cryptoasset trading pairs available on the exchange

**Weight:**
1

**Parameters:**
None

**Response:**

```javascript
[
  {
    "symbol": "LTCBTC",
    "quoteToken": "LTC",
    "baseToken": "BTC"
  },
  {
    "symbol": "BTCUSDT",
    "quoteToken": "BTC",
    "baseToken": "USDT"
  }
]
```

---

### Market

Security Type: **None**

Endpoints under **Market** section can be accessed freely without requiring any API-key or signatures.

#### Broker information

```shell
GET /openapi/v1/brokerInfo
```

Current broker trading rules and symbol information

**Weight:**
0

**Parameters:**
Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
type | STRING | NO | Trade section information. Possible values include `token`, `options`, and `contracts`. If the parameter is not sent, all trading information will be returned.

**Response:**

```javascript
{
  "timezone": "UTC",
  "serverTime": 1538323200000,
  "rateLimits": [{
      "rateLimitType": "REQUESTS_WEIGHT",
      "interval": "MINUTE",
      "limit": 1500
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "SECOND",
      "limit": 20
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "DAY",
      "limit": 350000
    }
  ],
  "brokerFilters":[],
  "symbols": [{
    "symbol": "ETHBTC",
    "status": "TRADING",
    "baseAsset": "ETH",
    "baseAssetPrecision": "0.001",
    "quoteAsset": "BTC",
    "quotePrecision": "0.01",
    "icebergAllowed": false,
    "filters": [{
      "filterType": "PRICE_FILTER",
      "minPrice": "0.00000100",
      "maxPrice": "100000.00000000",
      "tickSize": "0.00000100"
    }, {
      "filterType": "LOT_SIZE",
      "minQty": "0.00100000",
      "maxQty": "100000.00000000",
      "stepSize": "0.00100000"
    }, {
      "filterType": "MIN_NOTIONAL",
      "minNotional": "0.00100000"
    }]
  }]
}
```

#### Depth

```shell
GET /openapi/quote/v1/depth
```

**Weight:**
Adjusted based on the limit:

Limit | Weight
------------ | ------------
5, 10, 20, 50, 100 | 1
500 | 5
1000 | 10

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 100; max 100.

**Caution:** setting limit=0 can return a lot of data.

**Response:**

```javascript
{
  "bids": [
    [
      "3.90000000",   // PRICE
      "431.00000000"  // QTY
    ],
    [
      "4.00000000",
      "431.00000000"
    ]
  ],
  "asks": [
    [
      "4.00000200",  // PRICE
      "12.00000000"  // QTY
    ],
    [
      "5.10000000",
      "28.00000000"
    ]
  ]
}
```

#### Merged depth (recommended)

```shell
/openapi/quote/v1/depth/merged
```

This endpoint retrieve market depth data (not full depth). This endpoint updates every 300ms.

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 40; max 40.

**Response:**

```javascript
{
  "bids": [
    [
      "3.90000000",   // PRICE
      "431.00000000"  // QTY
    ],
    [
      "4.00000000",
      "431.00000000"
    ]
  ],
  "asks": [
    [
      "4.00000200",  // PRICE
      "12.00000000"  // QTY
    ],
    [
      "5.10000000",
      "28.00000000"
    ]
  ]
}
```

#### Recent trades list

```shell
GET /openapi/quote/v1/trades
```

Get recent trades (up to last 60).

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES |
limit | INT | NO | Default 500; max 1000.

**Response:**

```javascript
[
  {
    "price": "4.00000100",
    "qty": "12.00000000",
    "time": 1499865549590,
    "isBuyerMaker": true
  }
]
```

#### Kline/candlestick data

```shell
GET /openapi/quote/v1/klines
```

Kline/candlestick bars for a symbol.
Klines are uniquely identified by their open time.
If startTime and endTime are not sent, the most recent klines are returned.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES | Symbol Name
interval | ENUM | YES | Interval of the Kline. Possible values include: `1m`, `5m`, `15m`, `30m`, `1h`, `1d`, `1w`, `1M`
startTime | LONG | NO | Starting timestamp (ms)
endTime | LONG | NO | Ending timestamp (ms)
limit | INT | NO | Default 500; max 1000.

* If startTime and endTime are not sent, the most recent klines are returned.

**Response:**

```javascript
[
  [
    1499040000000,      // Open time
    "0.01634790",       // Open
    "0.80000000",       // High
    "0.01575800",       // Low
    "0.01577100",       // Close
    "148976.11427815",  // Volume
    1499644799999,      // Close time
    "2434.19055334",    // Quote asset volume
    308,                // Number of trades
    "1756.87402397",    // Taker buy base asset volume
    "28.46694368"       // Taker buy quote asset volume
  ]
]
```

#### 24hr ticker price change statistics

```shell
GET /openapi/quote/v1/ticker/24hr
```

24 hour price change statistics. **Careful** when accessing this with no symbol. 

**Weight:**
1 for a single symbol; **40** when the symbol parameter is omitted

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO | Symbol Name.  E.g. `BTCUSDT`

* If the symbol is not sent, tickers for all symbols will be returned in an array.

**Response:**

```javascript
{
  "time": 1538725500422,
  "symbol": "ETHBTC",
  "bestBidPrice": "4.00000200",
  "bestAskPrice": "4.00000200",
  "lastPrice": "4.00000200",
  "openPrice": "99.00000000",
  "highPrice": "100.00000000",
  "lowPrice": "0.10000000",
  "volume": "8913.30000000"
}
```

OR

```javascript
[
  {
    "time": 1538725500422,
    "symbol": "ETHBTC",
    "lastPrice": "4.00000200",
    "openPrice": "99.00000000",
    "highPrice": "100.00000000",
    "lowPrice": "0.10000000",
    "volume": "8913.30000000"
 }
]
```

#### Symbol price ticker

```shell
GET /openapi/quote/v1/ticker/price
```

Latest price for a symbol or symbols.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO |

* If the symbol is not sent, prices for all symbols will be returned in an array.

**Response:**

```javascript
## Single ticker
{
  "price": "4.00000200"
}
```

OR

```javascript
## Multiple ticker info when symbol is omiited
[
  {
    "symbol": "LTCBTC",
    "price": "4.00000200"
  },
  {
    "symbol": "ETHBTC",
    "price": "0.07946600"
  }
]
```

#### Symbol orderbook ticker

```shell
GET /openapi/quote/v1/ticker/bookTicker
```

Best price/quantity on the order book for a symbol or symbols.

**Weight:**
1

**Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO | Symbol Name. E.g. `BTCUSDT`

* If the symbol is not sent, bookTickers for all symbols will be returned in an array.

**Response:**

```javascript
## Single ticker
{
  "symbol": "LTCBTC",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
}
```

OR

```javascript
## Multiple ticker info when symbol is omiited
[
  {
    "symbol": "LTCBTC",
    "bidPrice": "4.00000000",
    "bidQty": "431.00000000",
    "askPrice": "4.00000200",
    "askQty": "9.00000000"
  },
  {
    "symbol": "ETHBTC",
    "bidPrice": "0.07946700",
    "bidQty": "9.00000000",
    "askPrice": "100000.00000000",
    "askQty": "1000.00000000"
  }
]
```

---

### Trade

Security Type: **[USER_DATA/TRADE](02-general-information.md#endpoint-security-type)**

Endpoints under **Trade** require an **[API-key and a signature](02-general-information.md#signed-trade-and-user_data-endpoint-security)**.

#### New order

```shell
POST /openapi/v1/order
```

Send in a new order.

**Weight:**
1

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES | Symbol Name. E.g. `BTCUSDT`
quantity | DECIMAL | YES | Order quantity. For **MARKET BUY** orders, `quantity=amount`
side | ENUM | YES | Side of the order, `BUY/SELL`
type | ENUM | YES | Type of the order, `LIMIT/MARKET/LIMIT_MAKER`
timeInForce | STRING | NO | Time in force. Possible values include `GTC`(Default), `FOK`, `IOC`
price | DECIMAL | NO | Order price, **REQUIRED** for `LIMIT` orders
newClientOrderId | STRING | NO | A unique id for the order. Automatically generated if not sent.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
{
  'symbol': 'LXTUSDT', 
  'orderId': '494736827050147840', 
  'clientOrderId': '157371322565051',
  'transactTime': '1573713225668', 
  'price': '0.005452', 
  'origQty': '110', 
  'executedQty': '0', 
  'status': 'NEW',
  'timeInForce': 'GTC', 
  'type': 'LIMIT', 
  'side': 'SELL'
}
```

#### Test new order

```shell
POST /openapi/v1/order/test
```

Test new order creation and signature/recvWindow length. Creates and validates a new order but does not send it into the matching engine.

**Weight:**
1

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | YES | Symbol Name. E.g. `BTCUSDT`
quantity | DECIMAL | YES | Order quantity. For **MARKET BUY** orders, `quantity=amount`
side | ENUM | YES | Side of the order, `BUY/SELL`
type | ENUM | YES | Type of the order, `LIMIT/MARKET/LIMIT_MAKER`
timeInForce | STRING | NO | Time in force. Possible values include `GTC`(Default), `FOK`, `IOC`
price | DECIMAL | NO | Order price, **REQUIRED** for `LIMIT` orders
newClientOrderId | STRING | NO | A unique id for the order. Automatically generated if not sent.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
{}
```

#### Query order

```shell
GET /openapi/v1/order
```

Check an order's status.

**Weight:**
1

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
orderId | LONG | NO | Order Id. E.g. `507904211109878016`
clientOrderId | STRING | NO | Client Order Id, Unique order ID generated by users to mark their orders. E.g. 12094ahsihsiad

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

Notes:

* Either `orderId` or `clientOrderId` must be sent.
* For some historical orders `cummulativeQuoteQty` will be < 0, meaning the data is not available at this time.

**Response:**

```javascript
{
  "symbol": "LTCBTC",
  "orderId": 1,
  "clientOrderId": "9t1M2K0Ya092",
  "price": "0.1",
  "origQty": "1.0",
  "executedQty": "0.0",
  "cummulativeQuoteQty": "0.0",
  "avgPrice": "0.0",
  "status": "NEW",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "BUY",
  "stopPrice": "0.0",
  "icebergQty": "0.0",
  "time": 1499827319559,
  "updateTime": 1499827319559,
  "isWorking": true
}
```

#### Cancel order

```shell
DELETE /openapi/v1/order
```

Cancel an active order.

**Weight:**
1

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
orderId | LONG | NO | Order Id. E.g. `507904211109878016`
clientOrderId | STRING | NO | Client Order Id, Unique order ID generated by users to mark their orders. E.g. 12094ahsihsiad

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

Notes:

* Either `orderId` or `clientOrderId` must be sent.

**Response:**

```javascript
{
  'exchangeId': '301', 
  'symbol': 'BHTUSDT', 
  'clientOrderId': '0', 
  'orderId': '499890200602846976', 
  'status': 'CANCELED'
}
```

#### Current open orders

```shell
GET /openapi/v1/openOrders
```

GET all open orders on a symbol. **Careful** when accessing this with no symbol.

**Weight:**
1

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO | Symbol Name. E.g. `BTCUSDT`
orderId | LONG | NO | Order Id. E.g. `507904211109878016`
limit | INT | NO | Default 500; max 1000.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Notes:**

* If `orderId` is set, it will get orders < that `orderId`. Otherwise most recent orders are returned.

**Response:**

```javascript
[
  {
    'orderId': '499902955766523648', 
    'clientOrderId': '157432907618453', 
    'exchangeId': '301', 
    'symbol': 'BHTUSDT', 
    'price': '0.01', 
    'origQty': '50', 
    'executedQty': '0', 
    'cummulativeQuoteQty': '0', 
    'avgPrice': '0', 
    'status': 'NEW', 
    'timeInForce': 'GTC', 
    'type': 'LIMIT', 
    'side': 'BUY', 
    'stopPrice': '0.0', 
    'icebergQty': '0.0', 
    'time': '1574329076202', 
    'updateTime': '0', 
    'isWorking': true
  },...
]
```

#### History orders

```shell
GET /openapi/v1/historyOrders
```

Get all history orders. Careful when accessing this with no symbol.

**Weight:**
5

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO | Symbol Name. E.g. `BTCUSDT`
orderId | LONG | NO | Order Id. E.g. `507904211109878016`
startTime | LONG | NO | Start time (ms)
endTime | LONG | NO | End time (ms)
limit | INT | NO | Default 500; max 1000.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Notes:**

* If `orderId` is set, it will get orders < that `orderId`. Otherwise most recent orders are returned.

**Response:**

```javascript
[
  {
    'orderId': '499890200602846976', 
    'clientOrderId': '157432755564968', 
    'exchangeId': '301', 
    'symbol': 'BHTUSDT', 
    'price': '0.01', 
    'origQty': '50', 
    'executedQty': '0', 
    'cummulativeQuoteQty': '0', 
    'avgPrice': '0', 
    'status': 'CANCELED', 
    'timeInForce': 'GTC', 
    'type': 'LIMIT', 
    'side': 'BUY', 
    'stopPrice': '0.0', 
    'icebergQty': '0.0', 
    'time': '1574327555669', 
    'updateTime': '0', 
    'isWorking': true
  },...
]
```

#### Trades

```shell
GET /openapi/v1/myTrades
```

Get historyical trades

**Weight:**
5

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
symbol | STRING | NO | Symbol Name. E.g. `BTCUSDT`
startTime | LONG | NO | Start time (ms)
endTime | LONG | NO | End time (ms)
fromId | LONG | NO | TradeId to fetch from.
toId | LONG | NO | TradeId to fetch to.
limit | INT | NO | Default 500; max 1000.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Notes:**

* If only `fromId` is set，it will get orders < that `fromId` in descending order.
* If only `toId` is set, it will get orders > that `toId` in ascending order.
* If `fromId` is set and `toId` is set, it will get orders < that `fromId` and > that `toId` in descending order.
* If `fromId` is not set and `toId` it not set, most recent order are returned in descending order.

**Response:**

```javascript
[
  {
    "symbol": "ETHBTC",
    "id": 28457,
    "orderId": 100234,
    "price": "4.00000100",
    "qty": "12.00000000",
    "commission": "10.10000000",
    "commissionAsset": "ETH",
    "time": 1499865549590,
    "isBuyer": true,
    "isMaker": false
  }
]
```

--- 

### Account

Security Type: **[USER_DATA/TRADE](02-general-information.md#endpoint-security-type)**

Endpoints under **Account** require an **[API-key and a signature](02-general-information.md#signed-trade-and-user_data-endpoint-security)**.

#### Account information

```shell
GET /openapi/v1/account
```

GET current account information (balances)

**Weight:**
5

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
{
  'balances': 
    [
      {
        'asset': 'ALGO', 
        'free': '0', 
        'locked': '0'
      }, 
    {
        'asset': 'BHT', 
        'free': '0', 
        'locked': '0'
      },...
    ]
}

```

#### Account deposit information

```shell
GET /openapi/v1/depositOrders
```

GET deposit orders for a specific account.

**Weight:**
5

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
startTime | LONG | NO | Start time (ms)
endTime | LONG | NO | End time (ms)
fromId | LONG | NO | Deposit orderId to fetch from. Default gets the most recent deposit orders.
limit | INT | NO | Default 500; max 1000.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Notes:**

* If `fromId` is set, it will get orders > that `fromId`. Otherwise most recent orders are returned.

**Response:**

```javascript
[
  {
    'time': '1565769575929', 
    'orderId': '428100569859739648', 
    'token': 'USDT', 
    'address': '', 
    'addressTag': '', 
    'fromAddress': '', 
    'fromAddressTag': '', 
    'quantity': '1100'
  },...
]
```

#### Account withdrawal information

```shell
GET /openapi/v1/withdrawalOrders
```

GET withdrawal orders for a specific account.

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
token | STRING | NO | Token name. Default: All tokens.
startTime | LONG | NO | Start time (ms)
endTime | LONG | NO | End time (ms)
fromId | LONG | NO | Query from this OrderId. Defaults to latest records.
limit | INT | NO | Default 500; max 1000.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
[
  {
    "time":"1536232111669",
    "orderId":"90161227158286336",
    "accountId":"517256161325920",
    "tokenId":"BHC",
    "tokenName":"BHC",
    "address":"0x815bF1c3cc0f49b8FC66B21A7e48fCb476051209",
    "addressExt":"address tag",
    "quantity":"14", // Withdrawal qty
    "arriveQuantity":"14", // Arrived qty
    "statusCode":"PROCESSING_STATUS",
    "status":3,
    "txid":"",
    "txidUrl":"",
    "walletHandleTime":"1536232111669",
    "feeTokenId":"BHC",
    "feeTokenName":"BHC",
    "fee":"0.1",
    "requiredConfirmNum":0, // Required confirmations
    "confirmNum":0, // Confirmations
    "kernelId":"", // BEAM and GRIN only
    "isInternalTransfer": false // True if this transfer is internal
  },
  {
    "time":"1536053746220",
    "orderId":"762522731831527",
    "accountId":"517256161325920",
    "tokenId":"BHC",
    "tokenName":"BHC",
    "address":"fdfasdfeqfas12323542rgfer54135123",
    "addressExt":"EOS tag",
    "quantity":"", //Withdrawal qty
    "arriveQuantity":"10", // Arrived qty
    "statusCode":"BROKER_AUDITING_STATUS",
    "status":"2",
    "txid":"",
    "txidUrl":"",
    "walletHandleTime":"1536232111669",
    "feeTokenId":"BHC",
    "feeTokenName":"BHC",
    "fee":"0.1",
    "requiredConfirmNum":0, // Required confirmations
    "confirmNum":0, // Confirmations
    "kernelId":"", // BEAM and GRIN only
    "isInternalTransfer": false // True if this transfer is internal
  }
]
```

Status Code | Status | Description
------------ | ------------ | ------------
1 | BROKER_AUDITING_STATUS | Processing by broker
2 | BROKER_REJECT_STATUS | Rejected by broker
3 | AUDITING_STATUS | Processing by platform
4 | AUDIT_REJECT_STATUS | Reject by platform
5 | PROCESSING_STATUS | Processing by wallet
6 | WITHDRAWAL_SUCCESS_STATUS | Withdrawal success
7 | WITHDRAWAL_FAILURE_STATUS | Withdrawal failed
8 | BLOCK_MINING_STATUS | Blockchain mining

#### Withdrawal detail

```shell
GET /openapi/v1/withdraw/detail
```

GET withdrawal info

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
orderId | LONG | NO | Either orderId or clientOrderId must be sent
clientOrderId | STRING | NO | Either orderId or clientOrderId must be sent

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
{
  "time":"1536232111669",
  "orderId":"90161227158286336",
  "accountId":"517256161325920",
  "tokenId":"BHC",
  "tokenName":"BHC",
  "address":"0x815bF1c3cc0f49b8FC66B21A7e48fCb476051209",
  "addressExt":"address tag",
  "quantity":"14", // Withdrawal qty
  "arriveQuantity":"14", // Arrived qty
  "statusCode":"PROCESSING_STATUS",
  "status":3,
  "txid":"",
  "txidUrl":"",
  "walletHandleTime":"1536232111669",
  "feeTokenId":"BHC",
  "feeTokenName":"BHC",
  "fee":"0.1",
  "requiredConfirmNum":0, // Required confirmations
  "confirmNum":0, // Confirmations
  "kernelId":"", // BEAM and GRIN only
  "isInternalTransfer": false // True if this transfer is internal
}
```

#### Get Sub-account list

```shell
POST /openapi/v1/subAccount/query
```

Get your main-account and sub-accounts

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Weight:**
5

**Response:**

```javascript
[
  {
      "accountId": "122216245228131",
      "accountName": "",
      "accountType": 1,
      "accountIndex": 0 // main-account: 0, sub-account: 1
  },
  {
      "accountId": "482694560475091200",
      "accountName": "createSubAccountByCurl", // sub-account name
      "accountType": 1, // sub-account type 1. token trading 3. contract trading
      "accountIndex": 1
  },
  {
      "accountId": "422446415267060992",
      "accountName": "",
      "accountType": 3,
      "accountIndex": 0
  },
  {
      "accountId": "482711469199298816",
      "accountName": "createSubAccountByCurl",
      "accountType": 3,
      "accountIndex": 1
  },
]
```

#### Internal account transfer

```shell
POST /openapi/v1/transfer
```

Internal transfer are permitted using this endpoint

**Weight:**
1

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
fromAccountType | INT | NO | Source account type: 1. token trading account 2. Options account 3. Contracts account
fromAccountIndex | INT | NO | Sub-account index (valid when using main-account api, get sub-account indices from `/subAccount/query` endpoint)
toAccountType | INT | NO | Target account type: 1. token trading account 2. Options account 3. Contracts account
toAccountIndex | INT | NO | Sub-account index(valid when using main-account api, get sub-account indices from `/subAccount/query` endpoint)
tokenId | STRING | YES |  Token Id. E.g. `BTC`, `ETH`, `XRP`
amount | INT | YES | Amount of token(s) to be transferred

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
{
    "success":"true" // success
}
```

**Explanation**

1. Either transferring or receiving account must be the main account (Token trading account)
2. Main-account API can support transferring to other account(including sub-accounts) and receiving from other accounts
3. **Sub-account API only supports transferring from current account to the main-account. Therefore `fromAccountType\fromAccountIndex\toAccountType\toAccountIndex` should be left empty.**

#### Withdraw

```shell
POST /openapi/v1/withdraw
```

Withdraw to external address

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
tokenId | STRING | YES | TokenId. E.g. `BTC`、 `ETH`...
clientOrderId | STRING | YES | Id generated from broker side, to prevent double withdrawal
address | STRING | YES | Withdrawal address (**Note: the withdrawal address must be in current tag list in your PC/APP client**)
addressExt | STRING | NO | EOS tag
withdrawalQuantity | STRING | Withdrawal amount
chainType | STRING | NO | chain type, USDT chain types are `OMNI` `ERC20` `TRC20`default is `OMNI`

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
{
  "time":"1536232111669",
  "orderId":"90161227158286336",
  "accountId":"517256161325920",
  "tokenId":"BHC",
  "tokenName":"BHC",
  "address":"0x815bF1c3cc0f49b8FC66B21A7e48fCb476051209",
  "addressExt":"address tag",
  "quantity":"14", // Withdrawal qty
  "arriveQuantity":"14", // Arrived qty
  "statusCode":"PROCESSING_STATUS",
  "status":3,
  "txid":"",
  "txidUrl":"",
  "walletHandleTime":"1536232111669",
  "feeTokenId":"BHC",
  "feeTokenName":"BHC",
  "fee":"0.1",
  "requiredConfirmNum":0, // Required confirmations
  "confirmNum":0, // Confirmations
  "kernelId":"", // BEAM and GRIN only
  "isInternalTransfer": false // True if this transfer is internal
}
```

#### Check balance flow

```shell
POST /openapi/v1/balance_flow
```

Check balance flow for a specified account

**Weight:**
5

**Headers:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-BH-APIKEY | STRING | YES | Your API key

**Query Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
accountType | INT | NO | Account type: 1. token trading account 2.Options account 3. Contracts account
accountIndex | INT | NO | Sub-account index(valid when using main-account api, get sub-account indices from `/subAccount/query` endpoint)
tokenId | STRING | NO | Token Id. E.g. `BTC`, `ETH`, `XRP`
fromFlowId | LONG | NO | FlowId to start from
endFlowId | LONG | NO | FlowId to end with
startTime | LONG | NO | Time to start from
endTime | LONG | NO | Time to end with
limit | INT | NO | Number of entries returned. Default 500, max 500.

**Body Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
signature | STRING | YES | Authentication is needed for this endpoint
timestamp | LONG | YES | Current unix timestamp(ms)
recvWindow | LONG | NO | RecvWindow for this request.

**Response:**

```javascript
[
  {
    "id": "539870570957903104",
    "accountId": "122216245228131",
    "tokenId": "BTC",
    "tokenName": "BTC",
    "flowTypeValue": 51, // balance flow type
    "flowType": "USER_ACCOUNT_TRANSFER", // balance flow type name
    "flowName": "Transfer", // balance flow type Explanation
    "change": "-12.5", // change
    "total": "379.624059937852365", // total asset after change
    "created": "1579093587214"
  },
  {
    "id": "536072393645448960",
    "accountId": "122216245228131",
    "tokenId": "USDT",
    "tokenName": "USDT",
    "flowTypeValue": 7,
    "flowType": "AIRDROP",
    "flowName": "Airdrop",
    "change": "-2000",
    "total": "918662.0917630848",
    "created": "1578640809195"
  }
]
```

**Explanation**

1. Main-account API can query balance flow for token account and other accounts (including sub-accounts, or designated `accountType` and `accountIndex` accounts)
2. Sub-account API can only query current sub-account, therefore `accountType` and `accountIndex` is not required.

**Balance flow types**

Category|Parameter Type Name|Parameter Type Id|Explanation|
------|------|------|------|
General Balance Flow | TRADE | 1 | trades
General Balance Flow |FEE | 2 | trading fees
General Balance Flow |TRANSFER | 3 | transfer
General Balance Flow |DEPOSIT | 4 | deposit
Derivatives | MAKER_REWARD | 27 | maker reward
Derivatives | PNL | 28 | PnL from contracts
Derivatives | SETTLEMENT | 30 | Settlement
Derivatives | LIQUIDATION | 31 | Liquidation
Derivatives | FUNDING_SETTLEMENT | 32 | Funding fee settlement
OTC | OTC_BUY_COIN | 65 | OTC buy coin
OTC | OTC_SELL_COIN | 66 | OTC sell coin
OTC | OTC_FEE | 73 | OTC fees
OTC | OTC_TRADE | 200 | Old OTC balance flow
Campaign | ACTIVITY_AWARD | 67 | Campaign reward
Campaign | INVITATION_REFERRAL_BONUS | 68 | User rebates
Campaign | REGISTER_BONUS | 69 | Registration reward
Campaign | AIRDROP | 70 | Airdrop
Campaign | MINE_REWARD | 71 | Mining reward

