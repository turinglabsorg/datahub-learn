---
description: Learn how to use the Rewards API on Terra
---

# Rewards Api

Test out our Rewards API today with [**DataHub**](https://datahub.figment.io/sign_up?service=terra)!


{% api-method method="post" host="https://terra--search.datahub.figment.io/rewards" path=" " %}
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
Network identifier \(eg. `terra`\)
{% endapi-method-parameter %}

{% api-method-parameter name="chain_id" type="string" required=true %}
The chain id \(eg. `columbus-4`\)
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

```json
[
    {
        "start": 1379034,
        "end": 1392599,
        "time": "2021-01-14T02:00:00Z",
        "amount": [
            {
                "text": "0.01286370627549245uluna",
                "currency": "uluna",
                "numeric": 12863706275492450,
                "exp": 18
            },
            {
                "text": "3.016829565251636umnt",
                "currency": "umnt",
                "numeric": 3016829565251636000,
                "exp": 18
            },
        ]
    },
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
GET /rewards?network=terra&chain_id=columbus-4&account=terra1h77gzryshx02v3vlmky32ug0ewx4m5jsdrly69&start_time=2021-01-14T02:00:00.00Z&end_time=2021-01-16T02:00:00.00Z
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
                "currency": "uluna",
                "numeric": 12863706275492450,
                "exp": 18
            },
            {
                "currency": "umnt",
                "numeric": 3016829565251636000,
                "exp": 18
            },
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

