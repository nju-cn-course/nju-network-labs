# Task 3: Caching server

## Overview

Caching server is at the heart of CDN. Caching works by selectively storing website files on a CDN’s cache proxy servers, where they can be quickly accessed by website visitors browsing from a nearby location. It maintains a local cache table (e.g. a database) to store all the content cached.

In this section, we will implement a simple CDN caching server, which will be an approximate implementation of a real CDN caching server to reduce the difficulty. So don’t be surprised if you find out that there are differences between our CDN caching server and the real one.

## HTTP

Before start coding, you should know the basic flow of HTTP.

### URL

First you should understand the format of a URL. A URL is composed of different parts, some mandatory and others optional. The most important parts are highlighted on the URL below:

![](../.gitbook/assets/url\_all.png)

**Authority** includes both the _domain_ (e.g. `www.example.com`) and the _port_ (`80`), separated by a colon.

* The domain indicates which Web server is being requested. Usually this is a domain name, but an IP address may also be used (but this is rare as it is much less convenient).
* The port indicates the technical "gate" used to access the resources on the web server. It is usually omitted if the web server uses the standard ports of the HTTP protocol (80 for HTTP and 443 for HTTPS) to grant access to its resources. Otherwise it is mandatory.

**Path to file** `/path/to/myfile.html` is the path to the resource on the Web server. In the early days of the Web, a path like this represented a physical file location on the Web server. Nowadays, it is mostly an abstraction handled by Web servers without any physical reality.

Parameters and anchors will not be used in this lab, so you can skip them.

