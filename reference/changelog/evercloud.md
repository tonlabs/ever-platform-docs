---
description: Evercloud API changelog
---

# Evercloud

## 0.65.0

New

* Added field `BlockchainBlock.signatures` – temporarily available only in non archive mode.
* Added argument `blockchain.account.info(byBlock)` – ability to get the account state from the specified block. Only states for the last several minutes are available (last 5 minutes). This feature can be used to implement consistent behaviour of application on top of nodes cluster.&#x20;

## 0.61.0

New

* `trace` field in transaction (data is available only in SE)
* `archive` parameter in blockchain signle-entity fields (`block`, `block_by_seq_no`, `transaction`, `message`).\
  When is `true` the blockchain requests use an archive database with full blockchain history\
  but reduced field set.
* `transactions_by_lt` query is added to `blockchain.account` to paginate account transactions by logical time
* `thread` parameter in `blockchain.block_by_seq_no` and `blockchain.blocks` is deprecated. `shard` parameter should be used instead
* `blockchain.prev_shard_blocks` and `blockchain.next_shard_blocks` queries for blocks chain iteration
* `blockchain.transactions_by_in_msg` query for retrieving transaction by inbound message
* `blocks` collection latency is calculated from `gen_utime` of masterchain block which has commited last known shard block

## 0.60.0

New

* Added parameter `archive` to blockchain lists fields.\
  When is `true` the blockchain requests use an archive database with full blockchain history\
  but reduced field set.

## 0.59.0

New

* Added helpful error messages in case of invalid `in` and `notIn` filter `{in: null}`.

## 0.58.0

New

* `master_seq_no` field in blocks, transactions and messages representing seq\_no of masterchain block\
  which committed the block, transaction or message
* `chain_order` in messages is derived from `src_chain_order` and `dst_chain_order`
* `message.counterparties` in `blockchain` API are not limited to internal messages

## 0.57.0

New

* Case-insensitive filtering in args:\
  `blockchain.block(hash)`, `blockchain.transaction(hash)`, `blockchain.message(hash)`
* Address filtering in args (accepts address in any format):\
  `blockchain.account(address)`, `messages.src`, `messages.dst`, `transactions.account_addr`, `accounts.id`
* Address formatting args in fields:\
  `blockchain.account.info.address`, `blockchain.transaction.account_addr`,\
  `blockchain.message.src`, `blockchain.message.dst`,\
  `messages.src`, `messages.dst`, `transactions.account_addr`, `accounts.id`
* `accounts(filter:{id:{eq:""}})`, `accounts(filter:{id:{in:[""]}})`, `blockchain.account(address:"")`\
  returns account of type `NonExist` if an account is missing in current shard state.
* Support new config parameter 44 (black list)

## 0.56.0

New

* Added ability to post external messages to JRPC endpoint

## 0.55.1

New

* Queries slow detector is improved.

## Blockchain Statistics Add-on 1.1.0

New

* `accounts.totalActiveCount` field
* `accounts.totalCount` field doesn't count deleted accounts
* Solidity elector supported
* Errors are not returned to user. `null` is returned instead

## 0.55.0

New

* Support new caps in capabilities\_flags.

Fixed

* `PageInfo.startCursor` and `PageInfo.endCursor` were required.

## Fungible Token Add-on 1.7.0

New

* Holder wallets search has new parameter `tokens` to filter wallets by provided list of token roots
* Tokens search has new parameter `rootOwner` to filter tokens by provided root owner
* Transfers can be filtered by types list

## 0.54.4

New

* Remove "UNSTABLE" marks from `blockchain` API
* Add indexHint to `messages(filter: {src: *, dst: *, created_at: *, * } orderBy: {path: "created_at"})` queries

## TON mainnet endpoint

New

* [TON (The Open Network) mainnet endpoint](../graphql-api/networks.md) added

## Query Cost and Price Add-ons 1.2.0

New

* [query Cost API](../evercloud-api-add-ons/query-cost.md) added&#x20;
* [Price API](../evercloud-api-add-ons/price.md) added&#x20;

## Flex Add-on 1.7.1

New

* [Flex API ](../evercloud-api-add-ons/flex-api.md)is added to Evercloud

## 0.54.1

New

* increase `Q_REQUESTS_MAX_SIZE` default to 64kb (now it is possible to send external inbound messages of size > 16kb, up to 64kb)

## 0.53.4

New

* `capabilities_flags` companion field to p8 config parameter `capabilities`

Fixed

