---
title: "Fingerprinting nodes via addr requests"
date: 2025-06-23T16:56:36+02:00
categories: Blog Post
tags:
    - Bitcoin
    - Bitcoin Core
    - P2P
description: This post explores a privacy vulnerability in Bitcoin’s P2P layer, where analyzing ADDR message responses can reveal nodes operating across multiple networks. We present attack findings, discuss implications, and outline possible mitigations.

---

This blog post comes from the research of @naiyoma and me.

It is currently possible to identify nodes running on multiple networks by analyzing their `ADDR` responses. Below, we’ll share highlights from our attack attempts - while deliberately avoiding too much detail about the methodology - and discuss some possible solutions.

This fingerprint attack can hurt network privacy and enable more critical scenarios; for example, it could expose network bridges then to be targeted in partitioning attacks or to gather sensitive metadata.

This attack is not outstanding; for instance, [#28760](https://github.com/bitcoin/bitcoin/issues/28760) outlines a different approach with the same objective, and a separate [research paper](https://www.cs.umd.edu/projects/coinscope/coinscope.pdf) demonstrates how `ADDR` timestamps can be exploited to infer network topology. However, we believe that addressing the attack would make a reasonable incremental step towards network security.

**Our primary goal in publishing this post is to gather feedback on the proposed solutions.** If you have thoughts on potential mitigations, concerns about the trade-offs involved, or ideas we haven’t considered, we’d very much appreciate hearing from you.

# Background

To discover other peers in the network, nodes rely on an address-sharing mechanism. One node can ask another for known peer addresses by sending a `GETADDR` message. In response, the receiving node returns an `ADDR` message, which contains 1000 network addresses (e.g. IP 1.2.3.4) along with their associated timestamps. This helps new or reconnecting nodes to find peers.

When a node is connected to multiple networks (e.g., clearnet and Tor), it maintains separate caches of `ADDR` responses for each network of request origin. These caches are refreshed about once per day (randomized to last between 21 and 27 hours). The caching was introduced in Bitcoin Core 0.21, PR [#18991](https://github.com/bitcoin/bitcoin/pull/18991).

For example, imagine a node’s addrman contains the following entries:
```
{
    "1.1.1.1": 1700000001,
    "2.2.2.2": 1700000002,
    "abc.xyz": 1700000003,
    "9.9.9.9": 1700000004,
    ...
}
```
Its network-specific caches might look like:

IPv4: `{"1.1.1.1": 1700000001, "2.2.2.2": 1700000002, ...}`

Tor: `{"abc.xyz": 1700000003, "2.2.2.2": 1700000002, ...}`

# The attack

The attack works as follows: a spy node looks at `ADDR` messages from “different” nodes, matches them up based on the timestamps returned to observe the overlap. Nodes that appear to be on separate networks but have high overlap in their address/timestamp responses are likely the same node.

# Research highlights

This data was collected in January 2025, and this post focuses on IPv4 <> Tor nodes.

We connected to a total of 20595 reachable nodes running on IPv4 and Tor networks, and recorded the `GETADDR` responses of each node. We filtered our data to only include nodes running Bitcoin Core version 0.21 or later, since version 0.21 introduced an address cache (see [#18991](https://github.com/bitcoin/bitcoin/pull/18991)).

|Network|Number of nodes|
| --- | --- |
|IPv4|7249|
|Tor|13346|

While analyzing the node responses, we noticed that 85% of the IPv4 nodes and 30% of Tor nodes only returned addresses from their own network - for example, IPv4 nodes responding exclusively with IPv4 addresses. We are assuming that these nodes are operating on a single network, and as such, we excluded them from further analysis. This filtering greatly reduced the number of nodes involved:

|Network|Number of nodes after filtering|
| --- | --- |
|IPv4|1034 (15% of initial nodes)|
|Tor|9271 (70% of initial nodes)|

We examined all possible IPv4–Tor node pairs by first coupling each IPv4 node with every Tor node (1034 × 9271 = ~9.5M pairs). We then filtered out pairs with mismatched version or service fields, yielding 515,155 valid pairs for comparison.

Each node’s ADDR response contains 1,000 pairs of (address, timestamp). To identify nodes that might actually be the same, we use two metrics for each node pair:

* Number of Addresses Matching
* Number of Addresses and Timestamps Matching

It is also useful to consider the ratio between the two metrics above, which we'll refer to as the “timestamp match ratio”:

* (Addresses + Timestamps Matching) / (Addresses Matching)

For example, consider two nodes, A and B, with the following ADDR responses:

Node A: 
```
{
    "1.1.1.1": 1700000001, 
    "2.2.2.2": 1700000002,
    "abc.xyz": 1700000003,
    "9.9.9.9": 1700000004,
}
```

Node B:
```
{
    "2.2.2.2": 1700000002,
    "5.5.5.5": 1700000005,
    "abc.xyz": 1700000006,
    "9.9.9.9": 1700000007,
}
```

In this scenario:

* Node A and Node B have three address matches (`2.2.2.2`, `abc.xyz`, `9.9.9.9`)
* They have one Address and Timestamp Match (`2.2.2.2` with timestamp `1700000002`)
* The Timestamp match ratio is 33% (1 out of 3 address matches also match timestamps)

We classified every considered node pair into one of the following categories based on their `GETADDR` responses overlap:

|Category|Number of pairs|
| --- | --- |
|zero addresses in common|9,843 (1.91%)|
|1–10 addresses in common with zero matching timestamps|142,634 (27.69%)|
|more than 10 addresses in common with zero matching timestamps|46,408 (9.01%)|
|at least one address in common with all timestamps matching|257 (0,05%)|
|at least one address in common with some (but not all) timestamps matching|316,013 (61.34%)|


**Category 1**: it’s highly unlikely that two nodes don’t share any addresses, but we still observed some pairs fall into this category. They might involve nodes with fresh address managers or even fake address managers. We can’t draw any meaningful conclusion from these pairs at this point.

**Category 2 and 3**: if two nodes share few (<10) addresses, or even if the share more than 10 but none of them have matching timestamps, they’re probably not the same node.

**Category 4**: If two nodes share several addresses and all of them have the same timestamp, that’s a strong signal they’re actually the same node on different networks.

**Category 5**: Node pairs in category 5 might still trace back to the same node: one interesting edge case we found is that even when two interfaces belong to the same node, they don’t always have a 100% timestamp match ratio.

For example, we looked at the data gathered from a node we control that runs on both I2P and Tor, and observed the following results:

|Node A Address|Node B Address|Address, timestamp matches|Address matches|% timestamp matches over address matches|
| --- | --- | --- | --- | --- |
|<I2P address [redacted]>|<Tor address [redacted]>|5|6|83%|

One possible explanation for this behavior is that `GETADDR` caches for different interfaces are refreshed at different times. The refresh timing is [randomized](https://github.com/bitcoin/bitcoin/blob/af65fd1a333011137dafd3df9a51704fd319feb4/src/net.cpp#L3550), and a cache is only updated when a `GETADDR` request is received. Since each cache acts as a "snapshot" of the node’s addrman at the moment it's refreshed, differences in refresh time can lead to discrepancies between caches for different interfaces.

Based on this broader network analysis, we selected a set of candidate IPv4–Tor node pairs to monitor more closely over a six-week period, during which we contacted each pair daily to gather `ADDR` data. Our selection process was based on a few practical but ultimately arbitrary choices:

* Select pairs with at least 10 matching addresses
* Require a timestamp match ratio of at least 60%
* Monitor the selected pairs daily over a six-week period

This filtering gave us 664 candidate node pairs for further analysis. However, due to the delay between the initial data collection and this follow-up phase (spanning a few weeks), 216 of those pairs were no longer usable because at least one node had become unreachable.

We proceeded to monitor the remaining 448 pairs, with the following results:

* 369 pairs (about 82%) exhibited a timestamp match ratio of >=90% at least once
* 79 pairs (about 18%) never reached >=90% match ratio

The 369 pairs that exhibited a timestamp match ratio of >=90% at least once during the six weeks strongly suggest they correspond to the same nodes observed across two different networks.

We managed to confirm several of our hits through personal contacts with node operators.

While our method was a rough first pass and didn’t cover all the edge cases - so it may include both missed matches and some incorrect ones - the results still show how easy it is to extract metadata that reveals the state of the network.

# Potential mitigations

We began investigating potential solutions and would appreciate feedback on these approaches. The most straightforward direction is to break the fingerprint by manipulating timestamps. There are essentially two approaches to achieve this - one more drastic than the other - but both aim to reduce the usefulness of timestamps for attackers.

## Randomizing timestamps

One idea is to slightly randomize timestamps before inserting them into each `ADDR` cache. This would result in addresses across multiple interfaces carrying slightly different timestamps, making cross-network comparisons less accurate.

The side effect is that we’re degrading the quality of the timestamps, and we should consider whether this can impact the ability of the network to propagate addresses.

## Removing timestamps

Another potential solution is to eliminate timestamps entirely. This idea was [previously discussed](https://github.com/bitcoin-core/bitcoin-devwiki/wiki/P2P-IRC-meetings#topic-remove-timestamps-from-addr-messages-it-seems-like-the-timestamp-is-only-used-to-leak-information-about-our-recent-connectivity-it-doesnt-look-like-we-use-it-to-make-decisions-about-who-to-connect-to-sdaftuarjnewbery) during an IRC meeting about block relay connections, where it was noted that `GETADDR` messages can leak connection times; additionally, some attacks can use timestamps to infer network topology, as described [here](https://www.cs.umd.edu/projects/coinscope/coinscope.pdf).

Bitcoin Core uses timestamps to filter out responses to `GETADDR` requests and sometimes to evict addresses from addrman, but they’re unverifiable and not used to decide who to connect to. In the places where they are used, they could likely be replaced without much trouble using `nlasttry` and `nlastsuccess`.

# Acknowledgments

Much of this process was shaped by the mentorship and feedback of @naumenkogs and @amiti, and our approach was strongly influenced by earlier work and tools from @virtu.

