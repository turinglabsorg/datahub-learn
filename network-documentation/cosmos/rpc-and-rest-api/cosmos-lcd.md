---
description: Learn how to interact with the Cosmos LCD
---

# Cosmos LCD

## Source documentation

[**The Cosmos LCD's source documentation can be found here**](https://cosmos.network/rpc/v0.37.9). 

A REST interface for state queries, transaction generation and broadcasting. 

## **Transactions**

**Search, encode, or broadcast transactions.**

### `GET/txs/{hash}`

**Description**

Get a transaction by hash ****

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **hash** | string \* required | Transaction hash  |

**Example JSON Output**

```javascript
{
  "hash": "D085138D913993919295FF4B0A9107F1F2CDE0D37A87CE0644E217CBF3B49656",
  "height": 368,
  "tx": {
    "msg": [
      "string"
    ],
    "fee": {
      "gas": "string",
      "amount": [
        {
          "denom": "stake",
          "amount": "50"
        }
      ]
    },
    "memo": "string",
    "signature": {
      "signature": "MEUCIQD02fsDPra8MtbRsyB1w7bqTM55Wu138zQbFcWx4+CFyAIge5WNPfKIuvzBZ69MyqHsqD8S1IwiEp+iUb6VSdtlpgY=",
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "Avz04VhtKJh8ACCVzlI8aTosGy0ikFXKIVHQ3jKMrosH"
      },
      "account_number": "0",
      "sequence": "0"
    }
  },
  "result": {
    "log": "string",
    "gas_wanted": "200000",
    "gas_used": "26354",
    "tags": [
      {
        "key": "string",
        "value": "string"
      }
    ]
  }
}
```

### `GET/txs`

**Description**

Search transactions

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **message.action** | string  | Transaction events such as 'message.action=send' which results in the following /txs?message.action=send'  |
| **message.sender** | string | Transaction tags with sender: 'GET /txs message.action=send&message.sender=cosmos16xyempempp92x9hyzz9wrgf94r6j9h5f06pxxv' |
| **page** | integer | Page number |
| **limit** | integer | Maximum number of items per page |
| **tx.minheight** | integer | Transactions on blocks with height greater or equal this value |
| **tx.maxheight** | integer | Transactions on blocks with height less than or equal this value |

**Example JSON Output**

```javascript
{
  "total_count": 1,
  "count": 1,
  "page_number": 1,
  "page_total": 1,
  "limit": 30,
  "txs": [
    {
      "hash": "D085138D913993919295FF4B0A9107F1F2CDE0D37A87CE0644E217CBF3B49656",
      "height": 368,
      "tx": {
        "msg": [
          "string"
        ],
        "fee": {
          "gas": "string",
          "amount": [
            {
              "denom": "stake",
              "amount": "50"
            }
          ]
        },
        "memo": "string",
        "signature": {
          "signature": "MEUCIQD02fsDPra8MtbRsyB1w7bqTM55Wu138zQbFcWx4+CFyAIge5WNPfKIuvzBZ69MyqHsqD8S1IwiEp+iUb6VSdtlpgY=",
          "pub_key": {
            "type": "tendermint/PubKeySecp256k1",
            "value": "Avz04VhtKJh8ACCVzlI8aTosGy0ikFXKIVHQ3jKMrosH"
          },
          "account_number": "0",
          "sequence": "0"
        }
      },
      "result": {
        "log": "string",
        "gas_wanted": "200000",
        "gas_used": "26354",
        "tags": [
          {
            "key": "string",
            "value": "string"
          }
        ]
      }
    }
  ]
}
```

### `POST/txs`

**Description**

Broadcast a signed transaction 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **txBroadcast** | object | The transaction must be signed **** StdTx. The supported broadcast modes include `"block"`\(return after tx commit\), `"sync"`\(return afer CheckTx\) and `"async"`\(return right away\). |

**Example JSON Output**

```javascript
{
  "tx": {
    "msg": [
      "string"
    ],
    "fee": {
      "gas": "string",
      "amount": [
        {
          "denom": "stake",
          "amount": "50"
        }
      ]
    },
    "memo": "string",
    "signature": {
      "signature": "MEUCIQD02fsDPra8MtbRsyB1w7bqTM55Wu138zQbFcWx4+CFyAIge5WNPfKIuvzBZ69MyqHsqD8S1IwiEp+iUb6VSdtlpgY=",
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "Avz04VhtKJh8ACCVzlI8aTosGy0ikFXKIVHQ3jKMrosH"
      },
      "account_number": "0",
      "sequence": "0"
    }
  },
  "mode": "block"
}
```

### `POST/txs/encode`

**Description**

Encode a transaction to the Amino wire format 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **tx** | object | The transaction to encode |

**Example JSON Output**

```javascript
{
  "tx": {
    "msg": [
      "string"
    ],
    "fee": {
      "gas": "string",
      "amount": [
        {
          "denom": "stake",
          "amount": "50"
        }
      ]
    },
    "memo": "string",
    "signature": {
      "signature": "MEUCIQD02fsDPra8MtbRsyB1w7bqTM55Wu138zQbFcWx4+CFyAIge5WNPfKIuvzBZ69MyqHsqD8S1IwiEp+iUb6VSdtlpgY=",
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "Avz04VhtKJh8ACCVzlI8aTosGy0ikFXKIVHQ3jKMrosH"
      },
      "account_number": "0",
      "sequence": "0"
    }
  }
}
```

## **Tendermint RPC**

**Tendermint APIs, such as query blocks, transactions and validator set**

### `GET/node_info`

**Description**

The properties of the connected node ****

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "application_version": {
    "build_tags": "string",
    "client_name": "string",
    "commit": "string",
    "go": "string",
    "name": "string",
    "server_name": "string",
    "version": "string"
  },
  "node_info": {
    "id": "string",
    "moniker": "validator-name",
    "protocol_version": {
      "p2p": 7,
      "block": 10,
      "app": 0
    },
    "network": "gaia-2",
    "channels": "string",
    "listen_addr": "192.168.56.1:26656",
    "version": "0.15.0",
    "other": {
      "tx_index": "on",
      "rpc_address": "tcp://0.0.0.0:26657"
    }
  }
}
```

### `GET/syncing`

**Description**

Syncing state of node

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "syncing": true
}
```

