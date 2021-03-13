# Switchyard

Switchyard is a framework for creating, testing, and experimenting with software implementations of networked systems such as Ethernet switches, IP routers, firewalls and middleboxes, and end-host protocol stacks. It is the framework targeting on teaching and used in University of Wisconsin.

So we have this framework to do some network testing without multiple devices and a bunch of wires. [Switchyard documentation](https://pavinberg.gitee.io/switchyard/) is available here. And this is the document you read most often. In this section we will combine Mininet, Wireshark and Switchyard together.

We expect that you have complete [How to use Mininet](mininet.md) and [How to use Wireshark](wireshark.md). Next is to learn how to program in Switchyard. We will show you a hub, just forward any input packets to any other interfaces. There are three files for this section.

* `examples/start_mininet.py`
* `examples/myhub.py`
* `examples/myhub_testscenario.py`

The [Switchyard documentation](https://pavinberg.gitee.io/switchyard/) also uses these files to show many useful APIs. Again, this document is very important. You need to read it whenever you get confused with the APIs or Switchyard itself. In this section we do not show you the APIs but the workflow and a little code explanation.

We expect that it will take you up to 4 days on this. It may be a little bit tricky. But this is important.

## Install Switchyard

You can find instructions [here](https://gitee.com/pavinberg/switchyard), the repository of Switchyard on Gitee. A quick note here for Ubuntu. Notice that Switchyard needs python 3.6+ .

If you downloaded the Ubuntu VM image from [NJU box](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/), you can **skip** the first two steps.

1. Get the source code of Switchyard in your home directory \(or anywhere you like\) by input the commad below which will create a directory named `switchyard`

   ```text
   $ git clone https://gitee.com/pavinberg/switchyard.git
   ```

   This source code of Switchyard is just for your practise. We actually don't need you to use the source code here nor use the framework.

2. Get some dependent softwares and libraries

   ```text
   $ sudo apt-get install libffi-dev libpcap-dev python3-dev python3-pip
   ```

3. Install Switchyard and the necessary related packages in an isolated Python virtual environment \("venv"\), which is the recommended path, or in the system directories, which is often less desirable. The venv route is highly suggested, since it makes all installation "local" and can easily destroyed, cleaned up, and recreated.  
   To **create** a new virtual environment, you could do something like the following under your workspace folder `switchyard`.

   ```bash
   $ python3 -m venv syenv
   ```

4. After this command, you will find a folder `syenv` in `switchyard`, which is the folder of the Python virtual environment. You can change the name `syenv` to whatever you'd like to name your virtual environment. Next, you need to **activate** the environment. The instructions vary depending on the shell you're using. On `bash`, the command is

   ```bash
   $ source ./syenv/bin/activate
   ```

   Exactly, `activate` is a runnable file in the folder `syenv`. You'll need to replace `syenv` with whatever you named the virtual environment. If you're using a different shell than bash, refer to Python documentation on the venv module.

5. Finally, **install Switchyard**. All the required additional libraries should be automatically installed, too. _\*\*_

   ```bash
   $ python3 -m pip install git+https://gitee.com/pavinberg/switchyard.git
   ```

6. At last, we suggest to exclude your virtual environment out of git tracking. Add this line in `.gitignore` if there is not.

   ```text
   syenv/
   ```

## Prepare Your Test Script

Writing tests to determine whether a piece of code behaves as expected is an important part of the software development process. With Switchyard, it is possible to create a set of tests that verify whether a program attempts to receive packets when it should and sends the right packet\(s\) out the right ports.

A test scenario is Switchyard’s term for a series of tests that verify a program’s behavior. A test scenario is simply a Python source code file that includes a particular variable name \(symbol\) called `scenario`, which must refer to an instance of the class `TestScenario`. A `TestScenario` object contains the basic configuration for an imaginary network device along with an ordered series of _test expectations_. These expectations may be one of three types:

* that a particular packet should arrive on a particular interface/port,
* that a particular packet should be emitted out one or more ports, and
* that the user program should time out when calling recv\_packet because no packets are available.

To start off, here is an example of an empty test scenario:

```python
from switchyard.lib.userlib import *

scenario = TestScenario("test example")
```

For more about writing tests, you need to read [Test Scenario Creation](https://pavinberg.gitee.io/switchyard/test_scenario_creation.html).

In later lab assignments, you need to construct test script yourself. But this time we have a template test script helps you. Here is the test script `examples/myhub_testscenario.py` for our hub.

```python
#!/usr/bin/env python3

from switchyard.lib.userlib import *


def new_packet(hwsrc, hwdst, ipsrc, ipdst, reply=False):
    ether = Ethernet(src=hwsrc, dst=hwdst, ethertype=EtherType.IP)
    ippkt = IPv4(src=ipsrc, dst=ipdst, protocol=IPProtocol.ICMP, ttl=32)
    icmppkt = ICMP()
    if reply:
        icmppkt.icmptype = ICMPType.EchoReply
    else:
        icmppkt.icmptype = ICMPType.EchoRequest
    return ether + ippkt + icmppkt

def test_hub():
    s = TestScenario("hub tests")
    s.add_interface('eth0', '10:00:00:00:00:01')
    s.add_interface('eth1', '10:00:00:00:00:02')
    s.add_interface('eth2', '10:00:00:00:00:03')

    # test case 1: a frame with broadcast destination should get sent out
    # all ports except ingress
    testpkt = new_packet(
        "30:00:00:00:00:02",
        "ff:ff:ff:ff:ff:ff",
        "172.16.42.2",
        "255.255.255.255"
    )
    s.expect(
        PacketInputEvent("eth1", testpkt, display=Ethernet),
        ("An Ethernet frame with a broadcast destination address "
         "should arrive on eth1")
    )
    s.expect(
        PacketOutputEvent("eth0", testpkt, "eth2", testpkt, display=Ethernet),
        ("The Ethernet frame with a broadcast destination address should be "
         "forwarded out ports eth0 and eth2")
    )

    # test case 2: a frame with any unicast address except one assigned to hub
    # interface should be sent out all ports except ingress
    reqpkt = new_packet(
        "20:00:00:00:00:01",
        "30:00:00:00:00:02",
        '192.168.1.100',
        '172.16.42.2'
    )
    s.expect(
        PacketInputEvent("eth0", reqpkt, display=Ethernet),
        ("An Ethernet frame from 20:00:00:00:00:01 to 30:00:00:00:00:02 "
         "should arrive on eth0")
    )
    s.expect(
        PacketOutputEvent("eth1", reqpkt, "eth2", reqpkt, display=Ethernet),
        ("Ethernet frame destined for 30:00:00:00:00:02 should be flooded out"
         " eth1 and eth2")
    )


    resppkt = new_packet(
        "30:00:00:00:00:02",
        "20:00:00:00:00:01",
        '172.16.42.2',
        '192.168.1.100',
        reply=True
    )
    s.expect(
        PacketInputEvent("eth1", resppkt, display=Ethernet),
        ("An Ethernet frame from 30:00:00:00:00:02 to 20:00:00:00:00:01 "
         "should arrive on eth1")
    )
    s.expect(
        PacketOutputEvent("eth0", resppkt, "eth2", resppkt, display=Ethernet),
        ("Ethernet frame destined to 20:00:00:00:00:01 should be flooded out"
         "eth0 and eth2")
    )

    # test case 3: a frame with dest address of one of the interfaces should
    # result in nothing happening
    reqpkt = new_packet(
        "20:00:00:00:00:01",
        "10:00:00:00:00:03",
        '192.168.1.100',
        '172.16.42.2'
    )
    s.expect(
        PacketInputEvent("eth2", reqpkt, display=Ethernet),
        ("An Ethernet frame should arrive on eth2 with destination address "
         "the same as eth2's MAC address")
    )
    s.expect(
        PacketInputTimeoutEvent(1.0),
        ("The hub should not do anything in response to a frame arriving with"
         " a destination address referring to the hub itself.")
    )
    return s


scenario = test_hub()
```

The function `new_packet()` is used to make a packet. For now you don't need to know what the parameters means.

The function `test_hub()`returns a `TestScenario` object which assigned to `scenario`. The variable `scenario` is very important in the test framework of Switchyard, do not forget it. We construct the scenario with a name "hub tests". Then we add three interfaces with name and MAC address on our hub. In every case, we make a packet then feed it into one interface of our hub with `PacketInputEvent`. Then we compare the outgoing packets from interfaces of our hub with our expectation packets using `PacketOutputEvent`. If there is no packet out, we use the function `PacketInputTimeoutEvent` to check there is no traffic for a period of time.

All test APIs used is introduced [here](https://pavinberg.gitee.io/switchyard/test_scenario_creation.html).

You may want to run this test, we will cover this later.

## Implement your device

Switchyard is the framework enables you to implement a device. A Switchyard program is simply a Python program that includes a particular entry point function which accepts a single parameter. The startup function can simply be named `main`, but can also be named `switchy_main` if you like. The function must accept at least one parameter, which is a reference to the Switchyard _network object_ \(described below\). Method calls on the network object are used to send and receive packets to and from network ports.

A Switchyard program isn’t executed _directly_ with the Python interpreter. Instead, the program `swyard` is used to start up the Switchyard framework and to load your code. When Switchyard starts your code it looks for a function named `main` and invokes it, passing in the network object as the first parameter. Details on how to start Switchyard \(and thus your program\) are given in the chapters on [running a Switchyard in the test environment](https://pavinberg.gitee.io/switchyard/test_execution.html#runtest) and [running Switchyard in a live environment](https://pavinberg.gitee.io/switchyard/live_execution.html#runlive). Note that it is possible to pass arguments into a Switchyard program; see [Passing arguments into a Switchyard program](https://pavinberg.gitee.io/switchyard/writing_a_program.html#swyardargs) for details.

A Switchyard program will typically also import other Switchyard modules such as modules for parsing and constructing packets, dealing with network addresses, and other functions. These modules are introduced below and described in detail in the [API reference chapter](https://pavinberg.gitee.io/switchyard/reference.html#apiref).

In the later lab assignments, you need to implement your device. Generally, your initial test files and device logic are not complete. You need to modify them step by step. This programming mode is called Test Driven Development \(TDD\). Here is our hub code `examples/myhub.py`.

```python
#!/usr/bin/env python3

'''
Ethernet hub in Switchyard.
'''
import switchyard
from switchyard.lib.userlib import *


def main(net: switchyard.llnetbase.LLNetBase):
    my_interfaces = net.interfaces()
    mymacs = [intf.ethaddr for intf in my_interfaces]

    while True:
        try:
            _, fromIface, packet = net.recv_packet()
        except NoPackets:
            continue
        except Shutdown:
            break

        log_debug (f"In {net.name} received packet {packet} on {fromIface}")
        eth = packet.get_header(Ethernet)
        if eth is None:
            log_info("Received a non-Ethernet packet?!")
            return
        if eth.dst in mymacs:
            log_info("Received a packet intended for me")
        else:
            for intf in my_interfaces:
                if fromIface!= intf.name:
                    log_info (f"Flooding packet {packet} to {intf.name}")
                    net.send_packet(intf, packet)

    net.shutdown()
```

In Switchyard, the device you want to be the hub will run this script and act like a hub by receiving any packets and forwarding to any other interfaces except the packets towards the hub itself. The APIs used in this file is introduced [here](https://pavinberg.gitee.io/switchyard/writing_a_program.html).

## Running in the Test Environment

{% hint style="info" %}
You need to activate your Python virtual environment first in any case you want to run Switchyard. This step is very important. In the root dictionary of Switchyard, run

```text
$ source ./syenv/bin/activate
```
{% endhint %}

You can test your hub code with your test file in Switchyard test mode. At minimum you would invoke `swyard` as follows.

```bash
$ swyard -t examples/myhub_testscenario.py examples/myhub.py
```

Note that the `-t` option puts swyard in test mode. The argument to the `-t` option should be the name of the test scenario to be executed, and the final argument is the name of your code.

After that, you will get some output shows if your tests pass or fail.

More about test environment and some debug methods are introduced [here](https://pavinberg.gitee.io/switchyard/test_execution.html).

## Running in the Mininet

In the test environment, here is no _true_ traffic here. The device only take the packets we provided. We need to challenge the true networking. Here we have Mininet can construct a network.

First let's start our topology we provided at `examples/start_mininet.py`.

```bash
$ sudo python examples/start_mininet.py
```

Then run your hub code to the device you what. Here must be the root of our star shape topology named `hub`. It is better to open xterm on it so you can see the output of it.

```text
mininet> xterm hub
```

Then run your hub code on it. Remember activate your Python virtual environment first. Replace `<Switchyard folder path>` to the path of Switchyard.

```bash
# source <Switchyard folder path>/syenv/bin/activate
# swyard examples/myhub.py
... here is your hub logs ...
```

Now you have your topology ready and your hub running, let's see if it works. In Mininet CLI, type `pingall` and return.

```text
mininet> pingall
*** Ping: testing ping reachability
client -> X server1 server2 
hub -> X X X 
server1 -> client X server2 
server2 -> client X server1 
*** Results: 50% dropped (6/12 received)
```

This is the output what you will see.

You are able to capture in Mininet too. In any host you want to capture packets, run wireshark on it. In our case, we run wireshark on the host named `client`. Then we ping `client` to `server1`.

```text
mininet> client wireshark &
mininet> client ping -c1 server1
```

![syward-wireshark](../../.gitbook/assets/swyard_wireshark.png)

