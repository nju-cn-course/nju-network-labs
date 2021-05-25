# Task 2: Middlebox

## Details

Even though it's called a middlebox, in practice this will be a very basic version of the router you implemented in Lab 3-5. Your middlebox will have two ports each with a single connection: one to the blaster and one for the blastee. You will do the same packet header modifications\(i.e layer 2\) as in Lab 3-5. However instead of making ARP requests you will hard code the IP-MAC mappings into the middlebox code. This means, if the middlebox receives a packet from its eth0 interface\(= from blaster\), it will forward it out to eth1\(= to blastee\) and vice versa. This basic assumption also obviates the need to do forwarding table lookups. Regardless of the source IP address, just forward the packet to the other interface.

So far so good. Now comes the fun part of the middlebox! Besides a very dumb forwarding mechanism, your middlebox will also be in charge of probabilistically dropping packets to simulate all the evil things that can happen in a real network. Packet drops will only happen in one direction, from blaster to blastee \(i.e do not drop ACKs\).

### Parameters

The middlebox should be passed a parameter to setup the device:

* _droprate_: Percentage of packets \(non-ACK\) that your middlebox is going to drop, 0 ≤ _droprate_ ≤ 1

To use this parameter, the `main` function of the Switchyard program should pass another keyword argument, e.g.

```python
def main(net, **kwargs):
```

This syntax is [Python keyword argument](https://realpython.com/python-kwargs-and-args/#using-the-python-kwargs-variable-in-function-definitions) which can be used to pass multiple keyword arguments as a dicrtionary. 

When we run the program with `swyard` pass the parameter using the `-g` parameter:

```bash
$ swyard middlebox.py -g "dropRate=0.19"
```

Then the `kwargs` in the main function will be a dictionary with one pair:

```python
print(kwargs)
# {'dropRate': '0.19'}
```

So that we can use it in our program. Notice that the key and value of the dictionary are both `str` .

## Coding

Your task is to implement the logic described above. The start file is named `middlebox.py`.

✅ In the report, show how you implement the features of middlebox.

