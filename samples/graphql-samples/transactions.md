# Transactions

{% hint style="info" %}
**Note**: With [graphql-api-1.0-migration.md](../../reference/breaking-changes/migration-guides/graphql-api-1.0-migration.md "mention") by default Blockchain API provides blocks, transactions and messages data only for the past 7 days. For use cases where earlier data is needed make sure to use the `archive: true` flag in `blockchain` query filters.
{% endhint %}

## Get transaction info by hash

```graphql
query{
  blockchain{
    transaction(hash:"b0e26c42164ec0137913fdcd754aa819323a6a4b9ef5188863b021c3801e7ae4"){
      id
      hash
      balance_delta
      aborted
      lt
      now
    }
  }
}
```

Result:

```graphql
{
  "data": {
    "blockchain": {
      "transaction": {
        "id": "transaction/b0e26c42164ec0137913fdcd754aa819323a6a4b9ef5188863b021c3801e7ae4",
        "hash": "b0e26c42164ec0137913fdcd754aa819323a6a4b9ef5188863b021c3801e7ae4",
        "balance_delta": "0x0",
        "aborted": false,
        "lt": "0x15bb39a23783",
        "now": 1645453010
      }
    }
  }
}
```

## Get transaction by inbound message hash

```graphql
query{
  transactions(filter:{
    in_msg:{
      eq:"d158f7437080f6835792ac0ef9cccbcbd2874a63e8f7f1db9dcb97edc06f410d"
    }
  })
  {
    id
    account_addr
    balance_delta(format:DEC)
    now
    now_string
  }
}


```

Result:

```graphql
{
  "data": {
    "transactions": [
      {
        "id": "6e87500efde682124e92f58769b7578fa546f22d46aa844249b51e6c8b63fce4",
        "account_addr": "0:802f5476cc99425184757c0672fe7737e94b3cbc2cee59e156dfe61c0c0c630c",
        "balance_delta": "-5110030",
        "now": 1682457905,
        "now_string": "2023-04-25 21:25:05.000"
      }
    ]
  }
}
```

## Calculate account fees for transaction

```graphql
query{
  blockchain{
    transaction(hash:"998fee062e8daad96e88ce43adb52832b2e653d9a824912bc83051060932aceb"){
      ext_in_msg_fee(format:DEC)
      storage{
        storage_fees_collected(format:DEC)
      }
      compute{
        gas_fees(format:DEC)
      }
      action{
        total_fwd_fees(format:DEC)
      }      
    }
  }
}
```

You need to sum up these values to get the total fee the account paid for the transaction

```graphql
{
  "data": {
    "blockchain": {
      "transaction": {
        "ext_in_msg_fee": "2062000",
        "storage": {
          "storage_fees_collected": "270"
        },
        "compute": {
          "gas_fees": "10741000"
        },
        "action": {
          "total_fwd_fees": null
        }
      }
    }
  }
```

## Paginate blockchain transactions

Paginate workchain transactions within masterchain seqNo range, sorted by blockchain logical time.

Look at this sample. See the parameters documentation below.

```graphql
query{
  blockchain{
      transactions(
        master_seq_no_range: {
          start: 2660661
          end: 2670661
      }
          workchain:0
      ){
        edges{
          node{
            id
            now
          }
          cursor
        }
        pageInfo{
          startCursor
          hasNextPage
        }
      }
  }
}
```

Result:

```graphql
{
  "data": {
    "blockchain": {
      "transactions": {
        "edges": [
          {
            "node": {
              "id": "transaction/d5ec03df890727a90eb80960560664ca49d9842e0a9f6f3c188ad1a49fca7264",
              "now": 1647429363
            },
            "cursor": "528cc96m05"
          },
          {
            "node": {
              "id": "transaction/39c2c3174aa414003bc55e68b6a08710cbcf6ce1b71dec1a0c8962fdcef5fc76",
              "now": 1647429363
            },
            "cursor": "528cc96m06"
          },
          {
            "node": {
              "id": "transaction/fa674f4c537aeaac951ebe4fd9eb2a1d094aae26d611f126652bee92827f3ed2",
              "now": 1647429363
            },
            "cursor": "528cc96m07"
          }
        ],
        "pageInfo": {
          "startCursor": "528cc96m05",
          "hasNextPage": true
        }
      }
    }
  }
}
```

