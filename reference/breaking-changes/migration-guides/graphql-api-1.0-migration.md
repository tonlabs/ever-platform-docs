---
description: >-
  Described changes are to be introduced in the end of August 2023. Learn how to
  make the transition seamless for your applications.
---

# GraphQL API 1.0 migration

## About coming changes

{% hint style="danger" %}
There will be several announcements prior to the actual API transition, which is planned for approximately the end of August 2023.

To stay updated on the transition, you can monitor [this](https://docs.evercloud.dev/reference/breaking-changes/deprecation-schedule) page or join [this](https://t.me/ever\_sdk\_channel) channel for announcements.
{% endhint %}

As TVM blockchains adoption grows, and with it blockchain load, so does the load on GraphQL API. To be able to cope with large amounts of data, GraphQL API is being refactored.

### Major changes

* The Collections API for blocks, transactions and messages in the new version will only provide data from the last 7 days. To retrieve older (archive) data, you need to use the `blockchain{...}` API with `archive: true` mode on.

{% hint style="warning" %}
☝ The access to archive data will have rps limits, so make sure you don’t implement many requests per second if you specify `archive: true`. **Limit yourself with maximum of 5 requests of archive data per second from one ip address.** If you need higher rate contact @EkaterinaPantaz
{% endhint %}

* The Accounts collection will not provide account boc, code and data anymore. Use `blockchain{account(address:"address"){info{boc}}`} for that.

## Specification

Take a look at the updated API. Read comments carefully.

```graphql
# Account.boc, code, data fields are removed from accounts collection 
# Use blockchain{account(address:"addrress"){info{boc}}} instead
accounts

#works the same way
aggregateaccounts

# work on last-7-days data
blocks
transactions
messages
aggregateblocks
aggregatetransactions
aggregatemessages

# blockchain API now provides access to archive data (older than 7 days)
# by default recent data is retrieved
# to get archive data, specify archive=true flag
# archive data has bigger latency than recent data (new data is recorded there 
# with a latency of a second), keep it in mind when you design UX 
# and architecture of your application 

blockchain{
	account {
		info(address: String): BlockchainAccount # works the same way
		transactions(..., archive: bool default = false):BlockchainTransaction
		messages(..., archive: bool default = false):BlockchainMessage
	}
	key_blocks(..., archive: bool default = false): BlockchainBlock
	blocks(..., archive: bool default = false): BlockchainBlock
	transactions(..., archive: bool default = false): BlockchainTransaction
	transaction(..., archive: bool default = false): BlockchainTransaction
	message(..., archive: bool default = false):BlockchainMessage
	block(..., archive: bool default = false): BlockchainBlock
}
```

