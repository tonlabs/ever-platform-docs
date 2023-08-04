# Message Processing and Monitoring

## Introduction

The [**Message Monitor**](../../reference/graphql-api/message-monitor-api.md) **GraphQL API extension** was developed to solve the problem of batch external message processing. Typically, to process large amounts of messages (which includes sending them, receiving their status, and retrying in case of failure), one would need to create a separate message sending request for each message and perform multiple queries to check the status of each message.

The `Message Monitor API` optimizes the process by taking advantage of the fact that all users walk along the same shards. Even when a client works with just one contract and performs many external requests, it has to perform block walking along the same blocks for each message. `Message Monitor` caches the transactions of the last block to provide all users with shard block walking on the backend side.

Also it ensures a transaction error trace is received on the backend if the transaction was not executed onchain within the specified timeout.

`Message Monitor` GraphQL API extension can be used in 2 ways:

* subscribe for the statuses of a batch of messages via websocket
* query the status of a batch of messages with 1 query

## GraphQL Query

Let’s look at the following query:

```graphql
query {
  recentExtInMessageStatuses(
    messages: [
      {
        address: "0:435fcf8a845e46a4c8184adbc9eb0fcede6667022de7ed2470a2f28846171e1c"
        hash: "ce9114726ca73b43601b8ac061e48cf20cc4d88242af91fe08991f0ddfd17c91"
        waitUntil: 1690033160
      }
      {
        address: "0:435fcf8a845e46a4c8184adbc9eb0fcede6667022de7ed2470a2f28846171e1c"
        hash: "d940b81faa2c89d072f459966faf82d33320cb5fb5f1a9fe83cd29d89b17eb68"
        waitUntil: 1690033160
      }
    ]
  ) {
    hash
    status
    transaction {
      hash
      aborted
      compute {
        exit_code
      }
    }
    error
  }
}
```

Here two external inbound messages’ status is requested with a single query.

The query returns the following result:

```graphql
{
  "data": {
    "recentExtInMessageStatuses": [
      {
        "hash": "ce9114726ca73b43601b8ac061e48cf20cc4d88242af91fe08991f0ddfd17c91",
        "status": "FINALIZED",
        "transaction": {
          "hash": "70960d6b04938454432389f66a302b9de2d425a0f2e306d366ef38160e058f8d",
          "aborted": false,
          "compute": {
            "exit_code": 0
          }
        },
        "error": null
      },
      {
        "hash": "d940b81faa2c89d072f459966faf82d33320cb5fb5f1a9fe83cd29d89b17eb68",
        "status": "TIMEOUT",
        "transaction": null,
        "error": "Can not resolve error because message BOC is not provided"
      }
    ]
  }
}
```

One message was successfully finalized. The other encountered some error that cannot be resolved without retrieving message `boc`.

Let’s modify the query to find out why the second message failed. We will do this by requesting message status through `boc`:

```graphql
query {
  recentExtInMessageStatuses(
    messages: [
      {
        address: "0:435fcf8a845e46a4c8184adbc9eb0fcede6667022de7ed2470a2f28846171e1c"
        boc: "te6ccgEBAQEAdQAA5YgAhr+fFQi8jUmQMJW3k9YfnbzMzgRbz9pI4UXlEIwuPDgHR2igIm68kFMUvnKay2UwpYFWHYmG4Vag1hO5G691OghbuF2bNVWeI4ECfNZTs6GDiNvCK3iZ1EfQT4dAoSMoIAAABibrarYBky3vqNzEuRY="
        waitUntil: 1690033160
      }
    ]
  ) {
    hash
    status
    transaction {
      hash
      aborted
      compute {
        exit_code
      }
    }
    error
  }
}GraphQL
```

## GraphQL Subscription

You can also create a websocket subscription to message statuses:

{% tabs %}
{% tab title="wscat" %}
```
wscat -c your_wss_endpoint -s graphql-ws \\
'{"id":"1","type":"start","payload":{"variables":{},"extensions":{},"operationName":null,"query":"subscription{\\n  recentExtInMessageStatuses(messages:{\\n    address: \\"0:435fcf8a845e46a4c8184adbc9eb0fcede6667022de7ed2470a2f28846171e1c\\", hash: \\"ce9114726ca73b43601b8ac061e48cf20cc4d88242af91fe08991f0ddfd17c91\\", waitUntil: 1690033160 }\\n){\\n    hash\\n    status\\n  }\\n}"}}'
```
{% endtab %}

{% tab title="Postman" %}
```
URL: wss://mainnet.evercloud.dev/your-project-id/graphql 
Sec-WebSocket-Protocol: graphql-ws

message
{"id":"1","type":"start","payload":{"variables":{},"extensions":{},"operationName":null,"query":"subscription{ recentExtInMessageStatuses(messages:{ address: \\"0:435fcf8a845e46a4c8184adbc9eb0fcede6667022de7ed2470a2f28846171e1c\\", hash: \\"ce9114726ca73b43601b8ac061e48cf20cc4d88242af91fe08991f0ddfd17c91\\", waitUntil: 1690033160 }) { hash status } }"}}
```
{% endtab %}
{% endtabs %}

Ever SDK supports additional capabilities that allow to process messages using a special queue. Explore the Ever SDK Message monitor object [here](https://docs.everos.dev/ever-sdk/guides/work\_with\_contracts/monitor-messages).
