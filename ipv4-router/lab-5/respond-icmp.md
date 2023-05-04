# Task 2: Responding to ICMP echo requests

## Procedure

The first task is to respond to ICMP echo request ("pings") destined for the router. Prior to a forwarding decision (i.e., a forwarding table lookup), you should first check whether the IP destination address is among one of the router's interfaces. If the packet is an ICMP echo request, you should construct an ICMP echo reply and send it back to the original host initiating the ping. To do that, you should:

* Construct an ICMP header + echo reply, correctly populating the fields in the header. See the Switchyard documentation for details on [ICMP packet headers](https://pavinberg.gitee.io/switchyard/reference.html#icmp-internet-control-message-protocol-header-v4). When creating the EchoReply, do the following:
  * Copy the sequence number from the request into the reply you make.
  * Copy the identifier from the request into the reply.
  * Set the data field in the reply to be the same as the data in the request.
* Construct an IP header. The destination IP address should be set as the source address of the incoming ICMP echo request, and the IP source address should be set as the router's interface address. The next header, apparently, should be the ICMP header you just created.
* Send (forward) the packet you constructed. Since you can already do forwarding table lookups and ARP requests, if you've designed the previous parts of your router reasonably well, this should just be a method/function call for forwarding the echo response.

## Coding

Your task is to implement the logic described above. The start file is `myrouter.py`.

✅ In the report, briefly show your logic about responding to ICMP echo requests.

## Testing

Wait until Task 3 is completed before testing.
