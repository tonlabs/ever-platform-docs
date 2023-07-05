# Migration guides

### How to migrate to the new account boc version with `init_code_hash` support <a href="#migrate_stats" id="migrate_stats"></a>

On March 25 DevNet has been updated to support new account structure that now has \`init\_code\_hash\` field. You can check it in playgrounds. This feature has also been added to Evernode SE and DS.

All accounts, that had been deployed before this feature was enabled, will not have this field. If you delete such an account and re-deploy it, \`init\_code\_hash\` will appear.

This feature has been a breaking change for tvm and transaction executor. These components are used for get methods, fees estimation, local debug etc. So, not to cause breaking of all your applications we now temporarily support each account in 2 formats: old and new. If you use SDK of version below 1.31.0, it will download old boc type without support of this feature. Starting from SDK 1.31 new version of boc is downloaded.

If you work directly with GraphQL API, by default, now, it returns the old box version. To query the new one, you need to add http header "X-Evernode-Expected-Account-Boc-Version":"2". Same for GraphQL playgrounds.

![](<../../../.gitbook/assets/image (15).png>)

<mark style="color:orange;">**Support of 2 boc versions is temporary. You need to migrate your applications on DevNet to the new SDK 1.31 version asap or start using special http header if you work with API directly. When the sunset period is over, API will start returning the new boc version by default for all clients. The sunset period is not defined yet, we will keep you informed. Watch for**</mark> [<mark style="color:orange;">**deprecation scedule**</mark>](../deprecation-schedule.md)

### `getAccountsCount, getTransactionsCount, getAccountsTotalBalance` <a href="#migrate_stats" id="migrate_stats"></a>

Use statistics API instead:

```
query{
  statistics{
    accounts{
      totalCount
      totalSupply
      circulatingSupply
      amountOnGivers
    }
    transactions{
      totalCount
    }
    
  }
}
```