### `GET/blocks/latest`

**Description**

Get the latest block 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "block_meta": {
    "header": {
      "chain_id": "cosmoshub-2",
      "height": 1,
      "time": "2017-12-30T05:53:09.287+01:00",
      "num_txs": 0,
      "last_block_id": {
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
        "parts": {
          "total": 0,
          "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
        }
      },
      "total_txs": 35,
      "last_commit_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "data_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "next_validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "consensus_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "app_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "last_results_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "evidence_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "proposer_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
      "version": {
        "block": 10,
        "app": 0
      }
    },
    "block_id": {
      "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "parts": {
        "total": 0,
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
      }
    }
  },
  "block": {
    "header": {
      "chain_id": "cosmoshub-2",
      "height": 1,
      "time": "2017-12-30T05:53:09.287+01:00",
      "num_txs": 0,
      "last_block_id": {
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
        "parts": {
          "total": 0,
          "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
        }
      },
      "total_txs": 35,
      "last_commit_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "data_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "next_validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "consensus_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "app_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "last_results_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "evidence_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "proposer_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
      "version": {
        "block": 10,
        "app": 0
      }
    },
    "txs": [
      "string"
    ],
    "evidence": [
      "string"
    ],
    "last_commit": {
      "block_id": {
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
        "parts": {
          "total": 0,
          "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
        }
      },
      "precommits": [
        {
          "validator_address": "string",
          "validator_index": "0",
          "height": "0",
          "round": "0",
          "timestamp": "2017-12-30T05:53:09.287+01:00",
          "type": 2,
          "block_id": {
            "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
            "parts": {
              "total": 0,
              "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
            }
          },
          "signature": "7uTC74QlknqYWEwg7Vn6M8Om7FuZ0EO4bjvuj6rwH1mTUJrRuMMZvAAqT9VjNgP0RA/TDp6u/92AqrZfXJSpBQ=="
        }
      ]
    }
  }
}
```

### `GET/blocks/{height}`

**Description**

Get a block at a certain height 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **height** | number \* required | Block height |

**Example JSON Output**

```javascript
{
  "block_meta": {
    "header": {
      "chain_id": "cosmoshub-2",
      "height": 1,
      "time": "2017-12-30T05:53:09.287+01:00",
      "num_txs": 0,
      "last_block_id": {
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
        "parts": {
          "total": 0,
          "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
        }
      },
      "total_txs": 35,
      "last_commit_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "data_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "next_validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "consensus_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "app_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "last_results_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "evidence_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "proposer_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
      "version": {
        "block": 10,
        "app": 0
      }
    },
    "block_id": {
      "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "parts": {
        "total": 0,
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
      }
    }
  },
  "block": {
    "header": {
      "chain_id": "cosmoshub-2",
      "height": 1,
      "time": "2017-12-30T05:53:09.287+01:00",
      "num_txs": 0,
      "last_block_id": {
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
        "parts": {
          "total": 0,
          "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
        }
      },
      "total_txs": 35,
      "last_commit_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "data_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "next_validators_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "consensus_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "app_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "last_results_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "evidence_hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
      "proposer_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
      "version": {
        "block": 10,
        "app": 0
      }
    },
    "txs": [
      "string"
    ],
    "evidence": [
      "string"
    ],
    "last_commit": {
      "block_id": {
        "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
        "parts": {
          "total": 0,
          "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
        }
      },
      "precommits": [
        {
          "validator_address": "string",
          "validator_index": "0",
          "height": "0",
          "round": "0",
          "timestamp": "2017-12-30T05:53:09.287+01:00",
          "type": 2,
          "block_id": {
            "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
            "parts": {
              "total": 0,
              "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04"
            }
          },
          "signature": "7uTC74QlknqYWEwg7Vn6M8Om7FuZ0EO4bjvuj6rwH1mTUJrRuMMZvAAqT9VjNgP0RA/TDp6u/92AqrZfXJSpBQ=="
        }
      ]
    }
  }
}
```

### `GET/validatorsets/latest`

**Description**

Get the latest validator set 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "block_height": "string",
  "validators": [
    {
      "address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
      "pub_key": "cosmosvalconspub1zcjduepq0vu2zgkgk49efa0nqwzndanq5m4c7pa3u4apz4g2r9gspqg6g9cs3k9cuf",
      "voting_power": "1000",
      "proposer_priority": "1000"
    }
  ]
}
```

