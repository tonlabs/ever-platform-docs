# Quick Start

## How to use playground

Choose any playground from the network list

{% content-ref url="../../reference/graphql-api/networks.md" %}
[networks.md](../../reference/graphql-api/networks.md)
{% endcontent-ref %}

You will see the GraphQL playground.

In this picture we query the GraphQL API version on the left and see the result on the right.

```graphql
query{
  info{
    version
  }
}
```

![scr1.png](../../.gitbook/assets/scr1.png)

## API documentation

GraphQL schema is self-documented. You can find all fields descriptions in the playground. Click this button:

![](<../../.gitbook/assets/image (1) (1) (1).png>)

## Access API from Client Libraries

Check out [Client Libraries net module](https://docs.everos.dev/ever-sdk/reference/types-and-methods/mod\_net).

Use [`query_collection`](https://docs.everos.dev/ever-sdk/reference/types-and-methods/mod\_net#query\_collection) method for `blocks`, `accounts`, `messages`, `transactions` queries.

Use [`query`](https://docs.everos.dev/ever-sdk/reference/types-and-methods/mod\_net#query) method for all other queries.
