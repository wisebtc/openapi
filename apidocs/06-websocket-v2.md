# Websocket V2

* The base endpoint is [here](01-endpoint.md)
* Raw streams are accessed at **/openapi/quote/ws/v2**

The V2 websocket supports faster streaming, therefore we recommend all previous users to switch to  the V2 version. The subscription message structure has changed for all streams. Please see below for changes.

## Full Depth Stream

**Subscription:**
```javascript
{
  "topic": "depth",
  "event": "sub",
  "params": {
    "binary": false,
    "symbol": "$symbol",
    }
}
```

**Return:**
```javascript
{
  "topic": "depth",
  "params": {
    "symbol": "BTCUSDT",
    "binary": "false"
  },
  "data": {
    "s": "BTCUSDT",
    "t": 1582001376853,
    "v": "13850022_2",
    "b": [
      [
        "9780.79", // price
        "0.01" // quantity
      ],
      [
        "9780.5",
        "0.1"
      ],
      [
        "9780.4",
        "0.517813"
      ], ...
    "a": [
      [
        "9781.21", // price
        "0.042842" // quantity
      ],
      [
        "9782",
        "0.3"
      ],
      [
        "9782.1",
        "0.226"
      ], ...
    ]
  }
}
```

## Market Tickers Stream

**Subscription:**
```javascript
{
  "topic": "realtimes",
  "event": "sub",
  "params": {
    "binary": false,
    "symbol": "$symbol",
  }
}
```

**Return:**
```javascript
{
  "topic": "realtimes",
  "params": {
    "symbol": "BTCUSDT",
    "binary": "false"
  },
  "data": {
    "t": 1582001616500,
    "s": "BTCUSDT",
    "o": "9736.5",
    "h": "9830.19",
    "l": "9455.71",
    "c": "9796.75",
    "v": "77211.561764",
    "qv": "740412516.91255711",
    "m": "0.0062"
  }
}
```

## Trade Streams

**Subscription:**
```javascript
{
  "topic": "trade",
  "event": "sub",
  "params": {
    "binary": false, // Whether data returned is in binary format
    "symbol": "$symbol"
  }
}
```

**Return:**
```javascript
{
  "topic": "trade",
  "params": {
    "symbol": "BTCUSDT",
    "binary": "false"
  },
  "data": {
    "v": "564265886622695424",
    "t": 1582001735462,
    "p": "9787.5",
    "q": "0.195009",
    "m": true // true=buy, vice versa
  }
}
```

## Bookticker

**Subscription:**
```javascript
{
  "topic": "bookTicker",
  "event": "sub",
  "params": {
    "binary": false, // Whether data returned is in binary format
    "symbol": "$symbol"
  }
}
```

**Return:**
```javascript
{
  "topic": "bookTicker",
  "params": {
    "symbol": "BTCUSDT",
    "binary": "false"
  },
  "data": {
    "symbol": "BTCUSDT",
    "bidPrice": "9797.79",
    "bidQty": "0.177976",
    "askPrice": "9799",
    "askQty": "0.65",
    "time": 1582001830346
  }
}
```

## Kline

**Subscription:**
```javascript
{
  "topic": "kline",
  "event": "sub",
  "params": {
    "binary": false,
    "symbol": "$symbol",
    "klineType": "1m"
  }
}
```

**Return:**
```javascript
{
  "topic": "kline",
  "params": {
    "symbol": "BTCUSDT",
    "binary": "false",
    "klineType": "1m"
  },
  "data": {
    "t": 1582001880000,
    "s": "BTCUSDT",
    "c": "9799.4",
    "h": "9801.4",
    "l": "9798.91",
    "o": "9799.4",
    "v": "15.917433" //Version, ignore for now
  }
}
```