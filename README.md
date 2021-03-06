First of all, thank to @n0nag0n come from the USA.
This is an idea project. I want to write Write high performance and high concurrency services such as TCP, UDP, UNIX socket, HTTP, websocket, etc., so that PHP is no longer limited to the web domain.
@n0nag0n, His project gave me good ideas, but the ideas were incomplete. So this project is incomplete. It's just a test. It cannot be used completely.
1. The FatFree framework is very good, but when combined with Swoole, it is obviously bloated, so FatFree must be better used in the CLI, we will change it in new projects.
2. The project can no longer be limited to one server, or when we use thousands of servers to run the project, there is a lack of server integration and control. We will solve it in new projects.
3. ...
4. ...

New: https://github.com/DpodoNetwork/DSF

# Write high performance and high concurrency services such as TCP, UDP, UNIX socket, HTTP, websocket, etc., so that PHP is no longer limited to the web domain.

**Work on Fatfree Froframework**
This is not a complete framework. The new scheme has been written, updated and organized: dpodo

# FatFree Swoole

In order to get this to work, clone this repo, then run `composer install` then do the following:
1. Around line 2314, Unprivileged or comment out the `private function __clone() {}` so the base can be cloned(**It's just a temporary alternative**).
2. Use：`cd bin && php Fs 127.0.0.1 9501`

# Take care
Please be aware you need to install swoole first. Also you **MUST** disable xdebug in order for swoole to run properly.

# Make
1. MongoDB can't work, close `enable_coroutine`.
2. Not Supported: `set_exception_handler`, **must be** use `try/catch`to handle exceptions.
3. Using static class ariable `class::$array`, global object attribute `$object->array`, and other super global variable `$GLOBALS`,  is very dangerous. 
Etc. to save context in a cooperation program.
4. importing PHP files of classes / functions **must be** use include_once or require_once, otherwise a fatal error: cannot redeclare function/class!

# Test
Here are some preliminary benchmarks using `ab -n 6000 -c 500 http://localhost:9501/hey`

# nginx+swoole config
```
server {
    root /data/wwwroot/;
    server_name local.swoole.com;

    location / {
        proxy_http_version 1.1;
        proxy_set_header Connection "keep-alive";
        proxy_set_header X-Real-IP $remote_addr;
        # if (!-e $request_filename) {
             proxy_pass http://127.0.0.1:9501;
        # }
    }
}
```
Using nginx anti proxy will consume some resources and reduce performance

Internal PHP Webserver:
```
Server Software:        
Server Hostname:        localhost
Server Port:            9502

Document Path:          /hey
Document Length:        44 bytes

Concurrency Level:      500
Time taken for tests:   1.909 seconds
Complete requests:      6000
Failed requests:        0
Total transferred:      2532000 bytes
HTML transferred:       264000 bytes
Requests per second:    3143.65 [#/sec] (mean)
Time per request:       159.051 [ms] (mean)
Time per request:       0.318 [ms] (mean, across all concurrent requests)
Transfer rate:          1295.53 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    5  67.7      0    1031
Processing:     6   46  50.7     38     837
Waiting:        6   46  50.7     38     837
Total:         20   51 113.2     38    1865

Percentage of the requests served within a certain time (ms)
  50%     38
  66%     39
  75%     42
  80%     43
  90%     48
  95%     96
  98%    145
  99%    151
 100%   1865 (longest request)

```

Swoole HTTP Server:
```
Server Software:        swoole-http-server
Server Hostname:        localhost
Server Port:            9501

Document Path:          /hey
Document Length:        55 bytes

Concurrency Level:      500
Time taken for tests:   0.440 seconds
Complete requests:      6000
Failed requests:        0
Total transferred:      1266000 bytes
HTML transferred:       330000 bytes
Requests per second:    13622.49 [#/sec] (mean)
Time per request:       36.704 [ms] (mean)
Time per request:       0.073 [ms] (mean, across all concurrent requests)
Transfer rate:          2806.98 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        2    7   2.2      6      18
Processing:     2    9   3.3      8      28
Waiting:        1    7   3.0      6      23
Total:          8   16   5.1     14      39

Percentage of the requests served within a certain time (ms)
  50%     14
  66%     15
  75%     15
  80%     16
  90%     18
  95%     30
  98%     34
  99%     36
 100%     39 (longest request)
```
