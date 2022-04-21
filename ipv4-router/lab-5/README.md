# Lab 5: Respond to ICMP

## Overview

This is the third stage in a series of exercises that have the ultimate goal of creating an IPv4 router. The basic functions of an Internet router are to:

1.  Respond to ARP (address resolution protocol) requests for addresses

    that are assigned to interfaces on the router. (Remember that the

    purpose of ARP is to obtain the Ethernet MAC address associated with

    an IP address so that an Ethernet frame can be sent to another host

    over the link layer.)
2.  Receive and forward packets that arrive on links and are destined to

    other hosts. Part of the forwarding process is to perform address

    lookups ("longest prefix match" lookups) in the forwarding table. We

    will just use "static" routing in our router rather than implement a

    dynamic routing protocol like RIP or OSPF.
3.  Make ARP requests for IP addresses that have no known Ethernet MAC

    address. A router will often have to send packets to other hosts,

    and needs Ethernet MAC addresses to do so.
4. Respond to ICMP messages like echo requests ("pings").
5.  Generate ICMP error messages when necessary, such as when an IP

    packet's TTL (time to live) value has been decremented to zero.

The goal of this stage of the project is to accomplish items **#4** and **#5** above. When you're done with this project, you will have a fully functioning Internet router.

Lab-5 assignment in Github Classroom: [https://classroom.github.com/a/rB5\_omWs](https://classroom.github.com/a/rB5\_omWs)

## Your Tasks

After the efforts of Lab 3 and Lab 4, you have implemented part of the functions of the router, including the response to ARP and the forwarding of packets. Next, you need to continue to improve your router in the `myrouter.py` so that it can respond to ICMP messages.

The main task for this exercise is to modify the Router class to do the following:

{% hint style="info" %}
The sentences marked with ✅ are related to the content of your report. Please pay attention.
{% endhint %}

### Task 1: Preparation

Initiate your project with our template.

[Start the task here](preparation.md)

### Task 2: Responding to ICMP echo requests

Respond to ICMP messages like echo requests ("pings").

[Start the task here](respond-icmp.md)

### Task 3: Generating ICMP error messages

Generate ICMP error messages when necessary.

[Start the task here](generate-error-messages.md)

## Handing it in

Create a directory named `report/` in your repository and place your report, capture files and other materials in it.

### Report

We will provide a template of your lab assignment report [here](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/). You need to submit the report in your repository named `<student ID><name>_lab_5.pdf`. The format of your report should be PDF. An example is `123456789拾佰仟_lab_5.pdf`.

### Capture file

The capture file's name should start with "lab\_5", e.g. `lab_5.pcapng` or `lab_5.pcap`.

### Submit to GitHub Classroom

Finally, the directory should be in this structure:

```
.
├── README.md
├── myrouter.py
├── report
│   ├── 123456789拾佰仟_lab_5.pdf
│   ├── lab_5.pcap
│   └── ...
├── start_mininet.py
└── testcases
    ├── myrouter3_testscenario.srpy
    ├── myrouter3__testscenario_template.py
    └── test_submit.py
```

Commit the change.

{% hint style="warning" %}
**Only** commit your **source code** to your local repository. If there are some generated files that are not source code, ignore them by adding them in the file `.gitignore`.
{% endhint %}

After you’ve committed you final codes and report, push the repository to GitHub by inputing command:

```
$ git push
```

After a few seconds, you can see the changes on your repository web page, which means you have handed in successfully.

## Check your submission

We provide a test script `testcases/test_submit.py` for you to check your submission files' format.

### Check locally

Run the `testcases/test_submit.py` file. "OK" means you've passed all the test.

```
$ python3 testcases/test_submit.py
..
----------------------------------------------------------------------
Ran 2 tests in 0.002s

OK
```

For example, if your report file has a wrong name format, you will get a test failure as below.

```
$ python3 testcases/test_submit.py
F.
======================================================================
FAIL: test_check_report (__main__.TestDir)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "testcases/test_submit.py", line 22, in test_check_report
    self.assertTrue(
AssertionError: False is not true : Wrong name of report PDF

----------------------------------------------------------------------
Ran 2 tests in 0.002s

FAILED (failures=1)
```

The message "Wrong name of report PDF" indicates that your report PDF isn't named correctly.
