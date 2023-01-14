---
description: Subscribe to blockchain events
---

# Subscribe Collections

You can create a websocket subscription for any event in the blockchain.&#x20;

Read filter syntax and collections documentation [in the previous section](query\_language.md).&#x20;

In this example, we start a subscription and get a result whenever a new block is generated.

```graphql
subscription{
  blocks{
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
const {TonClient} = require("@eversdk/core");
const {libNode} = require("@eversdk/lib-node");

TonClient.useBinaryLibrary(libNode)

const client = new TonClient({
    network: {
        endpoints: [
            "your-endpoint"
        ],
        queries_protocol: WS
    },
});

(async () => {
    try {
        queryString = `
            query{
                blockchain{
                blocks(workchain:-1, last:1){
                    edges{
                    node{
                        hash
                        seq_no
                    }
                    }
                }
                }
            }
        `
        let {seq_no, hash} = (await client.net.query({ 
            "query": queryString }))
        .result.data.blockchain.blocks.edges[0].node;
        console.log("The last masterchain block seqNo is " + seq_no+ '\n' + "the hash is" + hash);
        client.close();
}
    catch (error) {
            console.error(error);
    }
}
)()
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