### `GET/validatorsets/{height}`

**Description**

Get a validator set a certain height 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **height** | number \* required | Block height |

**Example JSON Output**

```javascript
{
  "block_height": "string",
  "validators": [
    {
      "address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
      "pub_key": "cosmosvalconspub1zcjduepq0vu2zgkgk49efa0nqwzndanq5m4c7pa3u4apz4g2r9gspqg6g9cs3k9cuf",
      "voting_power": "1000",
      "proposer_priority": "1000"
    }
  ]
}
```

## **Authentication**

**Authenticate accounts**

### `GET/auth/accounts/{address}`

**Description**

Get the account information on blockchain

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **address** | string \* required | Account address |

**Example JSON Output**

```javascript
{
  "type": "string",
  "value": {
    "account_number": "string",
    "address": "string",
    "coins": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "public_key": {
      "type": "string",
      "value": "string"
    },
    "sequence": "string"
  }
}
```

## **Bank**

**Create and broadcast transactions**

### `GET/bank/balances/{address}`

**Description**

Get the account balances 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **address** | string \* required | Account address |

**Example JSON Output**

```javascript
[
  {
    "denom": "stake",
    "amount": "50"
  }
]
```

### `POST/bank/accounts/{address}/transfers`

**Description**

Send coins from one account to another 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **address** | string \* required | Account address in bech32 format |
| **account** | object \* required | The sender and transaction information |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "amount": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ]
}
```

## **Staking**

**Stake module APIs**

### `GET​/staking​/delegators​/{delegatorAddr}​/delegations`

**Description**

Get all delegations from a delegator 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |

**Example JSON Output**

```javascript
[
  {
    "delegator_address": "string",
    "validator_address": "string",
    "shares": "string",
    "height": 0
  }
]
```

### `POST​/staking​/delegators​/{delegatorAddr}​/delegations`

**Description**

Submit delegation 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegation** | object | The password of the account to remove from the KMS  **** |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "delegator_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "validator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "delegation": {
    "denom": "stake",
    "amount": "50"
  }
}
```

