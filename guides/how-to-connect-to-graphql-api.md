# How to connect to GraphQL API

## HTTPS

#### Without secret

{% tabs %}
{% tab title="Curl" %}
```bash
curl --location --request POST 'https://mainnet.evercloud.dev/your-project-id/graphql' \
--header 'Content-Type: application/json' \
--data-raw '{"query":"query($address: String!){\n  blockchain{\n    account(address:$address){\n      info{\n        balance(format:DEC)\n      }\n    }\n  }\n}","variables":{"address":"0:e17ac4e77f46626579c7c4fefe35286117384c5ccfc8745c9780cdf056c378bf"}}'
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
            "mainnet.evercloud.dev/your-project-id/graphql"
        ],
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

{% tab title="everdev" %}
```bash
everdev network credentials main --project "your-project-id"
```
{% endtab %}

{% tab title="tonos-cli" %}
```
tonos-cli config --url "mainnet.evercloud.dev" --project_id "your-project-id"
Succeeded.
{
  "url": "mainnet.evercloud.dev",
  "wc": 0,
  "addr": null,
  "method": null,
  "parameters": null,
  "wallet": null,
  "pubkey": null,
  "abi_path": null,
  "keys_path": null,
  "retries": 5,
  "timeout": 60000,
  "message_processing_timeout": 40000,
  "out_of_sync_threshold": 15,
  "is_json": false,
  "depool_fee": 0.5,
  "lifetime": 60,
  "no_answer": true,
  "balance_in_tons": false,
  "local_run": false,
  "async_call": false,
  "debug_fail": "None",
  "project_id": "your-project-id",
  "access_key": null,
  "endpoints": []
}
```
{% endtab %}

{% tab title="JS fetch" %}
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var graphql = JSON.stringify({
  query: "query{\n  blockchain{\n    blocks(workchain:-1, last:1){\n      edges{\n        node{\n          hash\n          seq_no\n        }\n      }\n    }\n  }\n}",
  variables: {}
})
var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: graphql,
  redirect: 'follow'
};

fetch("https://mainnet.evercloud.dev/your-project-id/graphql", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```
{% endtab %}

{% tab title="Postman" %}
```
URL: https://mainnet.evercloud.dev/your-project-id/graphql
Body: GraphQL
Query:

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
```
{% endtab %}
{% endtabs %}

#### With secret

{% tabs %}
{% tab title="Curl" %}
```bash
curl --location --request POST 'https://mainnet.evercloud.dev/your-project-id/graphql' \
--header 'Authorization: Basic project_secret_in_base64' \
--header 'Content-Type: application/json' \
--data-raw '{"query":"query{\n  blockchain{\n    blocks(workchain:-1, last:1){\n      edges{\n        node{\n          hash\n          seq_no\n        }\n      }\n    }\n  }\n}","variables":{}}'
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
            "mainnet.evercloud.dev/your-project-id/graphql"
        ],
        access_key: "Project's secret"
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

{% tab title="everdev" %}
```
everdev network credentials main --project "Project Id" --access-key "Project secret"
```
{% endtab %}

{% tab title="tonos-cli" %}
```
tonos-cli config --url "mainnet.evercloud.dev" --project_id "your-project-id id" --access_key "Project secret"
Succeeded.
{
  "url": "mainnet.evercloud.dev",
  "wc": 0,
  "addr": null,
  "method": null,
  "parameters": null,
  "wallet": null,
  "pubkey": null,
  "abi_path": null,
  "keys_path": null,
  "retries": 5,
  "timeout": 60000,
  "message_processing_timeout": 40000,
  "out_of_sync_threshold": 15,
  "is_json": false,
  "depool_fee": 0.5,
  "lifetime": 60,
  "no_answer": true,
  "balance_in_tons": false,
  "local_run": false,
  "async_call": false,
  "debug_fail": "None",
  "project_id": "your-project-id",
  "access_key": "project-secret",
  "endpoints": []
}
```
{% endtab %}

{% tab title="JS fetch" %}
```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var graphql = JSON.stringify({
  query: "query{\n  blockchain{\n    blocks(workchain:-1, last:1){\n      edges{\n        node{\n          hash\n          seq_no\n        }\n      }\n    }\n  }\n}",
  variables: {}
})
var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: graphql,
  redirect: 'follow'
};

fetch("https://mainnet.evercloud.dev/your-project-id/graphql", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```
{% endtab %}

{% tab title="Postman" %}
```
URL: https://mainnet.evercloud.dev/your-project-id/graphql
Authorization: Basic Auth
Username: empty
Password: <Project secret>
Body: GraphQL
Query:

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
```
{% endtab %}
{% endtabs %}

## WSS

#### Without secret

{% tabs %}
{% tab title="wscat" %}
```bash
wscat -c wss://mainnet.evercloud.dev/your-project-id/graphql -s graphql-ws
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
            "mainnet.evercloud.dev/your-project-id/graphql"
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
```json
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



## Client libraries

{% embed url="https://docs.everos.dev/ever-sdk/guides/installation/configure_sdk" %}
