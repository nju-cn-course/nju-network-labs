# FAQ

1.  **Q:** What should the router do when an irrelevant ARP packet arrives at the router?

    **A:** The router needs to check its Ethernet destination first. If the destination is neither a broadcast address nor the MAC of the incoming port, it should always be dropped. Then, the router should only process it when the ARP destination IP is held by a port of the router. If it passed the aforementioned check, you need to cache the source in the ARP packet, regardless of its operation field.
2.  **Q:** Should the ARP table learn from non-ARP packets?

    **A:** Non-ARP packets should always be ignored as for ARP table.
3.  **Q:** If I implement the timeout mechanism, should the router actively send ARP request before the corresponding entry expires?

    **A:** Your router should do nothing before the entry expires. However, the logic afterwards is totally up to you. For the expired entry, either refreshing or removing is acceptable.
4.  **Q:** What should the router do in the following scenario? Packet for a certain IP address arrives and it sends an ARP request. Before receiving the ARP reply, it receives another non-ARP packet for the same IP address, does it send an ARP request again?

    **A:** You do not retransmit the ARP request. More generally, even if your router receives many packets for a certain IP address while there is an ongoing ARP request for that address, it should not send out any new ARP requests or update the timestamp of the initial ARP request. Meanwhile, your router should buffer the packets for transmitting once it receives the ARP reply. **IMPORTANT**: If your router buffers multiple packets for an ongoing ARP request, upon receiving the corresponding ARP reply, these packets has to be forwarded in the arrival order. Beyond this, multiple ongoing ARP requests also follow the FCFS order.
5.  **Q:** When the router needs to make an ARP request for the next hop IP address (obtained after the longest prefix match lookup), should it flood the request on all ports?

    **A:** The router does not flood the ARP request on all ports. The ARP query is merely broadcast on the port from lookup. The response ARP query _should_ come back on the same port but it doesn't actually need to (and it doesn't matter for the purposes of forwarding the packet or sending out the ARP request).
6.  **Q:** What to do with packets with IP option field(s)?

    **A:** As for this course, you need not support those operations. It is OK to simply treat them as standard IPv4 packets. Just keep the option fields as they were.
7.  **Q:** When sending ICMP echo replies or error messages, does the router need to do a forwarding table lookup and send ARP requests if needed? Can't the router just send the ICMP messages back on the interface through which the IP packet was received?

    **A:** The router will still need to do an ARP query as it normally does for forwarding an IP packet. It doesn't matter that an echo request arrives on, say port eth0. The echo reply may end up going out on a different port depending on the forwarding table lookup. The entire lookup and ARP query process should be the same as forwarding an IP packet, and will always behave exactly this way.
8.  **Q:** How many error messages should be generated if a packet meets multiple criteria at the same time, say one has TTL expired and network unreachable errors?

    **A:** Your router will only generate one error in this case. Specifically, the precedence should be in accordance with the router's processing logic. As for the example above, since the router decrements the TTL field after doing a lookup, if the lookup fails then your router will not reach at decrementing the TTL value.
9.  **Q:** If there are multiple packets buffered for the same destination host or next hop and the router doesn't receive an ARP reply after 5 retransmissions of ARP requests, what should the router do?

    **A:** Your router should send an ICMP destination host unreachable message back to the host referred to by the source address in the IP packet. When there are multiple packets buffered for the same destination address, the router will send an ICMP error message to the source host for each packet (even if the same source host sent multiple packets).
10. **Q:** Part of the instructions say if the packet is for us (i.e. it's destination ipaddr is in net.interfaces) then drop it. But later on, the instructions say that if the packet is meant for one of our directly connected neighbors then we forward it. How is net.interfaces() different than the directly connected networks.

    **A:** The net.interfaces() is the information for the interfaces on the router itself. For example, eth1 might have an ipaddr of 192.168.1.1. However, from these interfaces you can use the ipaddr and netmask to get the subnet that the interface is directly connected to. So if the netmask of 255.255.255.0 for the same eth1, then the subnet would be the mask applied to the ipaddr which is 192.168.1.0. So, if a packet came to the router destined for an ip address like 192.168.1.100, then you would find a match in the forwarding table and end up forwarding the packet out eth1. But if a packet came to the router destined for 192.168.1.1, then you would drop it, since that is the exact ip address of the interface.
11. **Q:** In the instruction of our project, we are told that the echo reply may end up going out on a different port depending on the forwarding table lookup. What does this mean?

    **A:** The source ip address of the ICMP response does not necessarily need to be the ip address of the interface that you are sending the reply packet out of. The ICMP reply should have an ip.src and ip.dst that are the the ip.dst and ip.src of the ICMP request respectively (that is just switch them around). However, the destination address is looked up in the forwarding table to determine what port to send the reply out of. So the `src ip` address and the port of the ICMP reply may not match up to the same interface.