### `GET​/staking​/delegators​/{delegatorAddr}​/delegations​/{validatorAddr}`

**Description**

Query the current delegation between a delegator and a validator 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech 32 AccAddress of Delegator |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
{
  "delegator_address": "string",
  "validator_address": "string",
  "shares": "string",
  "height": 0
}
```

### `GET​/staking​/delegators​/{delegatorAddr}​/unbonding_delegations`

**Description**

Get all unbonding delegations 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech 32 AccAddress of Delegator |

**Example JSON Output**

```javascript
[
  {
    "delegator_address": "string",
    "validator_address": "string",
    "initial_balance": "string",
    "balance": "string",
    "creation_height": 0,
    "min_time": 0
  }
]
```

### `POST​/staking​/delegators​/{delegatorAddr}​/unbonding_delegations`

**Description**

Submit an unbonding delegation 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegation** | object | The password of the account to remove from the KMS   |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "delegator_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "validator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "shares": "100"
}
```

### `GET​/staking​/delegators​/{delegatorAddr}​/unbonding_delegations​/{validatorAddr}`

**Description**

Query all unbonding delegations between a delegator and a validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech 32 AccAddress of Delegator |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
{
  "delegator_address": "string",
  "validator_address": "string",
  "entries": [
    {
      "initial_balance": "string",
      "balance": "string",
      "creation_height": "string",
      "min_time": "string"
    }
  ]
}
```

### `GET​/staking​/redelegations`

**Description**

Get all redelegations \(filter by query params\) __

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator** | string | Bech32 AccAddress of Delegator |
| **validator\_from** | string | Bech32 ValAddress of SrcValidator |
| **validator\_to** | string | Bech32 ValAddress of DstValidator |

**Example JSON Output**

```javascript
[
  {
    "delegator_address": "string",
    "validator_src_address": "string",
    "validator_dst_address": "string",
    "entries": [
      null
    ]
  }
]
```

### `POST​/staking​/delegators​/{delegatorAddr}​/redelegations`

**Description**

Submit a redelegation 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegation** | object | The sender and transaction information |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "delegator_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "validator_src_addressess": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "validator_dst_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "shares": "100"
}
```

### `GET​/staking​/delegators​/{delegatorAddr}​/validators`

**Description**

Query all validators that a delegator is bonded to 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |

**Example JSON Output**

```javascript
[
  {
    "operator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
    "consensus_pubkey": "cosmosvalconspub1zcjduepq0vu2zgkgk49efa0nqwzndanq5m4c7pa3u4apz4g2r9gspqg6g9cs3k9cuf",
    "jailed": true,
    "status": 0,
    "tokens": "string",
    "delegator_shares": "string",
    "description": {
      "moniker": "string",
      "identity": "string",
      "website": "string",
      "details": "string"
    },
    "bond_height": "0",
    "bond_intra_tx_counter": 0,
    "unbonding_height": "0",
    "unbonding_time": "1970-01-01T00:00:00Z",
    "commission": {
      "rate": "0",
      "max_rate": "0",
      "max_change_rate": "0",
      "update_time": "1970-01-01T00:00:00Z"
    }
  }
]
```

