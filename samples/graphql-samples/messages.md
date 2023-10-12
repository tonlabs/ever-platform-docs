# Messages

{% hint style="info" %}
**Note**: With [graphql-api-1.0-migration.md](../../reference/breaking-changes/migration-guides/graphql-api-1.0-migration.md "mention") by default Blockchain API provides blocks, transactions and messages data only for the past 7 days. For use cases where earlier data is needed make sure to use the `archive: true` flag in `blockchain` query filters.
{% endhint %}

## Get message info by hash

```graphql
query{
  blockchain{
    message(hash:"f19c40cc408a453c76417fcae8afc48407abf31610b295f4bb1039cb4d13a7f4"){
      id
      hash
      value
      src
      dst
      # check other available fields in the schema in playground
    }
  }
}
```

Result

```graphql
{
  "data": {
    "blockchain": {
      "message": {
        "id": "message/f19c40cc408a453c76417fcae8afc48407abf31610b295f4bb1039cb4d13a7f4",
        "hash": "f19c40cc408a453c76417fcae8afc48407abf31610b295f4bb1039cb4d13a7f4",
        "value": null,
        "src": "",
        "dst": "-1:8888888888888888888888888888888888888888888888888888888888888888"
      }
    }
  }
}
```
