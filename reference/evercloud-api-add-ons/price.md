---
description: Get EVER price, market cap and volume
---

# Price

Now you can get EVER price, .... from Evercloud.

Run this query:

```graphql
query{
  price{
    price{
      usd
    }
    volume{
      usd
    }
    marketCap{
      usd
    }
  }
}
```

Only USD denomination is supported now.
