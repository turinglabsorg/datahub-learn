---
description: Learn how to interact with Figment's Miner Reputation System API
---

# Miner Reputation System API

## Overview

The Miner Reputation System API provides users with all of the necessary information for assessing the reputation of storage miners on the Filecoin network.

By tracking storage capacity, sector faults, deals, and slashes of every storage miner, the API calculates a reputation score, which can be used by network participants to compare and choose a reliable miner.

Additionally, the API allows users to look up account details \(such as balance\) or retrieve a list of transactions for an account. It also keeps a history of miner-related events, such as storage capacity changes, sector faults, and deal slashes.

Check out the API documentation below. 

{% api-method method="get" host="" path="/miners" %}
{% api-method-summary %}
Get miners
{% endapi-method-summary %}

{% api-method-description %}
Lists all storage miners for a given epoch. If no epoch is given, the data is returned for the most recent epoch.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
[
  {
    "address": "f01002",
    "sector_size": 34359738368,
    "raw_byte_power": 26388279066624,
    "quality_adj_power": 263882790666240,
    "relative_power": 0.33333334,
    "deals_count": 768,
    "slashed_deals_count": 0,
    "faults_count": 0,
    "score": 543
  },
  {
    "address": "f01000",
    "sector_size": 34359738368,
    "raw_byte_power": 26388279066624,
    "quality_adj_power": 263882790666240,
    "relative_power": 0.33333334,
    "deals_count": 768,
    "slashed_deals_count": 0,
    "faults_count": 0,
    "score": 543
  },
  {
    "address": "f01001",
    "sector_size": 34359738368,
    "raw_byte_power": 26388279066624,
    "quality_adj_power": 263882790666240,
    "relative_power": 0.33333334,
    "deals_count": 768,
    "slashed_deals_count": 0,
    "faults_count": 0,
    "score": 543
  }
]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/miners/:address" %}
{% api-method-summary %}
Get miner
{% endapi-method-summary %}

{% api-method-description %}
Returns storage miner details for a given epoch. If no epoch is given, the data is returned for the most recent epoch.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
The ID address of a storage miner
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
  "address": "f01000",
  "sector_size": 34359738368,
  "raw_byte_power": 26388279066624,
  "quality_adj_power": 263882790666240,
  "relative_power": 0.33333334,
  "deals_count": 768,
  "slashed_deals_count": 0,
  "faults_count": 0,
  "score": 543
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/miners/:address/events" %}
{% api-method-summary %}
Get miner events
{% endapi-method-summary %}

{% api-method-description %}
Lists network events related to a storage miner for a given epoch and kind. If no epoch is given, the data is returned for all epochs. If no kind is given, the data is returned for all event kinds.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
The ID address of a storage miner
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}

{% api-method-parameter name="kind" type="string" required=false %}
The event kind
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
[
  {
    "height": 0,
    "miner_address": "f01000",
    "kind": "new_deal",
    "data": {
      "client_address": "f0100",
      "deal_id": "656",
      "is_verified": true,
      "piece_size": "34359738368",
      "storage_price": "0"
    }
  }
]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/top\_miners" %}
{% api-method-summary %}
Get top miners
{% endapi-method-summary %}

{% api-method-description %}
Lists top 100 storage miners for a given epoch based on their reputation score. If no epoch is given, the data is returned for the most recent epoch.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
[
  {
    "address": "f01002",
    "sector_size": 34359738368,
    "raw_byte_power": 26388279066624,
    "quality_adj_power": 263882790666240,
    "relative_power": 0.33333334,
    "deals_count": 768,
    "slashed_deals_count": 0,
    "faults_count": 0,
    "score": 300
  },
  {
    "address": "f01000",
    "sector_size": 34359738368,
    "raw_byte_power": 26388279066624,
    "quality_adj_power": 263882790666240,
    "relative_power": 0.33333334,
    "deals_count": 768,
    "slashed_deals_count": 0,
    "faults_count": 0,
    "score": 200
  },
  {
    "address": "f01001",
    "sector_size": 34359738368,
    "raw_byte_power": 26388279066624,
    "quality_adj_power": 263882790666240,
    "relative_power": 0.33333334,
    "deals_count": 768,
    "slashed_deals_count": 0,
    "faults_count": 0,
    "score": 100
  }
]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/transactions" %}
{% api-method-summary %}
Get transactions
{% endapi-method-summary %}

