# Lab 3: Respond to ARP

## Overview

This is the first in a series of exercises that have the ultimate goal of creating an IPv4 router. The basic functions of an Internet router are to:

1. Respond to ARP \(Address Resolution Protocol\) requests for addresses that are assigned to interfaces on the router.
2. Make ARP requests for IP addresses that have no known Ethernet MAC address. A router will often have to send packets to other hosts, and needs Ethernet MAC addresses to do so.
3. Receive and forward packets that arrive on links and are destined to other hosts. Part of the forwarding process is to perform address lookups \("longest prefix match" lookups\) in the forwarding information base. You will eventually just use "static" routing in your router, rather than implement a dynamic routing protocol like RIP or OSPF.
4. Respond to Internet Control Message Protocol \(ICMP\) messages like echo requests \("pings"\).
5. Generate ICMP error messages when necessary, such as when an IP packet's TTL \(time to live\) value has been decremented to zero.

The goal of this first stage of building the router is to accomplish item **\#1** above: respond to ARP requests.

Lab-3 assignment in Github Classroom: [https://classroom.github.com/a/9QvgOqHw](https://classroom.github.com/a/9QvgOqHw)

## Your Tasks

In the source directory for this exercise, there is a Python file to use as a starter template: `myrouter.py`. This file contains the outline of a Router class, and currently contains a constructor \(`__init__`\) method and a `router_main` method. This is just a starter template: you can refactor and redesign the code in any way you like.

The main task for this exercise is to modify the Router class to do the following:

{% hint style="info" %}
The sentences marked with ✅ are related to the content of your report. Please pay attention.
{% endhint %}

### Task 1: Preparation

Initiate your project with our template.

[Start the task here](preparation.md)

### Task 2: Handle ARP Requests

Ready to make ARP work.

[Start the task here](handle-arp-request.md)

### Task 3: Cached ARP Table

Maintain a correlation between each MAC address and its corresponding IP address.

[Start the task here](arp-table.md)

## Handing it in

Create a directory named `report/` in your repository and place your report, capture files and other materials in it.

### Report

We will provide a template of your lab assignment report [here](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/). You need to submit the report in your repository named `<student ID><name>_lab_3.pdf`. The format of your report should be PDF. An example is `123456789拾佰仟_lab_3.pdf`.

### Capture file

The capture file's name should be `lab_3.pcapng` or `lab_3.pcap`.

### Submit to GitHub Classroom

Finally, the directory should be in this structure:

```text
.
├── README.md
├── myrouter.py
├── report
│   ├── 123456789拾佰仟_lab_3.pdf
│   └── lab_3.pcap
├── start_mininet.py
└── testcases
    ├── routertests1.srpy
    └── routertests1full.srpy
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

