---
description: Learn how to interact with the Cosmos LCD
---

# Cosmos LCD

## Source documentation

[**The Cosmos LCD's source documentation can be found here**](https://cosmos.network/rpc/v0.37.9). 

A REST interface for state queries, transaction generation and broadcasting. 

## **Gaia REST**

### `GET/node_info`

**Description**

Get the properties of the connected node 

**Parameters**

No parameters

**Example JSON output**

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

## **Transactions**

**Search, encode, or broadcast transactions.**

### `POST/txs`

**Description**

Broadcast a signed transaction 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **txBroadcast** | object | The transaction must be signed **** StdTx. The supported broadcast modes include `"block"`\(return after tx commit\), `"sync"`\(return afer CheckTx\) and `"async"`\(return right away\). |

**Example Request**

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

**Example JSON Output**

```javascript
{
  "check_tx": {
    "code": 0,
    "data": "data",
    "log": "log",
    "gas_used": 5000,
    "gas_wanted": 10000,
    "info": "info",
    "tags": [
      "",
      ""
    ]
  },
  "deliver_tx": {
    "code": 5,
    "data": "data",
    "log": "log",
    "gas_used": 5000,
    "gas_wanted": 10000,
    "info": "info",
    "tags": [
      "",
      ""
    ]
  },
  "hash": "EE5F3404034C524501629B56E0DDC38FAD651F04",
  "height": 0
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

## **Staking**

**Stake module APIs**

### `POST/staking/delegators/{delegatorAddr}​/delgations`

**Description**

Submit delegation 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegation** | object | The password of the account to remove from the KMS  **** |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |

**Example Request**

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

**Example JSON Output**

```javascript
{
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
```

### `POST/staking/delegators/{delegatorAddr}/unbonding_delegations`

**Description**

Submit an unbonding delegation 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegation** | object | The password of the account to remove from the KMS   |
| **delegatorAddr** | string \* required | Bech32 AccAddress of Delegator |

**Example Request**

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

**Example JSON Output**

```javascript
{
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
```

## **Query**

### **`GET/cosmos/auth/v1beta1/accounts/{address}`**

**Description**

Account returns account details based on address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **address** | string \* required | address defines the address to query for |

**Example JSON Output**

```javascript
{
  "account": {
    "type_url": "string",
    "value": "string"
  }
}
```

### **`GET/cosmos/auth/v1beta1/params`**

**Description**

Params queries all parameters

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "params": {
    "max_memo_characters": "string",
    "tx_sig_limit": "string",
    "tx_size_cost_per_byte": "string",
    "sig_verify_cost_ed25519": "string",
    "sig_verify_cost_secp256k1": "string"
  }
}
```

### **`GET/cosmos/bank/v1beta1/balances/{address}`**

**Description**

Queries the balance of all coins for a single account

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **address** | string \* required | address is the address to query balances for |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "balances": [
    {
      "denom": "string",
      "amount": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/bank/v1beta1/balances/{address}/{denom}`**

**Description**

Queries the balance of a single coin for a single account

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **address** | string \* required | address defines the address to query for |
| **denom** | string \* required | denom is the coin denom to query balances for |

**Example JSON Output**

```javascript
{
  "balance": {
    "denom": "string",
    "amount": "string"
  }
}
```

### **`GET/cosmos/bank/v1beta1/params`**

**Description**

Queries the parameters of x/bank module

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "params": {
    "send_enabled": [
      {
        "denom": "string",
        "enabled": true
      }
    ],
    "default_send_enabled": true
  }
}
```

### **`GET/cosmos/bank/v1beta1/supply`**

**Description**

Queries the total supply of all coins

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "supply": [
    {
      "denom": "string",
      "amount": "string"
    }
  ]
}
```

### **`GET/cosmos/bank/v1beta1/supply/{denom}`**

**Description**

Queries the balance of a single coin for a single account

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **denom** | string \* required | denom is the coin denom to query balances for |

**Example JSON Output**

```javascript
{
  "amount": {
    "denom": "string",
    "amount": "string"
  }
}
```

### **`GET/cosmos/distribution/v1beta1/community_pool`**

**Description**

Queries the community pool coins

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "pool": [
    {
      "denom": "string",
      "amount": "string"
    }
  ]
}
```

### **`GET/cosmos/distribution/v1beta1/delegators/{delegator_address}/rewards`**

**Description**

Queries the total rewards accrued by each validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_address** | string \* required | defines the delegator address to query for |

**Example JSON Output**

```javascript
{
  "rewards": [
    {
      "validator_address": "string",
      "reward": [
        {
          "denom": "string",
          "amount": "string"
        }
      ]
    }
  ],
  "total": [
    {
      "denom": "string",
      "amount": "string"
    }
  ]
}
```



### **`GET/cosmos/distribution/v1beta1/delegators/{delegator_address}/rewards/{validator_address}`**

**Description**

Queries the total rewards accrued by a delegator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_address** | string \* required | defines the delegator address to query for |
| **validator\_address** | string \* required | defines the validator address to query for |

**Example JSON Output**

```javascript
{
  "rewards": [
    {
      "denom": "string",
      "amount": "string"
    }
  ]
}
```

### **`GET/cosmos/distribution/v1beta1/delegators/{delegator_address}/validators`**

**Description**

Queries the validators of a delegator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_address** | string \* required | defines the delegator address to query for |

**Example JSON Output**

```javascript
{
  "validators": [
    "string"
  ]
}
```

### **`GET/cosmos/distribution/v1beta1/delegators/{delegator_address}/withdraw_address`**

**Description**

Queries the withdrawal address of a delegator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_address** | string \* required | defines the delegator address to query for |

**Example JSON Output**

```javascript
{
  "withdraw_address": "string"
}
```

### **`GET/cosmos/distribution/v1beta1/params`**

**Description**

Queries the params of the distribution module 

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "params": {
    "community_tax": "string",
    "base_proposer_reward": "string",
    "bonus_proposer_reward": "string",
    "withdraw_addr_enabled": true
  }
}
```

### **`GET/cosmos/distribution/v1beta1/validators/{validator_address}/commission`**

**Description**

Queries accumulated commission for a validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_address** | string \* required | defines the validator address to query for |

**Example JSON Output**

```javascript
{
  "commission": {
    "commission": [
      {
        "denom": "string",
        "amount": "string"
      }
    ]
  }
}
```

### **`GET/cosmos/distribution/v1beta1/validators/{validator_address}/outstanding_rewards`**

**Description**

Queries rewards of a validator address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_address** | string \* required | defines the validator address to query for |

**Example JSON Output**

```javascript
{
  "rewards": {
    "rewards": [
      {
        "denom": "string",
        "amount": "string"
      }
    ]
  }
}
```

### **`GET/cosmos/distribution/v1beta1/validators/{validator_address}/slashes`**

**Description**

Queries slash events of a validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_address** | string \* required | defined the validator address to query for |
| **starting\_height** | string | The optional starting height to query the slashes |
| **ending\_height** | string | the optional ending height to query the slashes  |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "slashes": [
    {
      "validator_period": "string",
      "fraction": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/evidence/v1beta1/evidence`**

**Description**

Queries all evidence

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "evidence": [
    {
      "type_url": "string",
      "value": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```



### **`GET/cosmos/evidence/v1beta1/evidence/{evidence_hash}`**

Queries evidence based on evidence hash

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **evidence\_hash** | string \* required | the hash of the requested evidence |

**Example JSON Output**

```javascript
{
  "evidence": {
    "type_url": "string",
    "value": "string"
  }
}
```

### **`GET/cosmos/gov/v1beta1/params/{params_type}`**

Queries all parameters of the gov module

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **params\_type** | string \* required | Which parameters to query for, can be one of "voting", "tallying" or "deposit" |

**Example JSON Output**

```javascript
{
  "voting_params": {
    "voting_period": "string"
  },
  "deposit_params": {
    "min_deposit": [
      {
        "denom": "string",
        "amount": "string"
      }
    ],
    "max_deposit_period": "string"
  },
  "tally_params": {
    "quorum": "string",
    "threshold": "string",
    "veto_threshold": "string"
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals`**

**Description**

Queries all proposals based on given status

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_status** | string | the status of the proposals |
| **voter** | string | the voter address for the proposals |
| **depositor** | string | the deposit addresses from the proposals  |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "proposals": [
    {
      "proposal_id": "string",
      "content": {
        "type_url": "string",
        "value": "string"
      },
      "status": "PROPOSAL_STATUS_UNSPECIFIED",
      "final_tally_result": {
        "yes": "string",
        "abstain": "string",
        "no": "string",
        "no_with_veto": "string"
      },
      "submit_time": "2021-03-25T15:00:52.800Z",
      "deposit_end_time": "2021-03-25T15:00:52.801Z",
      "total_deposit": [
        {
          "denom": "string",
          "amount": "string"
        }
      ],
      "voting_start_time": "2021-03-25T15:00:52.801Z",
      "voting_end_time": "2021-03-25T15:00:52.801Z"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals/{proposal_id}`**

Queries proposal details based on Proposal ID

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_id** | string \* required | the unique id of the proposal |

**Example JSON Output**

```javascript
{
  "proposal": {
    "proposal_id": "string",
    "content": {
      "type_url": "string",
      "value": "string"
    },
    "status": "PROPOSAL_STATUS_UNSPECIFIED",
    "final_tally_result": {
      "yes": "string",
      "abstain": "string",
      "no": "string",
      "no_with_veto": "string"
    },
    "submit_time": "2021-03-25T15:05:00.435Z",
    "deposit_end_time": "2021-03-25T15:05:00.435Z",
    "total_deposit": [
      {
        "denom": "string",
        "amount": "string"
      }
    ],
    "voting_start_time": "2021-03-25T15:05:00.435Z",
    "voting_end_time": "2021-03-25T15:05:00.435Z"
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals/{proposal_id}/deposits`**

**Description**

Queries all deposits of a single proposal

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_id** | string \* required | the unique id of the proposal |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "deposits": [
    {
      "proposal_id": "string",
      "depositor": "string",
      "amount": [
        {
          "denom": "string",
          "amount": "string"
        }
      ]
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals/{proposal_id}/deposits/{depositor}`**

Queries single deposit information based proposalID, depositAddr

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_id** | string \* required | the unique id of the proposal |
| **depositor** | string \* required | the deposit addresses from the proposals |

**Example JSON Output**

```javascript
{
  "deposit": {
    "proposal_id": "string",
    "depositor": "string",
    "amount": [
      {
        "denom": "string",
        "amount": "string"
      }
    ]
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals/{proposal_id}/tally`**

Queries the tally of a proposal vote

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_id** | string \* required | the unique id of the proposal |

**Example JSON Output**

```javascript
{
  "tally": {
    "yes": "string",
    "abstain": "string",
    "no": "string",
    "no_with_veto": "string"
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals/{proposal_id}/deposits`**

**Description**

Queries votes of a given proposal

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_id** | string \* required | the unique id of the proposal |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "votes": [
    {
      "proposal_id": "string",
      "voter": "string",
      "option": "VOTE_OPTION_UNSPECIFIED"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/gov/v1beta1/proposals/{proposal_id}/votes/{voter}`**

Queries voted information based on proposalID, voterAddr

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **proposal\_id** | string \* required | the unique id of the proposal |
| **voter** | string \* required | The voter address for the proposals |

**Example JSON Output**

```javascript
{
  "vote": {
    "proposal_id": "string",
    "voter": "string",
    "option": "VOTE_OPTION_UNSPECIFIED"
  }
}
```

### **`GET/cosmos/mint/v1beta1/annual_provisions`**

**Description**

Current minting annual provisions value

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "annual_provisions": "string"
}
```

### **`GET/cosmos/mint/v1beta1/inflation`**

**Description**

Returns the current minting inflation value

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "inflation": "string"
}
```

### **`GET/cosmos/mint/v1beta1/params`**

**Description**

Returns the total set of minting parameters

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "params": {
    "mint_denom": "string",
    "inflation_rate_change": "string",
    "inflation_max": "string",
    "inflation_min": "string",
    "goal_bonded": "string",
    "blocks_per_year": "string"
  }
}
```

### **`GET/cosmos/params/v1beta1/params`**

Queries a specific parameter of a module, given its subspace and key 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **subspace** | string | the module to query the parameter for |
| **key** | string | the key of the parameter in the subspace  |

**Example JSON Output**

```javascript
{
  "param": {
    "subspace": "string",
    "key": "string",
    "value": "string"
  }
}
```

### **`GET/cosmos/mint/v1beta1/params`**

**Description**

Queries the parameters of the slashing module

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "params": {
    "signed_blocks_window": "string",
    "min_signed_per_window": "string",
    "downtime_jail_duration": "string",
    "slash_fraction_double_sign": "string",
    "slash_fraction_downtime": "string"
  }
}
```

### **`GET/cosmos/slashing/v1beta1/signing_infos`**

**Description**

Queries signing info of all validators

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "info": [
    {
      "address": "string",
      "start_height": "string",
      "index_offset": "string",
      "jailed_until": "2021-03-25T15:18:29.945Z",
      "tombstoned": true,
      "missed_blocks_counter": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/slashing/v1beta1/signing_infos/{cons_address}`**

Queries the signing info of a given cons address 

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **cons\_address** | string \* required | the address to query the  signing info for |

**Example JSON Output**

```javascript
{
  "val_signing_info": {
    "address": "string",
    "start_height": "string",
    "index_offset": "string",
    "jailed_until": "2021-03-25T15:19:35.216Z",
    "tombstoned": true,
    "missed_blocks_counter": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/delegations/{delegator_addr}`**

**Description**

Queries all delegations of a given delegator address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_addr** | string \* required | the delegator address to query for |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "delegation_responses": [
    {
      "delegation": {
        "delegator_address": "string",
        "validator_address": "string",
        "shares": "string"
      },
      "balance": {
        "denom": "string",
        "amount": "string"
      }
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/delegators/{delegator_addr}/redelegations`**

**Description**

Queries redelegations of a given address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_addr** | string \* required | the delegator address to query for |
| **src\_validator\_addr** | string | the validator address to redelegate from |
| **dst\_validator\_addr** | string | the validator address to redelegate to  |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "redelegation_responses": [
    {
      "redelegation": {
        "delegator_address": "string",
        "validator_src_address": "string",
        "validator_dst_address": "string",
        "entries": [
          {
            "creation_height": "string",
            "completion_time": "2021-03-25T15:22:40.747Z",
            "initial_balance": "string",
            "shares_dst": "string"
          }
        ]
      },
      "entries": [
        {
          "redelegation_entry": {
            "creation_height": "string",
            "completion_time": "2021-03-25T15:22:40.747Z",
            "initial_balance": "string",
            "shares_dst": "string"
          },
          "balance": "string"
        }
      ]
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/delegators/{delegator_addr}/unbonding_delegations`**

**Description**

Queries all unbonding delegations of a given delegator address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_addr** | string \* required | the delegator address to query for |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "unbonding_responses": [
    {
      "delegator_address": "string",
      "validator_address": "string",
      "entries": [
        {
          "creation_height": "string",
          "completion_time": "2021-03-25T15:24:19.244Z",
          "initial_balance": "string",
          "balance": "string"
        }
      ]
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/delegators/{delegator_addr}/validators`**

**Description**

Queries all validators info for given delegator address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_addr** | string \* required | the delegator address to query for |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "validators": [
    {
      "operator_address": "string",
      "consensus_pubkey": {
        "type_url": "string",
        "value": "string"
      },
      "jailed": true,
      "status": "BOND_STATUS_UNSPECIFIED",
      "tokens": "string",
      "delegator_shares": "string",
      "description": {
        "moniker": "string",
        "identity": "string",
        "website": "string",
        "security_contact": "string",
        "details": "string"
      },
      "unbonding_height": "string",
      "unbonding_time": "2021-03-25T15:25:53.172Z",
      "commission": {
        "commission_rates": {
          "rate": "string",
          "max_rate": "string",
          "max_change_rate": "string"
        },
        "update_time": "2021-03-25T15:25:53.172Z"
      },
      "min_self_delegation": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/delegators/{delegator_addr}/validators/{validator_addr}`**

Queries validator info for given delegator-validator pair

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **delegator\_addr** | string \* required | the delegator address to query for |
| **validator\_addr** | string \* required | the validator address to query for  |

**Example JSON Output**

```javascript
{
  "validator": {
    "operator_address": "string",
    "consensus_pubkey": {
      "type_url": "string",
      "value": "string"
    },
    "jailed": true,
    "status": "BOND_STATUS_UNSPECIFIED",
    "tokens": "string",
    "delegator_shares": "string",
    "description": {
      "moniker": "string",
      "identity": "string",
      "website": "string",
      "security_contact": "string",
      "details": "string"
    },
    "unbonding_height": "string",
    "unbonding_time": "2021-03-25T15:26:44.700Z",
    "commission": {
      "commission_rates": {
        "rate": "string",
        "max_rate": "string",
        "max_change_rate": "string"
      },
      "update_time": "2021-03-25T15:26:44.700Z"
    },
    "min_self_delegation": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/historical_info/{height}`**

Queries the historical info for given height

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **height** | string \* required | which height to query the historical info |

**Example JSON Output**

```javascript
{
  "hist": {
    "header": {
      "version": {
        "block": "string",
        "app": "string"
      },
      "chain_id": "string",
      "height": "string",
      "time": "2021-03-25T15:28:31.852Z",
      "last_block_id": {
        "hash": "string",
        "part_set_header": {
          "total": 0,
          "hash": "string"
        }
      },
      "last_commit_hash": "string",
      "data_hash": "string",
      "validators_hash": "string",
      "next_validators_hash": "string",
      "consensus_hash": "string",
      "app_hash": "string",
      "last_results_hash": "string",
      "evidence_hash": "string",
      "proposer_address": "string"
    },
    "valset": [
      {
        "operator_address": "string",
        "consensus_pubkey": {
          "type_url": "string",
          "value": "string"
        },
        "jailed": true,
        "status": "BOND_STATUS_UNSPECIFIED",
        "tokens": "string",
        "delegator_shares": "string",
        "description": {
          "moniker": "string",
          "identity": "string",
          "website": "string",
          "security_contact": "string",
          "details": "string"
        },
        "unbonding_height": "string",
        "unbonding_time": "2021-03-25T15:28:31.852Z",
        "commission": {
          "commission_rates": {
            "rate": "string",
            "max_rate": "string",
            "max_change_rate": "string"
          },
          "update_time": "2021-03-25T15:28:31.852Z"
        },
        "min_self_delegation": "string"
      }
    ]
  }
}
```

### **`GET/cosmos/staking/v1beta1/params`**

**Description**

Queries the parameters of the slashing module

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "params": {
    "unbonding_time": "string",
    "max_validators": 0,
    "max_entries": 0,
    "historical_entries": 0,
    "bond_denom": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/pool`**

**Description**

Queries the pool info

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "pool": {
    "not_bonded_tokens": "string",
    "bonded_tokens": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/validators`**

**Description**

Queries all validators that match the given address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **status** | string | validators matching a given status |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "validators": [
    {
      "operator_address": "string",
      "consensus_pubkey": {
        "type_url": "string",
        "value": "string"
      },
      "jailed": true,
      "status": "BOND_STATUS_UNSPECIFIED",
      "tokens": "string",
      "delegator_shares": "string",
      "description": {
        "moniker": "string",
        "identity": "string",
        "website": "string",
        "security_contact": "string",
        "details": "string"
      },
      "unbonding_height": "string",
      "unbonding_time": "2021-03-25T15:30:52.279Z",
      "commission": {
        "commission_rates": {
          "rate": "string",
          "max_rate": "string",
          "max_change_rate": "string"
        },
        "update_time": "2021-03-25T15:30:52.279Z"
      },
      "min_self_delegation": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/validators/{validator_addr}`**

Queries validator info for a given validator address

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_addr** | string \* required | the validator address to query for |

**Example JSON Output**

```javascript
{
  "validator": {
    "operator_address": "string",
    "consensus_pubkey": {
      "type_url": "string",
      "value": "string"
    },
    "jailed": true,
    "status": "BOND_STATUS_UNSPECIFIED",
    "tokens": "string",
    "delegator_shares": "string",
    "description": {
      "moniker": "string",
      "identity": "string",
      "website": "string",
      "security_contact": "string",
      "details": "string"
    },
    "unbonding_height": "string",
    "unbonding_time": "2021-03-25T15:32:16.587Z",
    "commission": {
      "commission_rates": {
        "rate": "string",
        "max_rate": "string",
        "max_change_rate": "string"
      },
      "update_time": "2021-03-25T15:32:16.587Z"
    },
    "min_self_delegation": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/validators/{validator_addr}/delegations`**

**Description**

Queries delegate info for a given validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_addr** | string \* required | the validator address to query for |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "delegation_responses": [
    {
      "delegation": {
        "delegator_address": "string",
        "validator_address": "string",
        "shares": "string"
      },
      "balance": {
        "denom": "string",
        "amount": "string"
      }
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/staking/v1beta1/validators/{validator_addr}/delegations/{delegator_addr}`**

Queries delegator info for a given delegator-validator pair

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_addr** | string \* required | the validator address to query for |
| **delegator\_addr** | string \* required | the delegator address to query for |

**Example JSON Output**

```javascript
{
  "delegation_response": {
    "delegation": {
      "delegator_address": "string",
      "validator_address": "string",
      "shares": "string"
    },
    "balance": {
      "denom": "string",
      "amount": "string"
    }
  }
}
```

### **`GET/cosmos/staking/v1beta1/validators/{validator_addr}/delegations/{delegator_addr}/unbonding_delegation`**

Queries unbonding info for given delegator-validator pair

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_addr** | string \* required | the validator address to query for |
| **delegator\_addr** | string \* required | the delegator address to query for |

**Example JSON Output**

```javascript
{
  "unbond": {
    "delegator_address": "string",
    "validator_address": "string",
    "entries": [
      {
        "creation_height": "string",
        "completion_time": "2021-03-25T15:37:00.114Z",
        "initial_balance": "string",
        "balance": "string"
      }
    ]
  }
}
```

### **`GET/cosmos/staking/v1beta1/validators/{validator_addr}/unbonding_delegations`**

**Description**

Queries unbonding delegations of a validator

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **validator\_addr** | string \* required | the validator address to query for |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "unbonding_responses": [
    {
      "delegator_address": "string",
      "validator_address": "string",
      "entries": [
        {
          "creation_height": "string",
          "completion_time": "2021-03-25T15:38:08.922Z",
          "initial_balance": "string",
          "balance": "string"
        }
      ]
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  }
}
```

### **`GET/cosmos/upgrade/v1beta1/applied_plan/{name}`**

Queries a previously applied upgrade plan by its name

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **name** | string \* required | the name of the applied plan to query for |

**Example JSON Output**

```javascript
{
  "height": "string"
}
```

### **`GET/cosmos/upgrade/v1beta1/current_plan`**

**Description**

Queries the current upgrade plan

**Parameters**  
  
No parameters

**Example JSON Output**

```javascript
{
  "plan": {
    "name": "string",
    "time": "2021-03-25T15:41:30.411Z",
    "height": "string",
    "info": "string",
    "upgraded_client_state": {
      "type_url": "string",
      "value": "string"
    }
  }
}
```

### **`GET/cosmos/upgrade/v1beta1/upgraded_consensus_state/{last_height}`**

Queries the consensus state that will serve as a trusted kernel for the next version of this chain. It will only be stored at the last height of this chain. UpgradedConsensusState RPC not supported with legacy querier

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **last\_height** | string \* required | last height of the current chain must be sent in request as this is the height under which next consensus state is stored |

**Example JSON output**

```javascript
{
  "upgraded_consensus_state": {
    "type_url": "string",
    "value": "string"
  }
}
```

### **`GET/ibc/core/channel/v1beta1/channels`**

**Description**

Queries all the IBC channels of a chain

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **pagination.key** | string | key is a value returned to begin querying the next page most efficiently. Only one of offset or key should be set. |
| **pagination.offset** | string | offset is a numeric offset that can be used when key is unavailable. It is less efficient than using key. Only one of offset or key should be set |
| **pagination.limit** | string | limit is the total number of results to be returned in  the result page. If left empty, it will default to a value to be set by each app |
| **pagination.count\_total** | boolean | count\_total is set to true to indicate that the result set should include a count of the total number of items available for pagination in UIs |

**Example JSON Output**

```javascript
{
  "channels": [
    {
      "state": "STATE_UNINITIALIZED_UNSPECIFIED",
      "ordering": "ORDER_NONE_UNSPECIFIED",
      "counterparty": {
        "port_id": "string",
        "channel_id": "string"
      },
      "connection_hops": [
        "string"
      ],
      "version": "string",
      "port_id": "string",
      "channel_id": "string"
    }
  ],
  "pagination": {
    "next_key": "string",
    "total": "string"
  },
  "height": {
    "revision_number": "string",
    "revision_height": "string"
  }
}
```

### **`GET/ibc/core/channel/v1beta1/channels/{channel_id}/ports/{port_id}`**

Queries an IBC channel

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **channel\_id** | string \* required | channel unique identifier |
| **port\_id** | string \* required | port unique identifier |

**Example JSON output**

```javascript
{
  "channel": {
    "state": "STATE_UNINITIALIZED_UNSPECIFIED",
    "ordering": "ORDER_NONE_UNSPECIFIED",
    "counterparty": {
      "port_id": "string",
      "channel_id": "string"
    },
    "connection_hops": [
      "string"
    ],
    "version": "string"
  },
  "proof": "string",
  "proof_height": {
    "revision_number": "string",
    "revision_height": "string"
  }
}
```

### **`GET/ibc/core/channel/v1beta1/channels/{channel_id}/ports/{port_id}`**

Queries an IBC channel

**Parameters**

| **Parameters** | Type | Description |
| :--- | :--- | :--- |
| **channel\_id** | string \* required | channel unique identifier |
| **port\_id** | string \* required | port unique identifier |

**Example JSON output**

```javascript
{
  "channel": {
    "state": "STATE_UNINITIALIZED_UNSPECIFIED",
    "ordering": "ORDER_NONE_UNSPECIFIED",
    "counterparty": {
      "port_id": "string",
      "channel_id": "string"
    },
    "connection_hops": [
      "string"
    ],
    "version": "string"
  },
  "proof": "string",
  "proof_height": {
    "revision_number": "string",
    "revision_height": "string"
  }
}
```

