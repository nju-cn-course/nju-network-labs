# FAQ

1.  **Q:** What should the router do when an irrelevant ARP packet arrives at the router?

    **A:** The router needs to check its Ethernet destination first. If the destination is neither a broadcast address nor the MAC of the incoming port, it should always be dropped. Then, the router should only process it when the ARP destination IP is held by a port of the router. If it passed the aforementioned check, you need to cache the source in the ARP packet, regardless of its operation field.
2.  **Q:** Should the ARP table learn from non-ARP packets?

    **A:** Non-ARP packets should always be ignored as for ARP table.
3.  **Q:** If I implement the timeout mechanism, should the router actively send ARP request before the corresponding entry expires?

    **A:** Your router should do nothing before the entry expires. However, the logic afterwards is totally up to you. For the expired entry, either refreshing or removing is acceptable.