### Filter parameters

You can  filter by&#x20;

* `master_seq_no_range`: BlockchainMasterSeqNoFilter - Everscale is sharded blockchain, you need paginate within masterchain seqNo range, where your workchain blocks were commited.
* `workchain`: Int - optional, if you want to filter by workchain. If ommited, queries all workchains data.&#x20;
* `max_balance_delta`: String
* `min_balance_delta`: String&#x20;
* `allow_latest_inconsistent_data`: Boolean   - false by default. If you need to have less latency over consistency, you can get the latest data in realtime, but may miss some data. If you need reliable reads, then specify as false.

### Pagination parameters

Use `cursor`, {`first`, `after`} or {`last`, `before`} filters for pagination.

{% hint style="success" %}
We followed GraphQL best practices and implemented Relay Cursor Connections Specification for pagination for all list types. You can read more here [https://relay.dev/graphql/connections.htm](https://relay.dev/graphql/connections.htm)
{% endhint %}

### How to paginate by time range

If you do not know the `seq_no` of masterchain blocks to create a range you can first obtain it by the time range, and then implement pagination the same way as described above.

To get the `seq_no` range by time rage do this query:

```graphql
query{
  blockchain{
    master_seq_no_range(time_start: 1647421084 time_end: 1647422084){
      start
      end
    }
  }
}
```

```graphql
{
  "data": {
    "blockchain": {
      "master_seq_no_range": {
        "start": 2670769,
        "end": 2671143
      }
    }
  }
}
```

In the result you will get the required seq\_no range.

<mark style="color:orange;">**Attention! Specifying timestamp range does not mean that there will be no blocks outside this range in the result set: this happens because some thread blocks that were generated outside this time range were committed to masterchain block generated within this time range. But anyway, this pagination allows us to get all blocks in a handy manner, these time deltas are very small and not significant and can be ignored.**</mark>

Use `startCursor` and `hasPreviousPage` == true condition to paginate backwards like this:

```graphql
query{
  blockchain{
      transactions(
        before: "528cc96m05"
      ){
        edges{
          node{
            id
            now
          }
          cursor
        }
        pageInfo{
          startCursor
          hasPreviousPage
        }
      }
  }
}
```

Result:

```graphql
{
  "data": {
    "blockchain": {
      "transactions": {
        "edges": [
          {
            "node": {
              "id": "transaction/2ddaa133fb753c34d72bbae1b9040b773d2b9360612874da5fce19bcdd4b37a1",
              "now": 1647429363
            },
            "cursor": "528cc96m02"
          },
          {
            "node": {
              "id": "transaction/051363b194ebf6cf0ecab31643b34254aef7ec859d027211c0701df9046ad0bc",
              "now": 1647429363
            },
            "cursor": "528cc96m03"
          },
          {
            "node": {
              "id": "transaction/d7b764cd13e5b945aff381e45d1b2b855bf81444950f6b83eb355ac9b2d53ef5",
              "now": 1647429363
            },
            "cursor": "528cc96m04"
          }
        ],
        "pageInfo": {
          "startCursor": "528cc96m02",
          "hasPreviousPage": true
        }
      }
    }
  }
}
```

## Get the number of transactions in a specified shard over a period of time.

Here we specify the only shard of "-1" workchain and time from 18.43 till 19.43. You can do the same for any shard of "0" workchain.

```graphql
query{
  aggregateBlocks(filter:{
    workchain_id:{
      eq:-1
    }
    shard:{
      eq:"8000000000000000"
    }
    gen_utime:{
      gt:1596393798 # Date and time (GMT): Sunday, August 2, 2020 18:43:18
      lt:1596397406 # Date and time (GMT): Sunday, August 2, 2020 19:43:18

    }
  },
    fields:[
      { field: "tr_count", fn:SUM },
    ]
  )
}
```

Result:

```graphql
{
  "data": {
    "aggregateBlocks": [
      "4447"
    ]
  }
}
```
