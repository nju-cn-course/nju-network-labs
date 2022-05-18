# Lab 7: Content Delivery Network

## Overview

In this lab, you are going to build a demo of **Content Delivery Network (CDN)**. A CDN is a geographically distributed network of proxy servers and their data centers. The goal is to provide high availability and performance by distributing the service spatially relative to end users.

To minimize the distance between the visitors and your website’s server, a CDN stores a cached version of its content in multiple geographical locations (a.k.a., points of presence, or PoPs). Each PoP contains a number of caching servers responsible for content delivery to visitors within its proximity.

As a feature of this lab, you will use [OpenNetLab](https://opennetlab.org/) for deployment. OpenNetLab is a networking platform running in WAN built by Microsoft Research Lab – Asia in cooperation with research group of [Associate Professor Chen Tian](https://cs.nju.edu.cn/tianchen/). That is to say, you can deploy your assginment in real WAN now!

Lab-7 assignment in Github Classroom: ([https://classroom.github.com/a/gTPr5R6S](https://classroom.github.com/a/gTPr5R6S))

## Your Tasks

There are two keys in CDN:

1. The caching server that stores the content.
2. The DNS server that finds the closest caching server for you.

Your tasks in this lab is to implement the both.

{% hint style="warning" %}
One thing you should be aware is that you are **NOT** going to use Switchyard in this lab, because here we are at the application layer which is not the place for Switchyard. Instead, we simply use the modules of Python and some third-party packages to finish the lab.
{% endhint %}

{% hint style="danger" %}
You shoud **NOT** write anything to disks in this lab since this may cause damage to the platform servers.
{% endhint %}

{% hint style="info" %}
The sentences marked with ✅ are related to the content of your report. Please pay attention.
{% endhint %}

### Task 1: Preparation

Initiate your project with our template.

[Start the task here](preparation.md)

### Task 2: DNS server

Implement the DNS server.

[Start the task here](dns-server.md)

### Task 3: Caching server

Implement the caching server.

[Start the task here](caching-server.md)

### Task 4: Deployment

Deploy the code to OpenNetLab.

[Start the task here](deployment.md)

## Handing in

Create a directory named `report/` in your repository and place your report, log files and other materials in it.

### Report‌

We will provide a template of your lab assignment report [here](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/). You need to submit the report in your repository named `<student ID><name>_lab_7.pdf`. The format of your report should be PDF. An example is `123456789拾佰仟_lab_7.pdf`.

### Log file

Hand in the log of DNS server, caching server and client.

### Submit to GitHub Classroom

The directory structure should be as follows:

```
.
├── README.md
├── cachingServer
│   ├── cacheTable.py
│   └── cachingServer.py
├── dnsServer
│   ├── dns_server.py
│   └── dns_table.txt
├── mainServer
│   ├── doc
│   │   ├── index.html
│   │   └── success.jpg
│   └── mainServer.py
├── report
│   ├── 123456789拾佰仟_lab_7.pdf
│   ├── 123456789_cache.log
│   ├── 123456789_client.log
│   └── 123456789_dns.log
├── requirements.txt
├── runCachingServer.py
├── runDNSServer.py
├── test_entry.py
├── testcases
│   ├── baseTestcase.py
│   ├── test_all.py
│   ├── test_cache.py
│   └── test_dns.py
└── utils
  ├── dns_utils.py
  ├── ip_utils.py
  ├── manageservice.py
  ├── network.py
  ├── rpcServer.py
  └── tracer.py
```

Commit the change.

{% hint style="warning" %}
Only commit your **source code** to your local repository. If there are some generated files that are not source code, ignore them by adding them in the file `.gitignore`.
{% endhint %}

After you’ve committed you final codes and report, push the repository to GitHub by inputing command:

```
$ git push
```

After a few seconds, you can see the changes on your repository web page, which means you have handed in successfully.

### Check locally

Run the `testcases/test_submit.py` file. "OK" means you've passed all the test.

```
$ python3 testcases/test_submit.py
..
----------------------------------------------------------------------
Ran 2 tests in 0.002s
​
​
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
​
​
----------------------------------------------------------------------
Ran 2 tests in 0.002s
​
​
FAILED (failures=1)
```

The message "Wrong name of report PDF" indicates that your report PDF isn't named correctly.