### `GET​/staking​/delegators​/{delegatorAddr}​/validators​/{validatorAddr}`

**Description**

Query a validator that a delegator is bonded to 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |
| **validatorAddr** | string \* required | Bech32 ValAddress of Delegator  |

**Example JSON Output**

```javascript
{
  "operator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "consensus_pubkey": "cosmosvalconspub1zcjduepq0vu2zgkgk49efa0nqwzndanq5m4c7pa3u4apz4g2r9gspqg6g9cs3k9cuf",
  "jailed": true,
  "status": 0,
  "tokens": "string",
  "delegator_shares": "string",
  "description": {
    "moniker": "string",
    "identity": "string",
    "website": "string",
    "details": "string"
  },
  "bond_height": "0",
  "bond_intra_tx_counter": 0,
  "unbonding_height": "0",
  "unbonding_time": "1970-01-01T00:00:00Z",
  "commission": {
    "rate": "0",
    "max_rate": "0",
    "max_change_rate": "0",
    "update_time": "1970-01-01T00:00:00Z"
  }
}
```

### `GET​/staking​/validators`

**Description**

Get all validator candidates. By default it returns only the bonded validators 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **status** | string | The validator bond status. Must be either 'bonded', 'unbonded', or 'unbonding'  |
| **page** | integer | The page number |
| **limit** | integer | The maximum number of items per page |

**Example JSON Output**

```javascript
[
  {
    "operator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
    "consensus_pubkey": "cosmosvalconspub1zcjduepq0vu2zgkgk49efa0nqwzndanq5m4c7pa3u4apz4g2r9gspqg6g9cs3k9cuf",
    "jailed": true,
    "status": 0,
    "tokens": "string",
    "delegator_shares": "string",
    "description": {
      "moniker": "string",
      "identity": "string",
      "website": "string",
      "details": "string"
    },
    "bond_height": "0",
    "bond_intra_tx_counter": 0,
    "unbonding_height": "0",
    "unbonding_time": "1970-01-01T00:00:00Z",
    "commission": {
      "rate": "0",
      "max_rate": "0",
      "max_change_rate": "0",
      "update_time": "1970-01-01T00:00:00Z"
    }
  }
]
```

### `GET​/staking​/validators​/{validatorAddr}`

**Description**

Query the information from a single validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
{
  "operator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "consensus_pubkey": "cosmosvalconspub1zcjduepq0vu2zgkgk49efa0nqwzndanq5m4c7pa3u4apz4g2r9gspqg6g9cs3k9cuf",
  "jailed": true,
  "status": 0,
  "tokens": "string",
  "delegator_shares": "string",
  "description": {
    "moniker": "string",
    "identity": "string",
    "website": "string",
    "details": "string"
  },
  "bond_height": "0",
  "bond_intra_tx_counter": 0,
  "unbonding_height": "0",
  "unbonding_time": "1970-01-01T00:00:00Z",
  "commission": {
    "rate": "0",
    "max_rate": "0",
    "max_change_rate": "0",
    "update_time": "1970-01-01T00:00:00Z"
  }
}
```

### `GET​/staking​/validators​/{validatorAddr}​/delegations`

**Description**

Get all delegations from a validator 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator  |

**Example JSON Output**

```javascript
[
  {
    "delegator_address": "string",
    "validator_address": "string",
    "shares": "string",
    "height": 0
  }
]
```

### `GET​/staking​/validators​/{validatorAddr}​/unbonding_delegations`

**Description**

Get all unbonding delegations from a validator 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
[
  {
    "delegator_address": "string",
    "validator_address": "string",
    "initial_balance": "string",
    "balance": "string",
    "creation_height": 0,
    "min_time": 0
  }
]
```

### `GET​/staking​/pool`

**Description**

