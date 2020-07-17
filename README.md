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
nc -u 8.8.8.8 53 < query_packet.txt > response_packet.txt
```

执行一秒后即可用`Ctrl+C`结束`nc`，此时生成`response_packet.txt`。
