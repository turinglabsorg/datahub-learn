---
description: Learn how to use the Transaction Search API on Cosmos
---

# Transaction Search

Test out our Transaction Search API today with [**DataHub**](https://datahub.figment.io/sign_up?service=cosmos)!

{% api-method method="post" host="https://cosmos--search.datahub.figment.io/transactions\_search" path=" " %}
{% api-method-summary %}
transactions\_search
{% endapi-method-summary %}

{% api-method-description %}
Transaction Search allows users to filter and query by account, transaction type, and date range. Users can also search by memo field and logs.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="network" type="string" required=true %}
Network identifier to search in. In this case, `cosmos`
{% endapi-method-parameter %}

{% api-method-parameter name="account" type="array" required=false %}
The account identifier to look for. This searches for all account IDs which exist in transaction, including senders, recipients, validators, feeders, etc.
{% endapi-method-parameter %}

{% api-method-parameter name="after\_height" type="integer" required=false %}
Gets all transactions bigger than given height. Has to be bigger than BeforeHeight
{% endapi-method-parameter %}

{% api-method-parameter name="after\_time" type="string" required=false %}
The time of transaction \(if not given by chain API, the same as block\)
{% endapi-method-parameter %}

{% api-method-parameter name="before\_height" type="integer" required=false %}
Gets all transactions lower than given height. Has to be lesser than AfterHeight
{% endapi-method-parameter %}

{% api-method-parameter name="before\_time" type="string" required=false %}
The time of transaction \(if not given by the chain API, the same as block\)
{% endapi-method-parameter %}

{% api-method-parameter name="block\_hash" type="string" required=false %}
The hash of block to get transaction from
{% endapi-method-parameter %}

{% api-method-parameter name="chain\_ids" type="array" required=false %}
ChainID to search in. In this case, `cosmoshub-3`
{% endapi-method-parameter %}

{% api-method-parameter name="epoch" type="string" required=false %}
Epoch of the transaction
{% endapi-method-parameter %}

{% api-method-parameter name="hash" type="string" required=false %}
The hash of the transaction
{% endapi-method-parameter %}

{% api-method-parameter name="height" type="integer" required=false %}
Height of the transactions to get
{% endapi-method-parameter %}

{% api-method-parameter name="limit" type="integer" required=false %}
Limit how many requests to get in one requests
{% endapi-method-parameter %}

{% api-method-parameter name="memo" type="string" required=false %}
Sets full text search for memo field
{% endapi-method-parameter %}

{% api-method-parameter name="offset" type="integer" required=false %}
Offset the next X number of records
{% endapi-method-parameter %}

{% api-method-parameter name="receiver" type="array" required=false %}
Looks for transactions that include given account IDs
{% endapi-method-parameter %}

{% api-method-parameter name="sender" type="array" required=false %}
Looks for transactions that include given account IDs
{% endapi-method-parameter %}

{% api-method-parameter name="type" type="array" required=false %}
The list of types of transactions \(see below for full list of parameters\)
{% endapi-method-parameter %}

{% api-method-parameter name="with\_raw" type="boolean" required=false %}
Include base64 raw request in search response
{% endapi-method-parameter %}

{% api-method-parameter name="with\_raw\_log" type="boolean" required=false %}
Include base64 raw log from search response. Defaults to `false`
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Success response
{% endapi-method-response-example-description %}

