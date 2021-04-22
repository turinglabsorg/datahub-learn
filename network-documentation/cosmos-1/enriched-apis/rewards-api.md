---
description: Learn how to use the Rewards API on Cosmos
---

# Rewards API

Test out our Rewards API today with [**DataHub**](https://datahub.figment.io/sign_up?service=cosmos)!

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
Network identifier \(eg. `cosmos`\)
{% endapi-method-parameter %}

{% api-method-parameter name="chain\_id" type="string" required=true %}
The chain id \(eg. `cosmoshub-3`\)
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
        "start": 3997443,
        "end": 4009234,
        "time": "2020-11-08T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 6.547710174181991e+24,
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
GET /rewards?network=cosmos&start_time=2020-11-08T02:00:00.00Z&chain_id=cosmoshub-3&end_time=2020-11-15T02:00:00.00Z&account=cosmos13axkauxjxulmvjyskppf8kxec56fyl96njm8qq
```

## Example Response

```javascript
[
    {
        "start": 3997443,
        "end": 4009234,
        "time": "2020-11-08T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 6.547710174181991e+24,
                "exp": 18
            }
        ]
    },
    {
        "start": 4009234,
        "end": 4020829,
        "time": "2020-11-09T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 6.429013482613538e+24,
                "exp": 18
            }
        ]
    },
    {
        "start": 4020829,
        "end": 4032432,
        "time": "2020-11-10T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 7.761534501887029e+24,
                "exp": 18
            }
        ]
    },
    {
        "start": 4032432,
        "end": 4044041,
        "time": "2020-11-11T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 9.177947760918705e+24,
                "exp": 18
            }
        ]
    },
    {
        "start": 4044041,
        "end": 4055673,
        "time": "2020-11-12T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 9.184057713294925e+24,
                "exp": 18
            }
        ]
    },
    {
        "start": 4055673,
        "end": 4067447,
        "time": "2020-11-13T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 9.299403317950247e+24,
                "exp": 18
            }
        ]
    },
    {
        "start": 4067447,
        "end": 4079206,
        "time": "2020-11-14T02:00:00Z",
        "amount": [
            {
                "currency": "uatom",
                "numeric": 9.271378498654964e+24,
                "exp": 18
            }
        ]
    }
]
```

If you need help with this API or simply want to share with other builders, you can [**join our community today**](https://discord.gg/fszyM7K)!

