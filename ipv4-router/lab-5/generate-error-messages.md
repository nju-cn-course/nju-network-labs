# Task 3: Generating ICMP error messages

### ICMP error cases

There are many situations where you'll need to generate ICMP error messages. Before this point, we have either explicitly ignored these error cases, or simply haven't considered them. The following cases describe 4 situations you may encounter, and the corresponding ICMP error messages to generate:

1.  No matching entries: When attempting to match the destination address of an IP packet with entries in the forwarding table, no matching entries are found (i.e., the router doesn't know where to forward the packet).

    In this case, an **ICMP destination network unreachable** error should be sent back to the host referred to by the source address in the IP packet. Note: the ICMP type should be destination unreachable, and the ICMP code should be network unreachable.
2.  TTL expired: After decrementing an IP packet's TTL value during part of the forwarding process, the TTL becomes zero.

    In this case, an **ICMP time exceeded** error message should be sent back to the host referred to by the source address in the IP packet. Note: the ICMP code should be TTL expired.
3.  ARP failure: During the forwarding process, the router often has to make ARP requests to obtain the Ethernet address of the next hop or the destination host. If there is no host that "owns" a particular IP address, the router will never receive an ARP reply.

    If, after 5 retransmission of an ARP request, the router does not receive an ARP reply, the router should send an **ICMP destination host unreachable** back to the host referred to by the source address in the IP packet. Note: the ICMP type should be destination unreachable, and the ICMP code should be host unreachable.
4.  Unsupported function: An incoming packet is destined to an IP addresses assigned to one of the router's interfaces, but the packet is not an ICMP echo request.

    The only packets destined for the router itself that it knows how to handle are ICMP echo requests. Any other packets should cause the router to send an **ICMP destination port unreachable** error message back to the source address in the IP packet. Note: the ICMP type should be destination unreachable, and the ICMP code should be port unreachable.

However, it is also worth noting that an router **SHOULD NOT** reply to any ICMP error messages even though they meet those criteria. ✅ In the report, explain the reason behind this. For details on generating ICMP errors, refer to the Switchyard documentation on [ICMP headers](https://pavinberg.gitee.io/switchyard/reference.html#icmp-internet-control-message-protocol-header-v4).

### Coding

Your task is to implement the logic described above. The start file is named `myrouter.py`.

As a convention, when you create ICMP error packet, you must include as the "data" payload of the ICMP header up to the first 28 bytes of the original packet, starting with the IPv4 header. (That is, your ICMP message will include part of the packet that caused the problem.) The switchyard documentation has an example of doing this, and an example is also given below. Also, be careful to make sure that the newly constructed IP packet you send has a reasonable ($$\ge32$$) TTL --- by default, when you create a new IPv4 header, the TTL value is 0. A code formula for including the "dead" packet in the ICMP payload is shown below:

```python
# assume this is the packet that caused the error
origpkt = Ethernet() + IPv4() + ICMP() 

i = origpkt.get_header_index(Ethernet)
# remove Ethernet header --- the errored packet contents sent with
# the ICMP error message should not have an Ethernet header
del origpkt[i]

icmp = ICMP()
icmp.icmptype = ICMPType.TimeExceeded
icmp.icmpdata.data = origpkt.to_bytes()[:28]
print(icmp)
# ICMP TimeExceeded:TTLExpired 28 bytes of raw payload 
# (b'E\x00\x00\x1c\x00\x00\x00\x00\x00\x01') OrigDgramLen: 0

ip = IPv4()
ip.protocol = IPProtocol.ICMP
# protocol defaults to ICMP
# setting it explicitly here anyway

# would also need to set ip.src, ip.dst, and ip.ttl to 
# something non-zero

pkt = ip + icmp
print(pkt)
# IPv4 0.0.0.0->0.0.0.0 ICMP | ICMP TimeExceeded:TTLExpired 
# 28 bytes of raw payload (b'E\x00\x00\x1c\x00\x00\x00\x00\x00\x01')
# OrigDgramLen: 28

```

✅ In the report, show your logic about generating ICMP error messages.

### Testing

To test your router, you can use the same formula you've used in the past:

```bash
$ swyard -t testcases/testscenario3.srpy myrouter.py
$ swyard -t testcases/testscenario3_advanced.srpy myrouter.py
```

✅ In the report, show the test result of your router.\
(Optional) If you have written the test files yourself, show how you test the forwarding packets.

### Deploying

Once the Switchyard tests pass, you can test your router in Mininet. There is a `start_mininet.py` script available for building the following network topology:

![router2\_topology](../../.gitbook/assets/router2\_topology.png)

(Note that the above topology is not the same as the one implied by the Switchyard tests.)

To test each of the new router functionalities in Mininet, you can open up a terminal on the virtual machine, and cd (if necessary) to the folder where your project files are located (or transfer them into the virtual machine). Then type the following to get Mininet started:

```bash
$ sudo python start_mininet.py
```

Once Mininet is running, open a terminal on the router node (xterm router) and get the router running (`swyard myrouter.py`). Again, be aware that you may need to activate a Python virtual environment in order for this command to succeed.

Next, open a terminal on the client node (`xterm client`). You may:

*   Use the ping tool to send an ICMP echo request to an IP address

    configured on one of the router's interfaces. Ping should

    successfully report that it is receiving replies to the echo

    requests.
*   Use the ping tool and specifically set the initial TTL in the ICMP packets to be 1, so that when your router receives them, it will decrement the TTL to zero and generate an ICMP time exceeded error. The -t flag to ping allows you to explicitly set the TTL. For example:

    ```clike
    client# ping -c 1 -t 1 192.168.200.1
    ```
*   Send a ping from the client to an address that doesn't have a match in the router's forwarding table. There is a route set up on the client to forward traffic destined to 172.16.0.0/16 to the router, but the router doesn't have any forwarding table entry for this subnet. So the following ping should result in an ICMP destination net unreachable message sent back to the client:

    ```clike
    client# ping -c 1 172.16.1.1
    ```
*   Probably the most complicated test you can run is to do a "traceroute" across the toy network in Mininet:

    ```clike
    client# traceroute 192.168.100.1
    ```

    The output should be similar to the following:

    ```clike
    traceroute to 192.168.100.1 (192.168.100.1), 30 hops max, 60 byte packets
     1  10.1.1.2  409.501 ms  201.130 ms  200.578 ms
     2  192.168.100.1  607.775 ms  401.868 ms  401.920 ms 
    ```

Your task is: test your router from another host (server1 or server2) as described above. Using Wireshark to prove that your router can correctly respond to ICMP messages and generate ICMP error messages when necessary. ✅ Briefly write the procedure and analysis in your report with screenshots.
