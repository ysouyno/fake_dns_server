# fake_dns_server

## `linux`发行版的选择

参考自：“[Building a DNS server in Rust](https://github.com/EmilHernvall/dnsguide)”。

安装`dig`和`nc`两个程序在`archlinux`上，但是我没有尝试成功，可能是`nc`的原因，它生成的`query_packet.txt`文件大小为`0`：

``` shellsession
% sudo pacman -S dnsutil
% sudo pacman -S gnu-netcat
```

``` shellsession
% nc -u -l 1053 > query_packet.txt
```

最后在`debian`尝试发现可行。

## 文件`query_packet.txt`和`response_packet.txt`是哪里来的

首先执行`nc`命令，`debian`下的`nc`命令参数与文章中描述的稍有不同，端口前使用`-p`参数，如下：

``` shellsession
$ nc -u -l -p 1053 > query_packet.txt
```

打开另一个`Terminal`执行：

``` shellsession
$ dig +retry=0 -p 1053 @127.0.0.1 +noedns baidu.com
```

在`dig`命令返回后`Ctrl+C`结束`nc`，此时生成`query_packet.txt`，然后运行以下命令生成`response_packet.txt`：

``` shellsession
$ nc -u 8.8.8.8 53 < query_packet.txt > response_packet.txt
```

执行一秒后即可用`Ctrl+C`结束`nc`，此时生成`response_packet.txt`。

## `dig` for Windows

可以在这里找到：“[Dig command on Windows OS](https://websistent.com/dig-command-on-windows-os/)”，附“[dig-for-windows-9.9.5-W1.zip](files/dig-for-windows-9.9.5-W1.zip)”。

## Chapter 4

在`Windows`下，先运行`cargo run`，然后再使用`dig`如下：

``` shellsession
> dig @127.0.0.1 -p 2053 google.com

; <<>> DiG 9.9.5-W1 <<>> @127.0.0.1 -p 2053 google.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1224
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             60      IN      A       46.82.174.69

;; Query time: 39 msec
;; SERVER: 127.0.0.1#2053(127.0.0.1)
;; WHEN: Sat Jul 18 14:22:15 中国标准时间 2020
;; MSG SIZE  rcvd: 54
```

此时`cargo run`的输出如下：

``` shellsession
> cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.02s
     Running `target\debug\fake_dns_server.exe`
Received query: DnsQuestion { name: "google.com", qtype: A }
Answer: A { domain: "google.com", addr: 46.82.174.69, ttl: 60 }
```