```javascript
{
  "block_hash": "string",
  "chain_id": "string",
  "created_at": "2020-10-15T20:38:09.017Z",
  "epoch": "string",
  "events": [
    {
      "id": "string",
      "kind": "string",
      "sub": [
        {
          "action": "string",
          "additional": {
            "additionalProp1": [
              "string"
            ],
            "additionalProp2": [
              "string"
            ],
            "additionalProp3": [
              "string"
            ]
          },
          "amount": {
            "additionalProp1": {
              "currency": "string",
              "exp": 0,
              "numeric": {},
              "text": "string"
            },
            "additionalProp2": {
              "currency": "string",
              "exp": 0,
              "numeric": {},
              "text": "string"
            },
            "additionalProp3": {
              "currency": "string",
              "exp": 0,
              "numeric": {},
              "text": "string"
            }
          },
          "completion": "2020-10-15T20:38:09.017Z",
          "error": {
            "message": "string"
          },
          "module": "string",
          "node": {
            "additionalProp1": [
              {
                "detail": {
                  "contact": "string",
                  "description": "string",
                  "name": "string",
                  "website": "string"
                },
                "id": "string"
              }
            ],
            "additionalProp2": [
              {
                "detail": {
                  "contact": "string",
                  "description": "string",
                  "name": "string",
                  "website": "string"
                },
                "id": "string"
              }
            ],
            "additionalProp3": [
              {
                "detail": {
                  "contact": "string",
                  "description": "string",
                  "name": "string",
                  "website": "string"
                },
                "id": "string"
              }
            ]
          },
          "nonce": "string",
          "recipient": [
            {
              "account": {
                "detail": {
                  "contact": "string",
                  "description": "string",
                  "name": "string",
                  "website": "string"
                },
                "id": "string"
              },
              "amounts": [
                {
                  "currency": "string",
                  "exp": 0,
                  "numeric": {},
                  "text": "string"
                }
              ]
            }
          ],
          "sender": [
            {
              "account": {
                "detail": {
                  "contact": "string",
                  "description": "string",
                  "name": "string",
                  "website": "string"
                },
                "id": "string"
              },
              "amounts": [
                {
                  "currency": "string",
                  "exp": 0,
                  "numeric": {},
                  "text": "string"
                }
              ]
            }
          ],
          "type": [
            "string"
          ]
        }
      ]
    }
  ],
  "gas_used": 0,
  "gas_wanted": 0,
  "hash": "string",
  "height": 0,
  "id": [
    0
  ],
  "memo": "string",
  "raw": [
    0
  ],
  "raw_log": [
    0
  ],
  "time": "2020-10-15T20:38:09.017Z",
  "transaction_fee": [
    {
      "currency": "string",
      "exp": 0,
      "numeric": {},
      "text": "string"
    }
  ],
  "updated_at": "2020-10-15T20:38:09.017Z",
  "version": "string",
  "has_errors": false,
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Bad parameters sent
{% endapi-method-response-example-description %}

```javascript
{
  "error": "Bad parameters sent"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=406 %}
{% api-method-response-example-description %}
Not acceptable content type
{% endapi-method-response-example-description %}

```javascript
{
  "error": "Not acceptable content type"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=500 %}
{% api-method-response-example-description %}
Internal/Other server error while processing request
{% endapi-method-response-example-description %}

```javascript
{
  "error": "Something bad happened" 
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## **Transaction Types**

List of currently supporter transaction types in cosmos-worker are \(listed by modules\):

| **Module** | Type |
| :--- | :--- |
| **bank** | `multisend` , `send` |
| **crisis** | `verify_invariant` |
| **distribution** | `withdraw_validator_commission`, `set_withdraw_address`, `withdraw_delegator_reward`, `fund_community_pool` |
| **evidence** | `submit_evidence` |
| **gov** | `deposit`, `vote`, `submit_proposal` |
| **slashing** | `unjail` |
| **staking** | `begin_unbonding`, `edit_validator`, `create_validator` , `delegate`, `begin_redelegate` |
| **internal** | `error` |

## Example Request

```javascript
{
    "network": "cosmos",
    "limit": 1
}
```

## Example Response

```javascript
[
    {
        "id": "45af02cd-bf42-414b-8c5d-75d42b49cd39",
        "hash": "BE62B6C0AB33B6CD251CCF6C76E433A813D858265F94506EFC57938B683030D3",
        "block_hash": "12038986D3F65E1508DF7D86E6290284B973BCF24A7F802C8BFF19554C0F8F9F",
        "height": 92,
        "chain_id": "cosmoshub-3",
        "time": "2019-12-11T16:43:51.699557Z",
        "gas_wanted": 200000,
        "gas_used": 93105,
        "memo": "P2P Dashboard",
        "version": "0.0.1",
        "has_errors": false,
        "events": [
            {
                "id": "0",
                "kind": "delegate",
                "sub": [
                    {
                        "type": [
                            "delegate"
                        ],
                        "module": "staking",
                        "node": {
                            "delegator": [
                                {
                                    "id": "cosmos1r0cpzr5aj4lpt2aefnmz7arz4sq5p6htgw2sfv"
                                }
                            ],
                            "validator": [
                                {
                                    "id": "cosmosvaloper132juzk0gdmwuxvx4phug7m3ymyatxlh9734g4w"
                                }
                            ]
                        },
                        "amount": {
                            "delegate": {
                                "text": "1uatom",
                                "currency": "uatom",
                                "numeric": 1
                            }
                        }
                    }
                ]
            }
        ]
    }
]
```

{% api-method method="post" host="https://cosmos--search.datahub.figment.io/rewards" path=" " %}
{% api-method-summary %}
rewards
{% endapi-method-summary %}

{% api-method-description %}
Rewards enpoint allows users to query daily reward summaries for an account.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="network" type="string" required=true %}
Network identifier \(eg. `kava`\)
{% endapi-method-parameter %}

