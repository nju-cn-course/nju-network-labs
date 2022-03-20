# Lab 2: Learning Switch

## Overview

In this assignment, you are going to implement the core functionalities of an Ethernet learning switch using the [Switchyard framework](https://gitee.com/pavinberg/switchyard). An Ethernet switch is a layer 2 device that uses packet switching to receive, process and forward frames to other devices (end hosts, other switches) in the network. A switch has a set of interfaces (ports) through which it sends/receives Ethernet frames. When Ethernet frames arrive on any port, the switch process the header of the frame to obtain information about the destination host. If the switch knows that the host is reachable through one of its ports, it sends out the frame from the appropriate output port. If it does not know where the host is, it floods the frame out of all ports except the input port.

Lab-2 assignment in Github Classroom: [https://classroom.github.com/a/Ne3heelA](https://classroom.github.com/a/Ne3heelA)

## Details

Your task is to implement the logic of a switch. As it is described in the last paragraph of the "Ethernet Learning Switch Operation" section, your switch will also handle the frames that are intended for itself and the frames whose Ethernet destination address is the broadcast address `FF:FF:FF:FF:FF:FF`.

In addition to these, you will also implement three different mechanisms to purge the outdated/stale entries from the forwarding table. This will allow your learning switch to adapt to changes in the network topology.

These mechanisms are:

* Remove an entry from the forwarding table after 10 seconds have elapsed.
* Remove the least recently used (LRU) entry from the forwarding table. For this functionality assume that your table can only hold 5 entries at a time. If a new entry comes and your table is full, you will remove the entry that has not been matched with a Ethernet frame destination address for the longest time.
* Remove the entry that has the least traffic volume. For this functionality assume that your table can only hold 5 entries at a time. Traffic volume for an entry is the number of frames that the switch received where `Destination MAC address == MAC address of entry`.

You will implement these mechanisms in three separate Python files. The core functionalities that are explained above will be the same for these implementations.

{% hint style="warning" %}
Please carefully read the [FAQ](faq.md) section, for more specific details regarding the implementations
{% endhint %}

## Your Tasks

In these tasks, you will write the code to implement the core logic in an Ethernet learning switch using the Switchyard framework. Besides using Switchyard for developing and testing your switch, you can deploy it in Mininet to test it in a "live" setting. The code you'll need to add for the simplest version of this exercise should be less than 20 lines (and possibly quite a bit less depending on exactly how you write the code). There are extensions to the basic learning switch that could add quite a bit more code.

{% hint style="warning" %}
The sentences marked with ✅ are related to the content of your report. Please pay attention.
{% endhint %}

### Task 1: Preparation

Initiate your project with our template.

[Start the task here](preparation.md)

### Task 2: Basic Learning Switch

Start with the basic learning switch.

[Start the task here](basic-switch.md)

### Task 3: Timeouts

Implement timeouts based on the previous task.

[Start the task here](timeouts.md)

### Task 4: LRU Rule Replacement Algorithm

Implement LRU rule replacement algorithm based on Task 2.

[Start the task here](lru.md)

### Task 5: Least Traffic Volume Rule Replacement Algorithm

Implement least traffic volume rule replacement algorithm based on Task 2.

[Start the task here](ltv.md)

## Handing it in

Create a directory named `report/` in your repository and place your report, capture files and other materials in it.

### Report

We will provide a template of your lab assignment report [here](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/). You need to submit the report in your repository named `<student ID><name>_lab_2.pdf`. The format of your report should be PDF. An example is `123456789拾佰仟_lab_2.pdf`.

### Capture file

The capture file's name should start with "lab\_2", e.g. `lab_2_lru.pcapng` or `lab_2_lru.pcap`.

### Submit to GitHub Classroom

Finally, the directory should be in this structure:

```
.
├── README.md
├── myswitch.py
├── myswitch_lru.py
├── myswitch_to.py
├── myswitch_traffic.py
├── report
│   ├── 123456789拾佰仟_lab_2.pdf
│   ├── lab_2.pcap
│   └── ...
├── start_mininet.py
└── testcases
    ├── myswitch_lru_testscenario.srpy
    ├── myswitch_to_testscenario.srpy
    ├── myswitch_traffic_testscenario.srpy
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
