# Lab 4: Forwarding Packets

## Overview

This is the second in a set of exercises that have the ultimate goal of creating the "brains" for an IPv4 router. The basic functions of an Internet router are to:

1. Respond to ARP \(address resolution protocol\) requests for addresses

   that are assigned to interfaces on the router. \(Remember that the

   purpose of ARP is to obtain the Ethernet MAC address associated with

   an IP address so that an Ethernet frame can be sent to another host

   over the link layer.\)

2. Receive and forward packets that arrive on links and are destined to

   other hosts. Part of the forwarding process is to perform address

   lookups \("longest prefix match" lookups\) in the forwarding table. We

   will just use "static" routing in our router rather than implement a

   dynamic routing protocol like RIP or OSPF.

3. Make ARP requests for IP addresses that have no known Ethernet MAC

   address. A router will often have to send packets to other hosts,

   and needs Ethernet MAC addresses to do so.

4. Respond to ICMP messages like echo requests \("pings"\).
5. Generate ICMP error messages when necessary, such as when an IP

   packet's TTL \(time to live\) value has been decremented to zero.

The goal of this stage is to accomplish **\#2** and **\#3** above.

Lab-4 assignment in Github Classroom: [https://classroom.github.com/a/pFaSMI1U](https://classroom.github.com/a/pFaSMI1U)

## Your Tasks

After Lab 3, you have implemented the response to ARP on the starter template: `myrouter.py`. Now, on the basis of Lab 3, we continue to improve the router to implement the function of packets forwarding.

The main task for this exercise is to modify the Router class to do the following:

{% hint style="info" %}
The sentences marked with ✅ are related to the content of your report. Please pay attention.
{% endhint %}

### Task 1: Preparation

Initiate your project with our template.

[Start the task here](preparation.md)

### Task 2: IP Forwarding Table Lookup

Build a forwarding table and match the destination addresses.

[Start the task here](forwarding-table-lookup.md)

### Task 3: Forwarding the Packet and ARP

Send an ARP query to create a new Ethernet header and forward the packet.

[Start the task here](make-arp-request.md)

## Handing it in

Create a directory named `report/` in your repository and place your report, capture files and other materials in it.

### Report

We will provide a template of your lab assignment report [here](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/). You need to submit the report in your repository named `<student ID><name>_lab_4.pdf`. The format of your report should be PDF. An example is `123456789拾佰仟_lab_4.pdf`.

### Capture file

The capture file's name should be `lab_4.pcapng` or `lab_4.pcap`.

### Submit to GitHub Classroom

Finally, the directory should be in this structure:

```text
.
├── README.md
├── myrouter.py
├── report
│   ├── 123456789拾佰仟_lab_4.pdf
│   └── lab_4.pcap
├── start_mininet.py
└── testcases
    └── myrouter2_testscenario.srpy
```

Commit the change.

{% hint style="warning" %}
**Only** commit your **source code** to your local repository. If there are some generated files that are not source code, ignore them by adding them in the file `.gitignore`.
{% endhint %}

After you’ve committed you final codes and report, push the repository to GitHub by inputing command:

```text
$ git push
```

After a few seconds, you can see the changes on your repository web page, which means you have handed in successfully.

