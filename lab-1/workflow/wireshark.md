# Wireshark

Wireshark is the world’s foremost and widely-used network protocol analyzer. It lets you see what’s happening on your network at a microscopic level and is the de facto (and often de jure) standard across many commercial and non-profit enterprises, government agencies, and educational institutions.

You will use it to inspect your network setting up by Mininet, and test the function of your device written in Switchyard. We also have a small practice of Wireshark in our manual.

[Wireshark User’s Guide](https://www.wireshark.org/docs/wsug\_html/) is a verbose document about Wireshark so we **DO NOT** recommend it. Sometimes the official document is hard for newcomers to get started. You can find many blogs with Google about how to use Wireshark like [this one](https://www.howtogeek.com/104278/how-to-use-wireshark-to-capture-filter-and-inspect-packets/). Read them instead.

We expect that this will cost you an afternoon.

## Install Wireshark

Wireshark has also been bundled in the VM we provided. For custom environments, the installation guide is [here](../../appendix/environment-setup.md#install-wireshark).

## Capturing Packets

First start the default Mininet topology:

```
$ sudo mn
```

Then open wireshark in `h1`.

```
mininet> h1 wireshark &
```

![wireshark-window](../../.gitbook/assets/wireshark\_0.png)

You need to choose which traffic you want to capture. Packets will send and receive on `h1-eth0` so you double click it.

It will be empty or with some ICMPv6 packets. Let's create some traffic ourselves.

```
mininet> h1 ping -c 1 h2
```

![wireshark-window](../../.gitbook/assets/wireshark\_1.png)

So here we have more packets. You may not know why these packets show up, but you will learn in the next few lessons. Now let's filter some packets by typing protocol name on the filter text box.

![wireshark-window](../../.gitbook/assets/wireshark\_2.png)

You can also check the field in the packets like ethernet source MAC address. The value will be highlighted both in packet details and packet raw bytes.

![wireshark-window](../../.gitbook/assets/wireshark\_3.png)
