# Task 2: DNS server

## Introduction

The DNS server is the entry point to the CDN and is responsible for load balancing and intelligent DNS resolution. When a user accesses a remote resource, it is usually through a domain name rather than a direct IP address, and the user accesses the DNS service to convert the domain name into an IP address before setup a TCP connection. In order to provide better quality of service, the DNS server needs to reply the IP of the optimal target server or CDN Cache node based on the relative location of the client. The common scenario of CDN is as follows:

![](../.gitbook/assets/CDN\_structure.png)

1. The client wants to access a file on the Source Server, but does not know the IP address corresponding to the domain name `www.example.com`, so client send a DNS request to local DNS server.
2. The Local DNS Server does not know the address of the server corresponding to the domain name, and through layer-by-layer resolution, it gets a response from the Remote DNS Server.
3. The Remote DNS Server does not return the IP address of the source server directly, but instead returns a **CNAME record**. A CNAME record is a mapping of one domain name to another, (e.g., `www.example.com` -> `storage1.mycdn.com` ). The CNAME records instruct the client (or local dns server) to continue resolving the domain address obtained from the CNAME mapping, with the aim of directing the client's request to CDN nodes.
4. The Local DNS Server gets a CNAME response from Remote DNS Server and continues to resolve the new domain name address, eventually getting a response from the CDN's DNS Server.
5. The CDN DNS Server returns the IP address of the optimal CDN Cache Node based on the client's IP address by taking geographic location and server load into account.
6. Local DNS returns the final IP address to the client and records it locally.

## Your tasks

In this experiment, You need to implement the functions of Remote DNS Server and CDN DNS Server.

* Remote DNS Server：Responsible for receiving requests from the client/Local DNS Server and returning a CNAME record.
* CDN DNS Server：You need to obtain a list of IP addresses of Cache nodes that can be served and select the best IP address to reply to the client/Local DNS Server based on its geographical location.

However, most functions of the two DNS servers are similar. **To avoid experimental code redundancy, you will only need to write one DNS server's code and implement the functions of both Remote DNS Server and CDN DNS Server. Our experimental framework will deploy your code in separate locations.**

### Step 1 : Load DNS Records Table

You will need to load the DNS table from a text file, converting the table records into the python data structure you are familiar with. In general, the table structure we provided is as follows:

```
*.netlab.nju.edu.cn. CNAME nju.storage1.opennetlab.com.
test.nasa.nju.edu.cn. CNAME nju.storage2.opennetlab.com.
*.storage1.opennetlab.com. A 10.0.0.1 10.0.0.2 10.0.0.3
*.storage2.opennetlab.com. A 10.0.0.4 10.0.0.5
*.storage3.opennetlab.com. A 10.0.0.7
```

Each row represents a table record and is divided into three parts:

* **Domain Name:** the domain name for which the user wants to request an ip address, you should note that the domain name records in this experiment may carry a wildcard mask, e.g. `*.netlab.nju.edu.cn.` means that any sub-domain under `netlab.nju.edu.cn` can be matched to that record (the `.` at the last represents the root domain name, although we usually omit it).
* **Record Type:** there are many record types in DNS protocol, for this experiment we will use only two DNS Record types, which are:
  * A ：Returns directly the IP address (or list of addresses) corresponding to the domain name.
  * CNAME：Mapping a domain name to another domain name tells the client that it needs to continue resolving that domain name.
* **Record Values:** The A or CNAME records returned to the client, possibly as a list.

You will need to parse the dns\_table.txt file line by line and convert the records into a suitable data structure for subsequent lookups.

```
def parse_dns_file(self, dns_file):
 # ---------------------------------------------------
 # TODO: your codes here. Parse the records file 
 #       and load the data into self._dns_table.
 self._dns_table = None
​
 # ---------------------------------------------------
```

### Step 2 : Reply Clients' DNS Request

With the DNS Table loaded, you will need to give the client response. For this experiment, we have encapsulated the DNS-related interface and you do not need to care about the specifics of the DNS protocol. You need to implement the following functions:

```
def get_response(self, request_domain_name):
 response_type, response_val = (None, None)
 # ------------------------------------------------
 # Task2 TODO: your codes here.
 # Determine an IP to response according to the client's IP address.
 #       set "response_ip" to "the best IP address".
 client_ip, _ = self.client_address
​
​
 # ------------------------------------------------
 return (response_type, response_val)
```

