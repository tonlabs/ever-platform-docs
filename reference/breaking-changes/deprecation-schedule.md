---
description: >-
  Deprecation and breaking changes schedule. Deprecation date - when a breaking
  change or deprecation is announced, sunset date - when it will be applied.
---

# Deprecation schedule

#### A schedule of the currently announced deprecation and sunset dates is provided below

| Feature                                                                                                                          | Deprecation date | Sunset date       | Migration Guide                                                                                                                      |
| -------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `timeout` parameter of `blocks`, `accounts`, `transactions`, `messages` is limited to 3 minutes                                  | Feb 20, 2023     | April 1, 2023     | Use [subscriptions](../graphql-api/subscribe-collections.md) for blocks, accounts, messages, transactions if you need longer timeout |
| Evercloud unauthorized access                                                                                                    | July 6, 2022     | September 1, 2022 | [How to authorize?](../../products/evercloud/get-started.md)                                                                         |
| account boc version 1                                                                                                            | March 23, 2022   | Not defined       | [Migrate](migration-guides.md#migrate\_stats)                                                                                        |
| <p>blockchain {<code>workchain_blocks,</code> <br><code>workchain_transactions,</code><br><code>account transactions}</code></p> | March 16, 2022   | June 1, 2022      | Use `blockchain{ blocks, transactions, account {transactions} }` instead.                                                            |
| `getAccountsCount`                                                                                                               | Feb 21, 2022     | June 1, 2022      | [Migrate](migration-guides.md#migrate\_stats-1)                                                                                      |
| `getTransactionsCount`                                                                                                           | Feb 21, 2022     | June 1, 2022      | [Migrate](migration-guides.md#migrate\_stats-1)                                                                                      |
| `getAccountsTotalBalance`                                                                                                        | Feb 21, 2022     | June 1, 2022      | [Migrate](migration-guides.md#migrate\_stats-1)                                                                                      |
| all `explain*` methods                                                                                                           | Feb 21, 2022     | June 1, 2022      | -                                                                                                                                    |
| `when` argument in all joined fields (for example, transaction.in\_message's `when` argument)                                    | Feb 21, 2022     | June 1, 2022      | -                                                                                                                                    |
| net\*.ton.dev, main\*.ton.dev endpoints                                                                                          | Feb 1, 2022      | May 16, 2022      | [Replaces with new endpoints ](../../products/evercloud/networks-endpoints.md#endpoint-list)                                         |
