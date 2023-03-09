---
description: Use this API to calculate the price of your query
---

# Query cost

### About query cost calculation

We have implemented static query cost analyses, based on GraphQL schema types and fields.

Total query cost is calculated from summarizing all resolver costs plus all fields costs from those resolver result set. If the field is of an array type, the total cost of the field is multiplied by numerator - limit, after or before parameters of the query. If none is specified, then default value = 50 is used.

Almost all scalar types (i.e. Int, String, bool) have the same cost, but there are exceptions for big strings, as account.boc, block.boc, etc.

You can observe gql schema for types and fields costs:

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>



### Calculate query cost

Type any query in the playground, and add `cost` api call above, so that it will show you the cost of your query. Use `tryRun` parameter to see the cost, without query execution.

```graphql

query{
  cost(dryRun:true){
    complexity
    explain
  }
  ft{
    token(address:"0:597081d5dfaf8f9a3bffbc354d5bab4bb200be9b675000b06a631a3301d6ae97"){
      address
      symbol
      name
      decimals
      rootOwner
      totalSupply
      transfers{
        edges{
          node{
            value
            transferType
            timestamp
          }
        }
      }
    }
  }
}
```

Result:

```graphql
{
  "data": {
    "cost": {
      "complexity": 311,
      "explain": [
        "Cost (0)",
        "      complexity (0)",
        "      explain -50x- (0)",
        "FungibleTokenQuery (2)",
        "      FungibleToken (2)",
        "            address (1)",
        "            symbol (1)",
        "            name (1)",
        "            decimals (1)",
        "            rootOwner (1)",
        "            totalSupply (1)",
        "            FungibleTokenTransferConnection (1)",
        "                  FungibleTokenTransferEdge -50x- (1)",
        "                        FungibleTokenTransfer (2)",
        "                              value (1)",
        "                              transferType (1)",
        "                              timestamp (1)"
      ]
    }
  }
}
```