**This function provides :**

* request\_domain\_name：the domain requested by the user.
* client\_ip : the IP address of the user.

**You need to return :**

* response\_type：Indicates the type of record matched, expressed in string format as "CNAME" and "A".
* response\_val：Indicates the records value, e.g., "10.0.0.1" or "nju.storage1.opennetlab.com".

**You need to consider the following scenarios：**

* The domain name requested by the user can not find the corresponding record in the table, in which case you need to return `(response_type, response_val)` with the value `(None, None)`.
* The domain name requested by the user is found in the table as a CNAME type record, in which case you need to return ("CNAME", "xxx.xxx.xxx") directly.
* The domain name requested by the user is found in the table with a list of records of type A.
  * If there is only one record in the list, return ("A", that IP address )  directly.
  * If there are multiple records in the list. You need to consider the geographical relative location of the client IP and the server IP. You can use `IP_Utils.getIpLocation(ip_str)` to get the latitude and longitude information for an IP address.
    * If it cannot find the location information for the client's IP address in our database (i.e., `IP_Utils.getIpLocation(ip_str)` returns None), you will need to adopt a random load balance policy for multiple servers.
    * If you find the location of the client's IP address, you will need to select the nearest Cache Node (CDN Node) from the list of records as the response\_val.

Once you have completed the above, your dns server can act as both CDN DNS Server and Remote DNS Server as your code implements all the functions required for the experiment.

✅ In the report, show how you implement the features of DNS server.

## Test your code

### Manually

To start the dns server, input:

```
$ python3 runDNSServer.py
DNS server serving on 0.0.0.0:9999
```

This will start the DNS server at every IP of the interfaces of your computer with port `9999`. You can access it with`localhost:9999`.  If you want a customized port number or so, you can input `-h` parameter for more usage:

```python
$ python3 runDNSServer.py -h                                                     146 ↵
usage: runDNSServer.py [-h] [port] [file]

Run DNS server

positional arguments:
  port        port to start the dns service (default: 9999)
  file        file used to load dns table (default: ./dnsServer/dns_table.txt)

optional arguments:
  -h, --help  show this help message and exit
```

Now you can use the function we provide to test if your DNS server runs correctly:

```python
from utils.network import resolve_domain_name
​
dnsIP, dnsPort = "localhost", 9999
​
def resolve(domain):
    resp = resolve_domain_name(domain, dnsIP, dnsPort)
    if resp:
        return resp.response_val
    return None
​
result = resolve("home.nasa.org")
print(result)
```

Run the Python code in another terminal window. This will send an DNS request to our DNS server at `localhost:9999` to resolve the domain name `home.nasa.org` and print out the result `10.0.0.1`.

{% hint style="info" %}
&#x20;`localhost` is just an alias name of IP `127.0.0.1` , which is a special IP address called "loopback". It means that when you visit this IP, you are actually visit your computer itself. This is especially useful when you are testing a web application locally, just like our DNS server that runs locally.&#x20;
{% endhint %}

### Testcases

A testcase is provided to individually check if the DNS server's logic is correct. In the project’s directory:

```
$ python3 test_entry.py dns
2021/05/17-19:36:05| [INFO] DNS server started
test_cname1 (testcases.test_dns.TestDNS) ... ok
test_cname2 (testcases.test_dns.TestDNS) ... ok
test_location1 (testcases.test_dns.TestDNS) ... ok
test_location2 (testcases.test_dns.TestDNS) ... ok
test_non_exist (testcases.test_dns.TestDNS) ... ok
​
----------------------------------------------------------------------
Ran 5 tests in 0.010s
​
OK
2021/05/17-19:36:06| [INFO] DNS server terminated
```

If the above result is returned, the basic test passes. If the following result is returned, it means that some testcases fail.

```
----------------------------------------------------------------------
Ran 5 tests in 0.009s
​
FAILED (failures=1)
```

{% hint style="info" %}
The testcases are run by Python unittest module. This will suppress the standard output of your program (which means your print will take no effect). If you must see the stdout, test manually or write the messages to a file instead.
{% endhint %}