Get the current state of the staking pool 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "loose_tokens": "string",
  "bonded_tokens": "string",
  "inflation_last_time": "string",
  "inflation": "string",
  "date_last_commission_reset": "string",
  "prev_bonded_shares": "string"
}
```

### `GET​/staking​/parameters`

**Description**

Get the current staking parameter values

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "inflation_rate_change": "string",
  "inflation_max": "string",
  "inflation_min": "string",
  "goal_bonded": "string",
  "unbonding_time": "string",
  "max_validators": 0,
  "bond_denom": "string"
}
```

## **Governance**

**Governance module APIs**

### `POST​/gov​/proposals`

**Description**

Submit a proposal 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **post\_proposal\_body** | object \* required | Valid value of `"proposal_type"` can be `"text"`, `"parameter_change"`, `"software_upgrade"` |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "title": "string",
  "description": "string",
  "proposal_type": "text",
  "proposer": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "initial_deposit": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ]
}
```

### `GET​/gov​/proposals`

**Description**

Query proposals 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **voter** | string | Transaction hash |
| **deposit** | string | Depositor address |
| **status** | string | Proposal status, valid values can be `"deposit_period"`, `"voting_period"`, `"passed"`, `"rejected"` |

**Example JSON Output**

```javascript
[
  {
    "proposal_id": 0,
    "title": "string",
    "description": "string",
    "proposal_type": "string",
    "proposal_status": "string",
    "final_tally_result": {
      "yes": "0.0000000000",
      "abstain": "0.0000000000",
      "no": "0.0000000000",
      "no_with_veto": "0.0000000000"
    },
    "submit_time": "string",
    "total_deposit": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "voting_start_time": "string"
  }
]
```

`POST​/gov​/proposals​/param_change`

**Description**

Generate a parameter change proposal transaction 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **post\_proposal\_body** | object \* required | The parameter change proposal body that contains all parameter changes |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "title": "string",
  "description": "string",
  "proposer": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "deposit": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ],
  "changes": [
    {
      "subspace": "staking",
      "key": "MaxValidators",
      "subkey": "",
      "value": {}
    }
  ]
}
```

### `GET​/gov​/proposals​/{proposalId}`

**Description**

Query a proposal 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID |

**Example JSON Output**

```javascript
{
  "proposal_id": 0,
  "title": "string",
  "description": "string",
  "proposal_type": "string",
  "proposal_status": "string",
  "final_tally_result": {
    "yes": "0.0000000000",
    "abstain": "0.0000000000",
    "no": "0.0000000000",
    "no_with_veto": "0.0000000000"
  },
  "submit_time": "string",
  "total_deposit": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ],
  "voting_start_time": "string"
}
```

### `GET​/gov​/proposals​/{proposalId}​/proposer`

**Description**

Query a proposer

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID |

**Example JSON Output**

```javascript
{
  "proposal_id": "string",
  "proposer": "string"
}
```

### `GET​/gov​/proposals​/{proposalId}​/deposits`

**Description**

Query deposits 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID  |

**Example JSON Output**

```javascript
[
  {
    "amount": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "proposal_id": "string",
    "depositor": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27"
  }
]
```

### `POST​/gov​/proposals​/{proposalId}​/deposits`

**Description**

Deposit tokens to a proposal 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID |
| **post\_deposit\_body**  | object \* required |  |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "depositor": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "amount": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ]
}
```

### `GET​/gov​/proposals​/{proposalId}​/deposits​/{depositor}`

**Description**

Query deposit 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID |
| depositor | string \* required | Bech32 depositor address |

**Example JSON Output**

```javascript
{
  "amount": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ],
  "proposal_id": "string",
  "depositor": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27"
}
```

### `GET​/gov​/proposals​/{proposalId}​/votes`

**Description**

Query voters

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID |

**Example JSON Output**

```javascript
[
  {
    "voter": "string",
    "proposal_id": "string",
    "option": "string"
  }
]
```

### `POST​/gov​/proposals​/{proposalId}​/votes`

**Description**

Vote a proposal 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Transaction hash |
| **post\_vote\_body** | object \* required  | Valid value of `"option"` field can be `"yes"`, `"no"`, `"no_with_veto"` and `"abstain"` |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "voter": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27",
  "option": "yes"
}
```

