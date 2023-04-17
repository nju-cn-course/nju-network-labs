# FAQ

1.  **Q:** What should the router do in the following scenario? Packet for a certain IP address arrives and it sends an ARP request. Before receiving the ARP reply, it receives another non-ARP packet for the same IP address, does it send an ARP request again?

    **A:** You do not retransmit the ARP request. More generally, even if your router receives many packets for a certain IP address while there is an ongoing ARP request for that address, it should not send out any new ARP requests or update the timestamp of the initial ARP request. Meanwhile, your router should buffer the packets for transmitting once it receives the ARP reply. **IMPORTANT**: If your router buffers multiple packets for an ongoing ARP request, upon receiving the corresponding ARP reply, these packets has to be forwarded in the arrival order!
2.  **Q:** When the router needs to make an ARP request for the next hop IP address (obtained after the longest prefix match lookup), should it flood the request on all ports?

    **A:** The router does not flood the ARP request on all ports. The ARP query is merely broadcast on the port from lookup. The response ARP query _should_ come back on the same port but it doesn't actually need to (and it doesn't matter for the purposes of forwarding the packet or sending out the ARP request).
