---
description: Subscribe to blockchain events
---

# Subscribe Collections

You can create a websocket subscription for any event in the blockchain.&#x20;

Read filter syntax and collections documentation [in the previous section](query\_language.md).&#x20;

In this example, we start a subscription and get a result whenever a new block is generated.

```graphql
subscription {
  blocks {
    id
  }
}
```

The `filter` and `result` parameters are the same as in the `query` method. The `filter` parameter narrows the action down to a subset of monitored items. In this case, the filter is empty: all items are included into monitoring.

{% tabs %}
{% tab title="wscat" %}
```bash
wscat -c your-wss-endpoint -s graphql-ws
{"id":"1","type":"start","payload":{"variables":{},"extensions":{},"operationName":null,"query":"subscription{\n  blocks(filter:{\n    workchain_id:{\n      eq:-1\n    }\n  }){\n    seq_no\n    id\n  }\n}"}}
```
{% endtab %}

{% tab title="ever-sdk-js" %}
```javascript
const {TonClient} = require('@eversdk/core');
const {libNode} = require('@eversdk/lib-node');

TonClient.useBinaryLibrary(libNode)

const client = new TonClient({
  network: {
    endpoints: [
      process.env.endpoint
    ],
    queries_protocol: 'WS'
  },
});


function responseHandler(data, responseType) {
  // Tip: Always wrap the logic inside responseHandler in a try-catch block
  // or you will be surprised by non-informative errors due to the context
  // in which the handler is executed
  try {
    if (responseType === 100 /* GraphQL data received */) {
      if (data?.result?.blocks) {
        console.log(data.result.blocks);
      }

    } else {
      // See full list of error codes here:
      // https://docs.everos.dev/ever-sdk/reference/types-and-methods/mod_net#neterrorcode
      console.error(data, responseType);
    }
  } catch (err) {
    console.log(err);
  }
}

(async () => {
    try {
      const subscription = /* language=graphql */ `subscription($wc: Int!) {
          blocks(filter: { workchain_id: { eq: $wc } }) {
              seq_no
              id
          }
      }`
      await client.net.subscribe({
        subscription,
        variables: {wc: -1},
      }, responseHandler)
      console.log('The last masterchain blocks')
      console.log('Press CTRL+C to interrupt it')
    } catch (error) {
      if (error.code === 504) {
        console.error('Network is inaccessible');
      } else {
        console.error(error);
      }
      process.exit(1);
    }
  }
)();
```
{% endtab %}

{% tab title="Postman" %}
```
URL: wss://mainnet.evercloud.dev/your-project-id/graphql 
Sec-WebSocket-Protocol: graphql-ws

message
{
  "id": "1",
  "type": "start",
  "payload": {
    "variables": {},
    "extensions": {},
    "operationName": null,
    "query": "subscription{\n  blocks(filter:{\n    workchain_id:{\n      eq:-1\n    }\n  }){\n    seq_no\n    id\n  }\n}"
  }
}
```
{% endtab %}
{% endtabs %}