### `GET​/gov​/proposals​/{proposalId}​/votes​/{voter}`

**Description**

Query vote 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId** | string \* required | Proposal ID |
| **voter** | string \* required | Bech32 voter address |

**Example JSON Output**

```javascript
{
  "voter": "string",
  "proposal_id": "string",
  "option": "string"
}
```

### `GET​/gov​/proposals​/{proposalId}​/tally`

**Description**

Get a proposal's tally result at the current time 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposalId**  | string \* required | Proposal ID  |

**Example JSON Output**

```javascript
{
  "yes": "0.0000000000",
  "abstain": "0.0000000000",
  "no": "0.0000000000",
  "no_with_veto": "0.0000000000"
}
```

### `GET​/gov​/parameters​/deposit`

**Description**

Query governance deposit parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "min_deposit": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ],
  "max_deposit_period": "86400000000000"
}
```

### `GET​/gov​/parameters​/tallying`

**Description**

Query governance tally parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "threshold": "0.5000000000",
  "veto": "0.3340000000",
  "governance_penalty": "0.0100000000"
}
```

### `GET​/gov​/parameters​/voting`

**Description**

Query governance parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "voting_period": "86400000000000"
}
```

## **Slashing**

**Slashing module APIs**

### `GET​/slashing​/signing_infos`

**Description**

Get sign info of all given validators 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **page** | integer \* required | Page number |
| **limit** | integer \* required | Maximum number of items per page |

**Example JSON Output**

```javascript
[
  {
    "start_height": "string",
    "index_offset": "string",
    "jailed_until": "string",
    "missed_blocks_counter": "string"
  }
]
```

### `POST​/slashing​/validators​/{validatorAddr}​/unjail`

**Description**

Unjail a jailed validator 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 validator address |
| **UnjailBody** | object \* required |  |

**Example JSON Output**

```javascript
{
  "base_req": {
    "msg": [
      "string"
    ],
    "fee": {
      "gas": "string",
      "amount": [
        {
          "denom": "stake",
          "amount": "50"
        }
      ]
    },
    "memo": "string",
    "signature": {
      "signature": "MEUCIQD02fsDPra8MtbRsyB1w7bqTM55Wu138zQbFcWx4+CFyAIge5WNPfKIuvzBZ69MyqHsqD8S1IwiEp+iUb6VSdtlpgY=",
      "pub_key": {
        "type": "tendermint/PubKeySecp256k1",
        "value": "Avz04VhtKJh8ACCVzlI8aTosGy0ikFXKIVHQ3jKMrosH"
      },
      "account_number": "0",
      "sequence": "0"
    }
  }
}
```

### `GET​/slashing​/parameters`

**Description**

Get the current slashing parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "max_evidence_age": "string",
  "signed_blocks_window": "string",
  "min_signed_per_window": "string",
  "double_sign_unbond_duration": "string",
  "downtime_unbond_duration": "string",
  "slash_fraction_double_sign": "string",
  "slash_fraction_downtime": "string"
}
```

## **Distribution**

**Fee distribution module APIs**

### `GET​/distribution​/delegators​/{delegatorAddr}​/rewards`

**Description**

Get the total rewards balance from all delegations 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |

**Example JSON Output**

```javascript
{
  "rewards": [
    {
      "validator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
      "reward": [
        {
          "denom": "stake",
          "amount": "50"
        }
      ]
    }
  ],
  "total": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ]
}
```

### `POST​/distribution​/delegators​/{delegatorAddr}​/rewards`

**Description**

