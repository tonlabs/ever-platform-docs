---
description: Learn playground, schema and SDK
---

# Quick Start

## How to use playground

Choose any playground from the network list

{% content-ref url="reference/graphql-api/networks.md" %}
[networks.md](reference/graphql-api/networks.md)
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

![scr1.png](.gitbook/assets/scr1.png)

## About API schema

GraphQL schema is self-documented. You can find all fields descriptions in the playground. Click this button:

![](<.gitbook/assets/image (1) (1) (1).png>)

## Access API from Client Libraries

[https://docs.everos.dev/ever-sdk/guides/queries\_and\_subscriptions/raw\_query](https://docs.everos.dev/ever-sdk/guides/queries\_and\_subscriptions/raw\_query)
