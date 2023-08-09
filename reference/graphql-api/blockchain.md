---
description: Learn about basic blockchain API
---

# Blockchain API

![](<../../.gitbook/assets/image (34).png>)

`blockchain` root type is API that includes such basic real-time data as:

* [blocks](../../samples/graphql-samples/blocks.md)
* [key blocks](../../samples/graphql-samples/blocks.md#key-blocks-pagination)
* [transactions](../../samples/graphql-samples/transactions.md)
* [account](../../samples/graphql-samples/accounts.md)
  * [account info](../../samples/graphql-samples/accounts.md#get-account-info)
  * [account transactions](../../samples/graphql-samples/accounts.md#pagination-of-account-transactions)
  * [account messages](../../samples/graphql-samples/accounts.md#pagination-of-accounts-messages)
* (coming soon) accounts - allows to paginate accounts by deploy time or init time, filtering by code\_hash

This API is truly a Graph-oriented API .

We followed GraphQL best practices and implemented Relay Cursor Connections Specification for pagination for all lists. You can read more here [https://relay.dev/graphql/connections.htm](https://relay.dev/graphql/connections.htm)

{% hint style="info" %}
**Note**: With [graphql-api-1.0-migration.md](../breaking-changes/migration-guides/graphql-api-1.0-migration.md "mention") by default Blockchain API provides only data for the past 7 days. For use cases where earlier data is needed make sure to use the `archive: true` flag in `blockchain` query filters. Do not however use it, if archive data isn't required, as it will generate unnecessary load.
{% endhint %}