{% api-method-description %}
Lists all transactions for a given epoch. If no epoch is given, the data is returned for all epochs.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
[
  {
    "cid": "bafy2bzaceaoo4msi45t3pbhfov3guu5l34ektpjhuftyddy2rvhf2o5ajijle",
    "height": 1,
    "from": "f3xf54js52xz74sw55pebpfr3q5uiogntgwmk5iq7jpvh4a66kb6loswq6filtmuk2b72wk7mhfpmms4swha6q",
    "to": "f01001",
    "value": "0",
    "method": "ChangePeerID"
  },
  {
    "cid": "bafy2bzaceb2cujbpoijbkyov7yb2lmzesacq24d7mtled7yybwkmrla5db354",
    "height": 1,
    "from": "f3xf54js52xz74sw55pebpfr3q5uiogntgwmk5iq7jpvh4a66kb6loswq6filtmuk2b72wk7mhfpmms4swha6q",
    "to": "f01001",
    "value": "0",
    "method": "ChangePeerID"
  },
  {
    "cid": "bafy2bzacebmapwdgjsod5ytgbcrsqumt77pynzt44l43homumj6l5h7yrhu7u",
    "height": 1,
    "from": "f3qfjszg5oe6ouqexl5uzrev7ht4b5q6fyead25hvt76rbppsaabjn6fwzqeuqtkk5f6hrsoray76oind2yaea",
    "to": "f01002",
    "value": "0",
    "method": "ChangePeerID"
  },
  {
    "cid": "bafy2bzacebghgexoolgk3rn4h4v3qteodnjkycc4i2ksce6hx7ekfcrc57a36",
    "height": 1,
    "from": "f3qfjszg5oe6ouqexl5uzrev7ht4b5q6fyead25hvt76rbppsaabjn6fwzqeuqtkk5f6hrsoray76oind2yaea",
    "to": "f01002",
    "value": "0",
    "method": "ChangePeerID"
  },
  {
    "cid": "bafy2bzaceb5sbhzn6i7bltslktujctr2rcd5f2nby6ernapn6ml74xmv3fnga",
    "height": 1,
    "from": "f3vfs6f7tagrcpnwv65wq3leznbajqyg77bmijrpvoyjv3zjyi3urq25vigfbs3ob6ug5xdihajumtgsxnz2pa",
    "to": "f01000",
    "value": "0",
    "method": "ChangePeerID"
  }
]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/accounts/:address" %}
{% api-method-summary %}
Get account
{% endapi-method-summary %}

{% api-method-description %}
Returns account details for a given address. The address could be either an ID address or an account key address.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
The address of an account \(ID or public key\)
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
  "id": "f0100",
  "public_key": "f3vfs6f7tagrcpnwv65wq3leznbajqyg77bmijrpvoyjv3zjyi3urq25vigfbs3ob6ug5xdihajumtgsxnz2pa",
  "balance": "0.482788569758516947",
  "nonce": 43,
  "transactions_sent": 1,
  "transactions_received": 0
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/accounts/:address/transactions" %}
{% api-method-summary %}
Get account transactions
{% endapi-method-summary %}

{% api-method-description %}
Lists sent and received transactions for a given account. The address of the account could be either an ID address or an account key address. If no epoch is given, the data is returned for all epochs.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
The address of an account \(ID or public key\)
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
[
  {
    "cid": "bafy2bzaceb5sbhzn6i7bltslktujctr2rcd5f2nby6ernapn6ml74xmv3fnga",
    "height": 1,
    "from": "f3vfs6f7tagrcpnwv65wq3leznbajqyg77bmijrpvoyjv3zjyi3urq25vigfbs3ob6ug5xdihajumtgsxnz2pa",
    "to": "f01000",
    "value": "0",
    "method": "ChangePeerID"
  }
]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="" path="/events" %}
{% api-method-summary %}
Get events
{% endapi-method-summary %}

{% api-method-description %}
Lists all network events for a given epoch and kind. If no epoch is given, the data is returned for all epochs. If no kind is given, the data is returned for all event kinds.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="height" type="number" required=false %}
The epoch number
{% endapi-method-parameter %}

{% api-method-parameter name="kind" type="string" required=false %}
The event kind
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
[
  {
    "height": 0,
    "miner_address": "f01000",
    "kind": "new_deal",
    "data": {
      "client_address": "f0100",
      "deal_id": "656",
      "is_verified": true,
      "piece_size": "34359738368",
      "storage_price": "0"
    }
  },
  {
    "height": 0,
    "miner_address": "f01000",
    "kind": "new_deal",
    "data": {
      "client_address": "f0100",
      "deal_id": "693",
      "is_verified": true,
      "piece_size": "34359738368",
      "storage_price": "0"
    }
  },
  {
    "height": 0,
    "miner_address": "f01000",
    "kind": "new_deal",
    "data": {
      "client_address": "f0100",
      "deal_id": "72",
      "is_verified": true,
      "piece_size": "34359738368",
      "storage_price": "0"
    }
  }
]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