For details about URL, you can refer to [here](https://developer.mozilla.org/en-US/docs/Learn/Common\_questions/What\_is\_a\_URL).

### HTTP Flow

When a client wants to communicate with a server, it performs the following steps:

1. Open a TCP connection: The TCP connection is used to send a request, or several, and receive an answer. The client may open a new connection, reuse an existing connection, or open several TCP connections to the servers.
2.  Send an HTTP message: HTTP messages (before HTTP/2) are human-readable. For example:

    ```
    GET / HTTP/1.1
    Host: developer.mozilla.org
    Accept-Language: en-us
    ```

    The `GET` is one of the HTTP methods to make a request to server.
3.  Read the response sent by the server, such as:

    ```
    HTTP/1.1 200 OK
    Date: Fri, 01 Jan 2021 12:28:02 GMT
    Server: Apache
    Last-Modified: Tue, 01 Dec 2020 20:18:22 GMT
    Accept-Ranges: bytes
    Content-Length: 29769
    Content-Type: text/html
    ​
    <!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
    ```

    The number `200` in the first line is the HTTP status code meaning OK. Other codes can be found [here](https://en.wikipedia.org/wiki/List\_of\_HTTP\_status\_codes). You may find code like `404` familiar 🤭.

    Lines from the second line are **headers**. You may pay attention to `Content-Length` and `Content-Type`. `Content-Length` indicates how many bytes the body has and `Content-Type` indicates what media type the body is (e.g. `text/html` for a web page and `application/pdf` for a PDF file). More information about HTTP headeres can be found [here](https://en.wikipedia.org/wiki/List\_of\_HTTP\_header\_fields).
4. Close or reuse the connection for further requests.

## Code Introduction

### Caching Server

The logic of a caching server lies in directory `cachingServer/`. There are two files:

1. `cachingServer.py`: logic of a caching server. This is the file you should work on.
2. `cacheTable.py`: a data structure of a cacheTable. You shouldn't modify it.&#x20;

The file you need to focus is `cachingServer.py`. There are two classes: `CachingServer` and `CachingServerHttpHandler`.

*   `CachingServerHttpHandler`

    Subclass of [http.server.BaseHTTPRequestHandler](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler) that serves as a request handler of HTTP. When the client send a request, say an HTTP GET, the handler will at last receive the request and do something on it. The method that will be called is `do_GET()`. You can simply see it as the starting point of the flow.

    When a request arrives, the handler will automatically parse the URL and store the “_Path to file_” to `self.path` which you will use to locate the target content. You should response the client with some headers and content body corresponding to the `self.path`.

    &#x20;`do_HEAD()` is called when client sends a HTTP HEAD request. The difference between `do_GET()` and `do_HEAD()` is that `do_HEAD()` only needs to respond the headers without sending back the whole body.
*   `CachingServer`

    Subclass of [socketserver.TCPServer](https://docs.python.org/3.6/library/socketserver.html) that serves as a TCP server. The `socketserver.TCPServer` should be constructed with the server’s address and a `HTTPRequestHandler`. Our caching server, based on the TCP server, has more functionalities. It needs to know the remote main server’s address when contructing.

    You don’t need to understand much about the `socketserver.TCPServer` class. All you have to know is that when receiving a request from the client, it will automatically pass the request to the `HTTPRequestHandler`, which will process the request as described above.

    In addition, our caching server should have functionalities to manage a cache table and send request to remote main server if needed. As a result, there is a `self.cacheTable` field that holds a `CacheTable` object and two methods, `requestMainServer()` and `touchItem()`. Briefly speaking, `requestMainServer()` can send a request to remote main server and get the [http.client.HTTPResponse](https://docs.python.org/3.6/library/http.client.html#httpresponse-objects). The `touchItem()` is called by `HTTPRequestHandler` to visit the `self.cacheTable` or call `requestMainServer()` if the target does not exist in cache table.

Here is a diagram to explain clearly:

![Caching server structure](../.gitbook/assets/caching\_server.png)

### Main Server

We provide a simple remote main server in `mainServer/mainServer.py`. It is just a [http.server](https://docs.python.org/3.6/library/http.server.html) which is a TCP server with some extra configurations to the Python native server. There is a directory `doc/` under `mainServer` that stores files for testing.

## Your tasks

Now you should fill in the blanks to complete a functional caching server. Before you start, you need to know that all the components work together so that it doesn’t mean you shall go step by step, but read the steps through and through to make things clear.

Besides the code’s structure is just in a style I prefer. You can change whatever you like except:

{% hint style="danger" %}
you should **NOT** change the method name that is decorated by `@trace` since these are methods we are going to use to trace the behavior of your program. You can change the arguments and return values of methods, though.
{% endhint %}

### Step1: HTTPRequestHandler

As introduced above, the `HTTPRequestHandler`should process the HTTP requests. It will call `do_GET()` when receives a HTTP GET request and `do_HEAD()` when receives HTTP HEAD. Since `do_HEAD()` is similar to `do_GET`, we only need focus on `do_GET()`.

As the diagram shows, the `HTTPRequestHandler` don’t check if the cache exists itself. It simply calls the `touchItem()` method of the server and gets its return value to see if the target exists or not. If the target exists, it sends headers and body back to client.

You need to:

* Complete `CachingServerHttpHandler.sendHeaders()`
* Complete `CachingServerHttpHandler.do_GET()`
*   Complete `CachingServerHttpHandler.do_HEAD()`

    At last, you will have a `HTTPRequestHandler` that can successfully reply to client.

    Methods and fields you need to know:

    * [self.send\_response(_code_, _message=None_)](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler.send\_response): create a response header and set status code. Status code can be found in [here](https://docs.python.org/3/library/http.html#http.HTTPStatus).
    * [self.send\_header(_keyword, value_)](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler.send\_header): prepare one header to be sent.
    * [self.end\_headers()](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler.send\_header): indicating the end of the HTTP headers in the response.
    * [self.send\_error(_code_, _message=None_, _explain=None_)](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler.send\_error): send an error to client, e.g.
    *   ```
        from http import HTTPStatus

        class CachingServerHttpHandler(BaseHTTPRequestHandler):
          ...
        	self.send_error(HTTPStatus.NOT_FOUND, "'File not found'")
        	...
        ```

        will send a “404 Not Found” to client.
    * [self.path](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler.path): the _path-to-file_ in the URL.
    * [self.server](https://docs.python.org/3.6/library/http.server.html#http.server.BaseHTTPRequestHandler.server): the server instance, i.e. you can call `touchItem()` by:
    * ```
      class CachingServerHttpHandler(BaseHTTPRequestHandler):
        ...
      	self.server.touchItem(self.path)
        ...
      ```

### Step2: Caching Server

First you should have a glimpse to `CacheTable` in `cachingServer/cacheTable.py`. The `CacheTable` is a dictionary-like data structure that use target’s _path-to-file_ as the key and a `CacheItem` as the value. The `CacheItem` stores the target’s _headers_, _body_ and _timestamp_. As a result, you can visit a target by the _path-to-file_ of it.

`requestMainServer` is called to request the target _path to file_ to remote main server. It will get a response which is a [http.client.HTTPResponse](https://docs.python.org/3.6/library/http.client.html#httpresponse-objects) object.

Methods you need to know:

* [http.client.HTTPResponse.getheaders()](https://docs.python.org/3.6/library/http.client.html#http.client.HTTPResponse.getheaders): get headers of the reponse.
* [http.client.HTTPResponse.read()](https://docs.python.org/3.6/library/http.client.html#http.client.HTTPResponse.read): read the whole body of the response.

`touchItem()` is the bridge in the server. It is called by `HTTPRequestHandler` to check if the target exists in local cache table. If not, call `requestMainServer` to get the response and store the target in `cacheTable`. If so, return the _headers_ and _body_ of the target so that `HTTPRequestHandler` can send back to client.

Your tasks: complete `touchItem()`:

* Check if the target _path-to-file_ exists in `cacheTable`.
* Check if the target has expired (timeout is set in variable `CACHE_TIMEOUT`).
* If the target doesn’t exist or has expired, fetch from remote main server.

> We provide a method `CachingServer._filterHeaders()` to discard headers that doesn’t need to store and methods to log (e.g. `log_info()`, `log_error()`). You may use them or design your own methods.

At last, you will have a working caching server.

### Optional Step: Stream Forwarding

{% hint style="info" %}
This is an optional step which is challenging. You will get extra score if you successfully finish it but relax if you find it too difficult. The key benifits you will get after you finish is actually not the score, but your progess in programming and the understanding of network systems. Please think through this step **after** you finish the 2 steps above.
{% endhint %}

After you finish the logic above, you may have a question: what will happen if I download a large file?

Suppose that it takes 1 hour to transfer the file from one machine to another. When a client tries to download the file from caching server but the caching server does not have the file locally, the caching server will fetch the file from remote main server, which will take 1 hour to transfer. After that, it will take another 1 hour to transfer the file back to client. It takes 2 hours for the client to download the file! Remeber that the purpose of the caching server is to reduce client's reponse time, however this approach slow down the response.

The cause of the problem is that the caching server’s 2 steps: fetch and response are two seperate process that run sequentially. To address this issue, the caching server should fetch the file and send the file to client at the same time.

To implement this, the `touchItem()` shouldn't read the whole response from remote main server, but read it to a small buffer (`BUFFER_SIZE = 64 KB`), store the buffer to local cache and send the buffer to client, buffer by buffer.

Things you may need:

* [http.client.HTTPResponse.readinto(b)](https://docs.python.org/3.6/library/http.client.html#http.client.HTTPResponse.readinto): read to a bytearray, return bytes read.
* [Python Generator](https://realpython.com/introduction-to-python-generators/): used to return buffer one by one

You can design your own logic to finish this step.

✅ In the report, show how you implement the features of caching server.

## Test your code

### Manually

Open three terminal windows. One for main server, one for caching server and last one for you. We are going to start the three processes locally for testing. Remeber the loopback address `localhost` is an alias of `127.0.0.1` which is a special address to visit the local web application.

1.  Start main server:

    ```
    $ python3 mainServer/mainServer.py -d mainServer/
    Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
    ```

    This will start a HTTP main server at every interfaces of your computer and you can visit it at [http://localhost:8000](http://localhost:8000). Also you can use the `-h` paramenter to see more options.
2.  Start caching server:

    ```
    $ python3 runCachingServer.py localhost:8000
    ```

    The argument indicate the "remote" main server’s address, which can be visited at`http://localhost:8000` as shown above. Notice that you should omit `http://` in argument. This will start the caching server at every interfaces of your computer and you can visit it at [http://localhost:1222](http://localhost:1222).
3.  Download a file to test

    On Linux/macOS, you can use `curl` command to download a file. In any directory on your computer, input command:

    ```
    $ curl -O http://localhost:1222/doc/success.jpg
    ```

    will download the file `success.jpg` in your directory. Notice that this is the file that locates at `mainServer/doc/success.jpg`. \
    Besides, you can simply use a browser to visit the file. Type the URL in your browser and you shall see the image.

    At the first time you download, the caching server will fetch it from main server. But the second request (before expires) will return the local cache directly.

    If you input a path that doesn’t exists:

    ```
    $ curl http://localhost:1222/nonexist
    ```

    You will get a "404 Not Found" error.

    To test method `do_HEAD()`, use the `-I` parameter of `curl` :

    ```
    $ curl -I http://localhost:1222/doc/success.jpg
    ```
4. The first time you download, the caching server should fetch it from main server. Pay attention to the logs.

### Testcases

A testcase is provided to individually check if the caching server's logic is correct. In the project’s directory:

```
$ python3 test_entry.py cache
2021/05/17-20:46:33| [INFO] Main server started
2021/05/17-20:46:33| [INFO] RPC server started
2021/05/17-20:46:33| [INFO] Caching server started
test_01_cache_missed_1 (testcases.test_cache.TestCache) ...
[Request time] 13.36 ms
ok
test_02_cache_hit_1 (testcases.test_cache.TestCache) ...
[Request time] 6.33 ms
ok
test_03_cache_missed_2 (testcases.test_cache.TestCache) ...
[Request time] 7.60 ms
ok
test_04_cache_hit_2 (testcases.test_cache.TestCache) ...
[Request time] 6.07 ms
ok
test_05_HEAD (testcases.test_cache.TestCache) ...
[Request time] 4.78 ms
ok
test_06_not_found (testcases.test_cache.TestCache) ...
[Request time] 5.72 ms
ok
​
----------------------------------------------------------------------
Ran 6 tests in 3.464s
​
OK
2021/05/17-20:46:37| [INFO] Caching server terminated
2021/05/17-20:46:37| [INFO] PRC server terminated
2021/05/17-20:46:37| [INFO] Main server terminated
```

{% hint style="warning" %}
Before running the testcases, you must stop the processes started manually in the last section. The reason is that the processes use a fixed port number to communicate with each other by default, so that if you had already started the processes manually, then the testcases would fail because the processes cannot start agian.
{% endhint %}

{% hint style="info" %}
The testcases are run by Python unittest module. This will suppress the standard output of your program (which means your print will take no effect). If you must see the stdout, test manually or write the messages to a file instead.
{% endhint %}
