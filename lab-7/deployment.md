# Task 4: Deployment

## Test all

Before the deployment, run test `all` to test DNS server and caching server at the same time:

```
$ python3 test_entry.py all
2021/05/17-21:34:22| [INFO] DNS server started
2021/05/17-21:34:22| [INFO] Main server started
2021/05/17-21:34:22| [INFO] RPC server started
2021/05/17-21:34:22| [INFO] Caching server started
test_01_cache_missed_1 (testcases.test_all.TestAll) ...
[Request time] 9.48 ms
ok
test_02_cache_hit_1 (testcases.test_all.TestAll) ...
[Request time] 4.94 ms
ok
test_03_not_found (testcases.test_all.TestAll) ...
[Request time] 6.02 ms
ok
​
----------------------------------------------------------------------
Ran 3 tests in 1.568s
​
OK
2021/05/17-21:34:25| [INFO] DNS server terminated
2021/05/17-21:34:25| [INFO] Caching server terminated
2021/05/17-21:34:25| [INFO] PRC server terminated
2021/05/17-21:34:25| [INFO] Main server terminated
```

Make sure you have passed all testcases (i.e. `dns`, `cache` and `all`). Please do **NOT** submit wrong code to OpenNetLab.

Besides, make sure that you merely modified `dnsServer/dns_server.py` and `cachingServer/cachingServer.py`.

## Deploy in the NJU Campus LAN

If you are in the campus network, your computer will be assigned an IP address that can be visited in the campus network. So that when you start one of the servers, others can visit your server as long as they know your IP address.

So you can actually deploy your own CDN. For example, you can find 2 friends that run their caching servers, which point to a main server launched by another friend (or any other server on the Internet), on their laptops and get their IP addresses in the campus network (e.g. 114.212.x.x). Then, you can change the A record in the `dns_table.txt` to match their IP addresses. Then, run your DNS server at localhost port 53 with `sudo python3 runDNSServer.py 53`, and change your DNS server to 127.0.0.1 in your network settings.&#x20;

If you finished these settings, then in your web browser, you can type the corresponding domain name in the `dns_table.txt`. Your browser will first initiate an DNS request to your local DNS server. Your local DNS server will then return an A record pointed to one of your friends' caching server. The caching server will response with the content of the main server, which will appear in your browser. You four make up a real CDN!

## ~~OpenNetLab~~ (Not Available Now)

The OpenNetLab has several servers in multiple locations around the continent. The furthest one, which is the main server, is located in a foreign country which causes a big round trip time (RTT) of one request. As a result, our caching server should be a great improvement on the RTT.

To submit your code on OpenNetLab, do as follows:

1. Open the [OpenNetLab for NJU](http://114.212.82.205:5001) (Notice that you need the NJU campus network to visit it). Log in with your student ID and the password we sent to you by email.
2. Click “Create New” to create a deployment.
3. Upload your`dnsServer/dns_server.py` and `cachingServer.cachingServer.py`.
4. Check the result and download log files.

## ~~Get the Results~~ (Not Available Now)

The client’s log file has “\[Request time]” logged, which indicates how much time it needs for the client to finish the request. You can compare the request that “fetched from main server” and request that “hits cache” to show the improvement of our CDN.

✅ In the report, show how much the CDN cache shortens the request time. Write the procedure and analysis in your report with screenshots.