{% api-method-parameter name="chain_id" type="string" required=true %}
The chain id \(eg. `kava-4`\)
{% endapi-method-parameter %}

{% api-method-parameter name="account" type="string" required=true %}
The account identifier
{% endapi-method-parameter %}


{% api-method-parameter name="start\_time" type="string" required=true %}
The start time in UTC. Daily reward summaries will be calculated in 24 hour periods from the start time. 
{% endapi-method-parameter %}

{% api-method-parameter name="end\_time" type="string" required=true %}
The end time in UTC.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Success response
{% endapi-method-response-example-description %}

```javascript
[
    {
        "start": 865999,
        "end": 867288,
        "time": "2021-01-14T02:00:00.000000Z",
        "rewards": [
            {
                "text": "1.023320944447133110740ukava",
                "currency": "ukava",
                "numeric": 1023320944447133110740,
                "exp": 18
            }
        ]
    }
]
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Bad parameters sent
{% endapi-method-response-example-description %}

```javascript
{
  "error": "Bad parameters sent"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=500 %}
{% api-method-response-example-description %}
Internal/Other server error while processing request
{% endapi-method-response-example-description %}

```javascript
{
  "error": "Something bad happened" 
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}
## Example Request

```http
GET /rewards?network=terra&chain_id=columbus-4&account=terra1h77gzryshx02v3vlmky32ug0ewx4m5jsdrly69&start_time=2021-01-14T02:00:00.00Z&end_time=2021-01-15T02:00:00.00Z
```

## Example Response

```javascript
[
    {
        "start": 1379034,
        "end": 1392599,
        "time": "2021-01-14T02:00:00Z",
        "amount": [
            {
                "currency": "usdr",
                "numeric": 682878680488644,
                "exp": 18
            },
            {
                "currency": "uusd",
                "numeric": 89696324609312670,
                "exp": 18
            },
            {
                "currency": "ueur",
                "numeric": 180101860858,
                "exp": 18
            },
            {
                "currency": "ukrw",
                "numeric": 703279082363615000000,
                "exp": 18
            },
            {
                "currency": "uluna",
                "numeric": 12863706275492450,
                "exp": 18
            },
            {
                "currency": "umnt",
                "numeric": 3016829565251636000,
                "exp": 18
            }
        ]
    },
    {
        "start": 1392599,
        "end": 1406111,
        "time": "2021-01-15T02:00:00Z",
        "amount": [
            {
                "currency": "ueur",
                "numeric": 0,
                "exp": 18
            },
            {
                "currency": "ukrw",
                "numeric": 736678361065665700000,
                "exp": 18
            },
            {
                "currency": "uluna",
                "numeric": 12706271331465964,
                "exp": 18
            },
            {
                "currency": "umnt",
                "numeric": 2589822569620091400,
                "exp": 18
            },
            {
                "currency": "usdr",
                "numeric": 381613126162623,
                "exp": 18
            },
            {
                "currency": "uusd",
                "numeric": 77430735325205840,
                "exp": 18
            }
        ]
    }
]
```

If you need help with this API or simply want to share with other builders, you can [**join our community today**](https://discord.gg/fszyM7K)!