* `in: []` (empty list) filter now leads to an empty query result as expected
* `notIn: []` (empty list) filter now is ignored as expected

## 0.53.2

New

* Add new option `--query-max-timeout-arg` or `Q_QUERY_MAX_TIMEOUT_ARG`.\
  This adds upper boundary for `timeout` parameter in collection API queries.\
  Default is 24 hours.

## Fungible Token Add-on 0.1.0

New

* [FT (Fungible token) API](../evercloud-api-add-ons/ft-fungible-token-api.md) is added to Evercloud

## 0.52.0

New

* master config `p30`, `p40`, `p42` fields types
* `prev_code_hash` account field
*   `allow_latest_inconsistent_data` option in paginated `blockchain` queries:

    > By default there is a delay of realtime data (several seconds) to ensure impossibility of data inserts before the latest accessible cursor. Now it became possible to disable this guarantee and thus - to reduce delay of realtime data by setting this flag to true.
* two config options for reading external subscriptions health messages from Redis channel
  * `subscriptions-health-redis-channel`
  * `subscriptions-health-timeout`

Fixed

* `blockchain.master_seq_no_range` behavior for edge cases (when boundaries are close to first and/or latests blocks) E.g. for `time_start=time_end=now` this function used to return `end<start` but now it returns `end=null`, because a masterblock for end doesn't exist yet.
* `max_shard_gen_utime_string` and `min_shard_gen_utime_string` in `BlockMaster`

Improved

* faster and more reliable ArangoDB query for `blockchain.master_seq_no_range.end`

Removed

See the [migration guide](../breaking-changes/migration-guides/#migrate\_stats-1)

Queries:

* `blockchain.workchain_blocks`. Use `blockchain{ blocks }` instead.
* `blockchain.workchain_transactions`. Use `blockchain{ transactions }` instead.
* `blockchain.account_transactions`. Use `blockchain{ account{ transactions } }` instead.
* `explainQueryAccounts`
* `explainQueryTransactions`
* `explainQueryMessages`
* `explainQueryBlocks`
* `explainQueryBlockSignatures`
* `explainQueryZerostates`
* `getAccountsCount`
* `getTransactionsCount`
* `getAccountsTotalBalance`
* `QueryExplanation` and `SlowReason` types

## 0.51.0

New

* Deleted accounts added to API (even old ones)
* `Subscription.remReceipts` subscription.

Improved

* Subscriptions improved: no more websocket reconnections on stable network, no missed data during short websocket reconnections caused by network problems&#x20;

## 0.48.0

New

* Add new functions to `blockchain` root api:
  * `account`, allows:
    * to fetch account info (e.g. boc) via `info` field
    * to fetch transaction info via `transactions` (similar to now deprecated `blockchain.account_transactions`)
  * `blocks` (is similar to now deprecated `workchain_blocks`)
  * `transactions` (is similar to now deprecated `workchain_transactions`)
* Support for `null` in scalar filter. e.g. `filter: { last_paid: null }`. Means missing filter.

Deprecated

* `blockchain { workchain_blocks }`. Use `blockchain{ blocks }` instead.
* `blockchain { workchain_transactions}`. Use `blockchain{ transactions }` instead.
* `blockchain { account_transactions }`. Use `blockchain{ account{ transactions } }` instead.

Breaking

* In `blockchain.key_blocks` rename `seq_no` argument to `master_seq_no_range`.

## 0.47.0

New

* `account.init_code_hash` – account 's initial code hash (when it was deployed). All new accounts will have this field after such capability is enabled in the network.&#x20;
* Support `X-Evernode-Expected-Account-Boc-Version` http header. `1` (default) means "return old boc version" , `2` – "return new boc version" (with `init_code_hash` field).

## 0.46.0

Deprecated

* `when` argument in all joined fields (for example, transaction.in\_message's `when` argument)
* the following root queries:
  * `explainQueryAccounts`
  * `explainQueryTransactions`
  * `explainQueryMessages`
  * `explainQueryBlocks`
  * `explainQueryBlockSignatures`
  * `explainQueryZerostates`
  * `getAccountsCount`
  * `getTransactionsCount`
  * `getAccountsTotalBalance`
* `QueryExplanation` type

[Migration guide](../breaking-changes/migration-guides/)

[Deprecation schedule](../breaking-changes/deprecation-schedule.md)

## Blockchain Statistics Add-on 1.0.4

New

[`Statistics` API](../evercloud-api-add-ons/statistics.md)