Detailed documentation of Blockchain API is available [here](https://docs.evercloud.dev/reference/graphql-api/blockchain).

## Migration guide

To prepare for the transition from current functionality to the new one, and make sure it happens seamlessly, developers should migrate their queries as soon as possible - before the actual release of the new API, which will happen in August 2023.

We have released a flag called '`archive`' for this purpose. Currently, this flag has no functionality (the API works the same way whether `archive=true` or `false`), but it helps in migrating to the new API. When you migrate, the switch from the old API to the new one will be under the hood and invisible to your application.

Be sure to specify the `archive=true` flag only in places where you need to retrieve data older than 7 days. If you do not require old data, do not include the flag.&#x20;

> **Note**: If you could not find a suitable query for your use case in the blockchain API, please message [@EkaterinaPantaz](https://t.me/EkaterinaPantaz) in Telegram, and we will see what we can do to prepare for migration in your individual case.
>
> The Evercloud team has collected and analyzed API usage statistics and developed an updated blockchain API that covers over 95% of use cases involving archive data. If your use case falls within the <5% where the new API is or seems entirely inapplicable, please also contact @Ekaterina Pantaz, and we will work together to adapt your use case to the API or vice versa.

## Query migration samples

### Get account data

Get account information

**Old query:**

```graphql
query {
  accounts(filter:{
    id:{
      eq:"0:d707caf6df3a7c2bb0b64915613eca9d8f17ca1de0b938dfdcbb9b4ff30c4526"
    }
  }){
    balance
    boc
  }
}
```

New query:

```graphql
{
  blockchain{
    account(address:"0:d707caf6df3a7c2bb0b64915613eca9d8f17ca1de0b938dfdcbb9b4ff30c4526"){
      info{
        balance
        boc
      }
    }
  }
}
```

### Get external outbound messages from account to :external\_address

Retrieve the last 2 external outbound messages from `src_address` to the “`:external address`”

Old query:

```graphql
query {
 messages(
   filter: {
	   msg_type: { eq: 2 },
	   src: { eq: "src_address" },
	   dst: { eq: ":external_address" },
   }
   orderBy: [{path: "created_at", direction: DESC}],
	limit:2
 ) {
      body
      msg_type
      id
      src
      created_at
      created_lt
      dst
 }
}
```

New query:

Get the last 2 `src_address`' s `ExtOut` messages filtered by "`:external_address`" counterparty and save the last message cursor for the next reads.

```graphql
query {
 blockchain {
  account(address:"src_address") {
   messages(
    msg_type: [ExtOut],
    counterparties: [":external_address"],
    last: 2,
	archive: true #specify false for realtime data
   ) {
    edges {
     node {
      body
      msg_type
      id
      src
      created_at
      created_lt
      dst
     }
     cursor
    }
   }
  }
 }
}
```

### Get account messages after timestamp

Get 100 messages of account $p3 of type $p2 created after timestamp $p1 sorted by creation timestamp ASC.

Old query

{% hint style="info" %}
☝ Pagination was implemented by created\_at timestamp. Please, note that this pagination is not correct and may end up with data loss in case of several messages (more than 50) created per 1 second, which may happen in highly loaded blockchains.
{% endhint %}

```graphql
query { 
	messages ( 
	filter : { 
		created_at : { gt : $p1 } 
		msg_type_name : { eq : $p2 } 
		src_account : { id : { eq : $p3 } } 
	} 
	limit : 50 
	orderBy : { direction : ASC path : "created_at" } ) 
	{ 
		boc 
		body 
		created_at 
	  data 
		id 
		msg_type 
		msg_type_name 
	} 
}
```

New query

{% hint style="info" %}
☝ This approach is reliable without data loss
{% endhint %}

```graphql
# Get the start of the pagination range for the specified timestamp $p1
query{
  blockchain{
    start_seqno: master_seq_no_range(time_start: $p1){
      start
    }
  }
}
```

Now query first page (one page is 50 messages) messages starting from $start

```graphql
query{
  blockchain{
    account(address: $p3){
      messages(master_seq_no_range:{start:$start_seqno}, first:50, archive: true){
        edges{
          node{
            boc 
            body 
            created_at 
            data 
            id 
            msg_type 
            msg_type_name             
          }
        }
        pageInfo{
          endCursor # we got "59e5c7c0059f9522070001"
          hasNextPage
        }
      }
    }
  }
}
```

And now the second page after `endCursor` = “59e5c7c0059f9522070001”

```graphql
query{
  blockchain{
    account(address: $p3){
      messages(master_seq_no_range:{start:$start_seqno}, first:50, after:"59e5c7c0059f9522070001"){
        edges{
          node{
            boc 
            body 
            created_at 
            data 
            id 
            msg_type 
            msg_type_name             
          }
        }
        pageInfo{
          endCursor
          hasNextPage
        }
      }
    }
  }
}
```

### Get account transactions

Get last 100 account transactions in descending order

Old query

```graphql
query { 
  transactions ( 
    filter : { 
      account_addr : { eq : $p1 } 
      lt : { le : $p2 }
    } 
    limit : 50 
    orderBy : [ { direction : DESC path : "lt" } ] ) 
  { 
    boc 
  }
}
```

New query

{% hint style="warning" %}
☝ Note that sorting order in `blockchain` api is always in ascending order, so if you need to process the data in descending order on the client, sort it by cursor field in descending order on the client side
{% endhint %}

Here we get last 50 transactions of account

```graphql
query{
  blockchain{
    account(address:"0:d807caf6df3a7c2bb0b64915613eca9d8f17ca1de0b938dfdcbb9b4ff30c4526"){
      transactions(
	last:50, 
	archive: true # specify false if only last 7-days-data is needed
      ){
        edges{
          node{
            boc
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
}
```

and now in the second query we get last 50 transactions before the `startCursor` from the result of the previous query

```graphql
query{
  blockchain{
    account(address:"0:d807caf6df3a7c2bb0b64915613eca9d8f17ca1de0b938dfdcbb9b4ff30c4526"){
      transactions(
	last:50, 
	before:"61219b5800618b3ffc0106", 
	archive: true # specify false if only last 7-days-data is needed
      ){
        edges{
          node{
            boc
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
}
```

### Get transaction/message/block data (including historic)

Old queries

```graphql
query{
tx: 
  transactions(filter:{
    id:{
      eq:"b55173bdca10b50d3fe9b947555be5ff57bc73d0778adea54a33aa64a377cee8"
    }
  }){
    balance_delta
    account_addr
    boc
  }
 bl: blocks(filter:{
    id:{
      eq:"03a990cfa633a2c281ced23221b7384d9fb9969538e000f1ec567311e9b197c4"
    }
  }){
    workchain_id
    shard
    seq_no
  }
 bl_by_seq_no: blocks(filter:{
    	workchain_id:{ eq:0}
      shard:{eq:"2000000000000000"}
      seq_no:{eq: 17659538}
    
  }){
    workchain_id
    shard
    seq_no
  }
  
 msg: messages(filter:{
    id:{
      eq:"03322f2260a7580592f264d0a578159049cf6ac00b87a0c2c9ed5aee53e23265"
    }
  }){
    value
    src
    dst
  }
}
```

New queries:

{% hint style="warning" %}
☝ Make sure to specify `archive` flag according to your use-case
{% endhint %}

```graphql
query{
  blockchain{
    tx: transaction(hash:"b55173bdca10b50d3fe9b947555be5ff57bc73d0778adea54a33aa64a377cee8", archive: true){
      balance_delta
      account_addr
      boc
    }
    bl: block(hash:"03a990cfa633a2c281ced23221b7384d9fb9969538e000f1ec567311e9b197c4", archive: true){
      workchain_id
      shard
      seq_no
      gen_utime_string
    }
    bl_by_seq_no: block_by_seq_no(workchain:0, thread:"2000000000000000", seq_no:17659538, archive: true ){
     	workchain_id
      shard
      seq_no
      gen_utime_string
    }
    msg: message(hash:"03322f2260a7580592f264d0a578159049cf6ac00b87a0c2c9ed5aee53e23265", archive: true){
      value
      src
      dst
    }
  }
}
```
