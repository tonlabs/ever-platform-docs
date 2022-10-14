# Public API

## May 25, 2022

### New

* Deleted accounts added to API (even old ones)

## May 17, 2022

### Improved

* Subscriptions improved: no more websocket reconnections on stable network, no missed data during short websocket reconnections caused by network problems&#x20;

## March 16, 2022

### New

* Add new functions to `blockchain` root api:
  * `account`, allows:
    * to fetch account info (e.g. boc) via `info` field
    * to fetch transaction info via `transactions` (similar to now deprecated `blockchain.account_transactions`)
  * `blocks` (is similar to now deprecated `workchain_blocks`)
  * `transactions` (is similar to now deprecated `workchain_transactions`)
* Support for `null` in scalar filter. e.g. `filter: { last_paid: null }`. Means missing filter.

### Deprecated

* `blockchain { workchain_blocks }`. Use `blockchain{ blocks }` instead.
* `blockchain { workchain_transactions}`. Use `blockchain{ transactions }` instead.
* `blockchain { account_transactions }`. Use `blockchain{ account{ transactions } }` instead.

### Breaking

* In `blockchain.key_blocks` rename `seq_no` argument to `master_seq_no_range`.

## Feb 26, 2022

### New

* `account.init_code_hash` – account 's initial code hash (when it was deployed). All new accounts will have this field after such capability is enabled in the network.&#x20;
* Support `X-Evernode-Expected-Account-Boc-Version` http header. `1` (default) means "return old boc version" , `2` – "return new boc version" (with `init_code_hash` field).

## Feb 21, 2022

### Deprecated

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

[Migration guide](../breaking-changes/migration-guides.md)

[Deprecation schedule](../breaking-changes/deprecation-schedule.md)

## Feb 1, 2022

### New features

``[`Statistics`](https://docs.everos.dev/ever-sdk/samples/graphql-samples/statistics) API

