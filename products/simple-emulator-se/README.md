---
description: Lite Everscale emulator with GraphQL API
---

# Simple Emulator (SE)

Test your basic Dapp functionality locally. Simple emulator is a light-weight Evernode instance with GraphQL API that suits good for DApp and contract testing in 90% cases.

Behaviour of Evernode SE is a bit different than that of a real network.&#x20;

New product [Evernode Network Emulator (NE)](../network-emulator-ne.md) is going to solve this problem.

{% embed url="https://github.com/tonlabs/evernode-se" %}

### Differences between Evernode SE and a real network <a href="#23854b" id="23854b"></a>

* It does not produce empty blocks like the real blockchain does. It produces blocks only if it processes some messages.&#x20;
* There is no threading and masterchain - only 1 thread of zero workchain
* It records aborted external inbound messages to the database. It means that if your contract failed to execute an external inbound message, it will end up with an error immediately, and you can see this transaction in graphql playground. You can not see such behaviour on the real network. Most of aborted external messages do not create transactions there.
* There are no elections, no validators and no elector contract deployed