Withdraw all the delegator's delegation rewards 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |
| **Withdraw request body** | body |  |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  }
}
```

### `GET​/distribution​/delegators​/{delegatorAddr}​/rewards​/{validatorAddr}`

**Description**

Query a delegation reward

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
[
  {
    "denom": "stake",
    "amount": "50"
  }
]
```

### `POST​/distribution​/delegators​/{delegatorAddr}​/rewards​/{validatorAddr}`

**Description**

Withdraw a delegation reward 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |
| **Withdraw request body** | body |  |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  }
}
```

### `GET​/distribution​/delegators​/{delegatorAddr}​/withdraw_address`

**Description**

Get the rewards withdrawal address 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27
```

### `POST​/distribution​/delegators​/{delegatorAddr}​/withdraw_address`

**Description**

Replace the rewards withdrawal address 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |
| **Withdraw request body** | body |  |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  },
  "withdraw_address": "cosmos1depk54cuajgkzea6zpgkq36tnjwdzv4afc3d27"
}
```

### `GET​/distribution​/validators​/{validatorAddr}`

**Description**

Validator distribution information 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
{
  "operator_address": "cosmosvaloper16xyempempp92x9hyzz9wrgf94r6j9h5f2w4n2l",
  "self_bond_rewards": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ],
  "val_commission": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ]
}
```

### `GET​/distribution​/validators​/{validatorAddr}​/outstanding_rewards`

**Description**

Fee distribution outstanding rewards of a single validator __

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
[
  {
    "denom": "stake",
    "amount": "50"
  }
]
```

### `GET​/distribution​/validators​/{validatorAddr}​/rewards`

**Description**

Commission and self-delegation rewards of a single validator 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validatorAddr** | string \* required | Bech32 OperatorAddress of validator |

**Example JSON Output**

```javascript
[
  {
    "denom": "stake",
    "amount": "50"
  }
]
```

### `POST​/distribution​/validators​/{validatorAddr}​/rewards`

**Description**

Withdraw the validator's rewards 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| validatorAddr | string \* required | Bech32 OperatorAddress of validator |
| **Withdraw request body** | body |  |

**Example JSON Output**

```javascript
{
  "base_req": {
    "from": "cosmos1g9ahr6xhht5rmqven628nklxluzyv8z9jqjcmc",
    "memo": "Sent via Cosmos Voyager 🚀",
    "chain_id": "Cosmos-Hub",
    "account_number": "0",
    "sequence": "1",
    "gas": "200000",
    "gas_adjustment": "1.2",
    "fees": [
      {
        "denom": "stake",
        "amount": "50"
      }
    ],
    "simulate": false
  }
}
```

### `GET​/distribution​/community_pool`

**Description**

Community pool parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
[
  {
    "denom": "stake",
    "amount": "50"
  }
]
```

### `GET​/distribution​/parameters`

**Description**

Fee distribution parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "base_proposer_reward": "string",
  "bonus_proposer_reward": "string",
  "community_tax": "string"
}
```

## **Supply**

**Supply module APIs**

### `GET​/supply​/total`

**Description**

Total supply of coins in the chain 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "total": [
    {
      "denom": "stake",
      "amount": "50"
    }
  ]
}
```

### `GET​/supply​/total​/{denomination}`

**Description**

Total supply of a single coin denomination 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **denomination** | string \* required | Coin denomination |

**Example JSON Output**

```javascript
string
```

## **Mint**

**Minting module APIs**

### `GET​/minting​/parameters`

**Description**

Minting module parameters 

**Parameters**

no parameters

**Example JSON Output**

```javascript
{
  "mint_denom": "string",
  "inflation_rate_change": "string",
  "inflation_max": "string",
  "inflation_min": "string",
  "goal_bonded": "string",
  "blocks_per_year": "string"
}
```

### `GET​/minting​/inflation`

**Description**

Current minting inflation value 

**Parameters**

no parameters

**Example JSON Output**

```javascript
string
```

### `GET​/minting​/annual-provisions`

**Description**

Current minting annual provisions value 

**Parameters**

no parameters

**Example JSON Output**

```javascript
string
```

