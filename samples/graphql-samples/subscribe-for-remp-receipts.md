---
description: >-
  After sending an external inbound message you can subscribe for its processing
  statuses
---

# Subscribe for REMP receipts

This feature may be good if you want to make your application more responsive and user-friendly by providing detailed information about message processing stages to the user.&#x20;

Use subscription `rempReceipts` to receive message processing statuses.

Subscription returns an error message and closes if the first receipt is not received within 5 seconds, and if any subsequent receipt is not received within 60 seconds after the previous receipt.

You can subscribe for receipts directly from API or you can use ever-sdk `process_message` function to receive these statuses into the callback. [See the sample. ](https://github.com/tonlabs/sdk-samples/blob/master/core-examples/node-js/remp/index.js)

{% tabs %}
{% tab title="gql playground" %}
```
subscription{
  rempReceipts(messageId: "082a5c2ab5b68b0ef9b8ced4fa865933ab19603f5171ec1190f3f45943214de0"){
    messageId
    timestamp
    json
    kind
  }
}
```
{% endtab %}

{% tab title="wscat" %}
```
wscat -c wss://mainnet.evercloud.dev/your-project-id/graphql -s graphql-ws
{"id":"1","type":"start","payload":{"variables":{},"extensions":{},"operationName":null,"query":"subscription{rempReceipts(messageId:\"082a5c2ab5b68b0ef9b8ced4fa865933ab19603f5171ec1190f3f45943214de0\"){messageId,timestamp,json,kind}}"}}
```
{% endtab %}
{% endtabs %}

### Result message payload example

```json
{
  "data": {
    "rempReceipts": {
      "messageId": "082a5c2ab5b68b0ef9b8ced4fa865933ab19603f5171ec1190f3f45943214de0",
      "timestamp": 0,
      "json": "{\"message_id\":\"082a5c2ab5b68b0ef9b8ced4fa865933ab19603f5171ec1190f3f45943214de0\",\"timestamp\":0,\"source_id\":\"a0573b3f9ed4e78781250a8a6955e930ffbde24e35d50b85dd2cf50f1d6ef30e\",\"signature\":\"\",\"kind\":\"PutIntoQueue\"}",
      "kind": "Other"
    }
  }
}

```

### Error payload

If no receipt was received within a timeout (5 seconds for the first receipt, and 60 seconds for next receipts), there will be a ws message with `error` type and this payload:

```json
{
  "error": {
    "name": "Error",
    "message": "Timeout"
  }
}
```
