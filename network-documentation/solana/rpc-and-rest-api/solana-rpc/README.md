---
description: Learn how to interact with the Solana RPC
---

# Solana RPC

After connecting to the RPC PubSub websocket at `ws://<ADDRESS>/`:

* Submit subscription requests to the websocket using the methods below
* Multiple subscriptions may be active at once
* Many subscriptions take the optional [`commitment` parameter](https://docs.solana.com/developing/clients/jsonrpc-api#configuring-state-commitment), defining how finalized a change should be to trigger a notification. For subscriptions, if commitment is unspecified, the default value is `"finalized"`.

### accountSubscribe

Subscribe to an account to receive notifications when the lamports or data for a given account public key changes

**Parameters:**

* `<string>` - account Pubkey, as base-58 encoded string
* `<object>` - \(optional\) Configuration object containing the following optional fields:
  * `<object>` - \(optional\) [Commitment](https://docs.solana.com/developing/clients/jsonrpc-api#configuring-state-commitment)
  * `encoding: <string>` - encoding for Account data, either "base58" \(_slow_\), "base64", "base64+zstd" or "jsonParsed". "jsonParsed" encoding attempts to use program-specific state parsers to return more human-readable and explicit account state data. If "jsonParsed" is requested but a parser cannot be found, the field falls back to binary encoding, detectable when the `data` field is type `<string>`.

**Results:**

* `<number>` - Subscription id \(needed to unsubscribe\)

**Example request:** 

```javascript
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "accountSubscribe",
  "params": [
    "CM78CPUeXjn8o3yroDHxUtKsZZgoy4GPkPPXfouKNH12",
    {
      "encoding": "base64",
      "commitment": "finalized"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "accountSubscribe",
  "params": [
    "CM78CPUeXjn8o3yroDHxUtKsZZgoy4GPkPPXfouKNH12",
    {
      "encoding": "jsonParsed"
    }
  ]
}
```

**Example result:** 

```javascript
{"jsonrpc": "2.0","result": 23784,"id": 1}
```

**Notification format:** 

Base58 encoding: 

```javascript
{
  "jsonrpc": "2.0",
  "method": "accountNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5199307
      },
      "value": {
        "data": ["11116bv5nS2h3y12kD1yUKeMZvGcKLSjQgX6BeV7u1FrjeJcKfsHPXHRDEHrBesJhZyqnnq9qJeUuF7WHxiuLuL5twc38w2TXNLxnDbjmuR", "base58"],
        "executable": false,
        "lamports": 33594,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 635
      }
    },
    "subscription": 23784
  }
}
```

Parsed-JSON encoding: 

```javascript
{
  "jsonrpc": "2.0",
  "method": "accountNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5199307
      },
      "value": {
        "data": {
           "program": "nonce",
           "parsed": {
              "type": "initialized",
              "info": {
                 "authority": "Bbqg1M4YVVfbhEzwA9SpC9FhsaG83YMTYoR4a8oTDLX",
                 "blockhash": "LUaQTmM7WbMRiATdMMHaRGakPtCkc2GHtH57STKXs6k",
                 "feeCalculator": {
                    "lamportsPerSignature": 5000
                 }
              }
           }
        },
        "executable": false,
        "lamports": 33594,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 635
      }
    },
    "subscription": 23784
  }
}
```

### accountUnsubscribe

Unsubscribe from account change notifications

**Parameters:**

* `<number>` - id of account Subscription to cancel

**Results:**

* `<bool>` - unsubscribe success message

**Example request:** 

```javascript
{"jsonrpc":"2.0", "id":1, "method":"accountUnsubscribe", "params":[0]}

```

**Example result:** 

```javascript
{"jsonrpc": "2.0","result": true,"id": 1}
```

### logsSubscribe

Subscribe to transaction logging

**Parameters:**

* `filter: <string>|<object>` - filter criteria for the logs to receive results by account type; currently supported:
  * "all" - subscribe to all transactions except for simple vote transactions
  * "allWithVotes" - subscribe to all transactions including simple vote transactions
  * `{ "mentions": [ <string> ] }` - subscribe to all transactions that mention the provided Pubkey \(as base-58 encoded string\)
* `<object>` - \(optional\) Configuration object containing the following optional fields:
  * \(optional\) [Commitment](https://docs.solana.com/developing/clients/jsonrpc-api#configuring-state-commitment)

**Results:**

* `<integer>` - Subscription id \(needed to unsubscribe\)

**Example result:** 

```javascript
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "logsSubscribe",
  "params": [
    {
      "mentions": [ "11111111111111111111111111111111" ]
    },
    {
      "commitment": "finalized"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "logsSubscribe",
  "params": [ "all" ]
}
```

**Example output:** 

```javascript
{"jsonrpc": "2.0","result": 24040,"id": 1}
```

**Notification format:** 

Base58 encoding

```javascript
{
  "jsonrpc": "2.0",
  "method": "logsNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5208469
      },
      "value": {
        "signature": "5h6xBEauJ3PK6SWCZ1PGjBvj8vDdWG3KpwATGy1ARAXFSDwt8GFXM7W5Ncn16wmqokgpiKRLuS83KUxyZyv2sUYv",
        "err": null,
        "logs": [
          "BPF program 83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri success"
        ]
      }
    },
    "subscription": 24040
  }
}
```

### logsUnsubscribe

Unsubscribe from transaction logging

**Parameters:**

* `<integer>` - id of subscription to cancel

**Results:**

* `<bool>` - unsubscribe success message

**Example request:**

```javascript
{"jsonrpc":"2.0", "id":1, "method":"logsUnsubscribe", "params":[0]}
```

**Example output:** 

```javascript
{"jsonrpc": "2.0","result": true,"id": 1}
```

### programSubscribe

Subscribe to a program to receive notifications when the lamports or data for a given account owned by the program changes

**Parameters:**

* `<string>` - program\_id Pubkey, as base-58 encoded string
* `<object>` - \(optional\) Configuration object containing the following optional fields:
  * \(optional\) [Commitment](https://docs.solana.com/developing/clients/jsonrpc-api#configuring-state-commitment)
  * `encoding: <string>` - encoding for Account data, either "base58" \(_slow_\), "base64", "base64+zstd" or "jsonParsed". "jsonParsed" encoding attempts to use program-specific state parsers to return more human-readable and explicit account state data. If "jsonParsed" is requested but a parser cannot be found, the field falls back to base64 encoding, detectable when the `data` field is type `<string>`.
  * \(optional\) `filters: <array>` - filter results using various [filter objects](https://docs.solana.com/developing/clients/jsonrpc-api#filters); account must meet all filter criteria to be included in results

**Results:**

* `<integer>` - Subscription id \(needed to unsubscribe\)

**Example request:**

```javascript
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "programSubscribe",
  "params": [
    "11111111111111111111111111111111",
    {
      "encoding": "base64",
      "commitment": "finalized"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "programSubscribe",
  "params": [
    "11111111111111111111111111111111",
    {
      "encoding": "jsonParsed"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "programSubscribe",
  "params": [
    "11111111111111111111111111111111",
    {
      "encoding": "base64",
      "filters": [
        {
          "dataSize": 80
        }
      ]
    }
  ]
}
```

**Example output:** 

```javascript
{"jsonrpc": "2.0","result": 24040,"id": 1}
```

**Notification format:**

Base58 encoding: 

```javascript
{
  "jsonrpc": "2.0",
  "method": "programNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5208469
      },
      "value": {
        "pubkey": "H4vnBqifaSACnKa7acsxstsY1iV1bvJNxsCY7enrd1hq",
        "account": {
          "data": ["11116bv5nS2h3y12kD1yUKeMZvGcKLSjQgX6BeV7u1FrjeJcKfsHPXHRDEHrBesJhZyqnnq9qJeUuF7WHxiuLuL5twc38w2TXNLxnDbjmuR", "base58"],
          "executable": false,
          "lamports": 33594,
          "owner": "11111111111111111111111111111111",
          "rentEpoch": 636
        },
      }
    },
    "subscription": 24040
  }
}
```



