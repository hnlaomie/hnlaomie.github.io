Title: dnscrypt proxy
Date: 2019-03-22 08:30
Category: tools
Tags: 201903, dnscrypt
Author: laomie
Summary: dnscrypt-proxy安装

Preface
=================================
>因为众所周知的原因，DNS缓存投毒(aka: DNS Spoofing) 的出现是因为DNS查询通信协议的设计缺陷导致的，因为DNS查询协议的设计者当时并没有预料到现在的网络环境这么糟糕。利用这个缺陷可以实现一些特技，比如：
>
>用浏览器打开Google然后跳转到了Baidu.
>ISP(网络服务提供商, 比如 奠信 联不通 移不动, etc.)在网页里随意插入广告.
>这里没能列举出的一些特技.
>为了解决这个问题，我（不知道在什么地方什么时候意外的）发现了个软件叫做 dnscrypt(GitHub项目主页)，相比传统的DNS查询有如下优点：
>
>与DNS服务器通信过程中使用SSL加密.
>可以强制使用TCP，避免因UDP丢包而浪费时间.
>有些DNS服务器不会记录用户查了哪些记录，进而也就不会知道用户去了哪些网站.
>有些DNS服务器支持DNSSEC，进一步加密/验证DNS查询结果.
>不过使用dnscrypt的缺点也是有的：相比ISP提供的DNS，dnscrypt的查询会慢上最少300ms。导致这个问题的原因包括但不限于：
>
>UDP丢包.
>强制使用TCP的话，TCP握手也要很久.
>SSL握手.
>城门口堵车了.
>不过这个问题也是可以解决的，那就是在本地搭建一个DNS服务器来缓存dnscrypt的查询结果。
>接下来，我就简单的写一下 dnscrypt-proxy 和 bind(用来缓存DNS查询结果) 的安装和配置.
>
>注1：下面的内容适用于Ubuntu及其衍生Distro，其他Linux Distro可能需要做些修改。
>注2：我之所以没用 dnsmasq，是因为无论怎么配置它 它都不缓存向本地请求的记录，但会缓存非本地地址的记录 比如用8.8.8.8它就会缓存。

dnscrypt-proxy
=========================================
>DNSCrypt 主页
>dnscrypt-proxy GitHub 主页
>dnscrypt-proxy launchpad.net PPA

安装 dnscrypt-proxy
=========================================
```
apt-get install dnscrypt-proxy
```

配置 dnscrypt-proxy
===========================
配置dnscrypt-proxy，配置文件在 /etc/default/dnscrypt-proxy：
```bash
# What local IP the daemon will listen to, with an optional port.
# The default port is 53. If using systemd, this is not used and must be
# specified in dnscrypt-proxy.socket.
# 设置dnscrypt-proxy监听哪个地址的哪个端口。
# 然而这个配置项并不管用，请参考下面的配置方法。
DNSCRYPT_PROXY_LOCAL_ADDRESS1=127.0.2.1:53
#DNSCRYPT_PROXY_LOCAL_ADDRESS2=127.0.2.2:53

# Remote DNS(Crypt) resolver.
# You can find a list of resolvers at
# /usr/share/dnscrypt-proxy/dnscrypt-resolvers.csv.
# 从 dnscrypt-resolvers.csv 的 'name' 列里挑选自己想要的dns服务器.
# 这里给几个例子："cisco" 访问速度最快，"dnscrypt.eu-dk" 不会记录查询日志.
DNSCRYPT_PROXY_RESOLVER_NAME1=dnscrypt.eu-nl
#DNSCRYPT_PROXY_RESOLVER_NAME2=dnscrypt.eu-dk

# Extra flags to pass to dnscrypt-proxy
# dnscrypt-proxy 的其他命令行参数，这里简单解释下：
# --tcp-only：只使用TCP连接.
# --logfile=/path/logfile：设置日志的存放位置.
# --loglevel=7：7为DEBUG级别的日志输出级别, 能看到查询超时的日志信息.
DNSCRYPT_PROXY_OPTIONS="--tcp-only --logfile=/var/log/dnscrypt-proxy/dnscrypt-proxy.log --loglevel=7"
```
DNSCRYPT_PROXY_LOCAL_ADDRESS1 无效的原因是因为 使用systemd的话会在另一个地方配置这个参数，这篇博文 提到了解决这个问题的方法。
执行命令来覆盖 dnscrypt-proxy.socket 的配置：systemctl edit dnscrypt-proxy.socket，然后写入如下内容（假设让dnscrypt监听 127.0.2.1:53）：
```bash
[Socket]
ListenStream=
ListenDatagram=
ListenStream=127.0.2.1:53
ListenDatagram=127.0.2.1:53
```
注：systemctl 会使用 emacs 作为文本编辑器，如果想用别的（比如 vim）就执行 export SYSTEMD_EDITOR="vim" 即可。
配置完成后执行命令 systemctl restart dnscrypt-proxy，使配置文件生效。

安装 bind9
===============================
```
apt-get install bind9 bind9-doc
```

配置 bind9
=========================
编辑文件 /etc/bind/named.conf.options：
```
options {
        directory "/var/cache/bind";

        // 禁用dnssec
        dnssec-enable no;
        dnssec-validation no;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };

        // 启用递归查询
        recursion yes;
        // 允许指定的地址向bind9请求dns查询, 参考下面的 "acl goodclients".
        allow-query { goodclients; };

        // 将dns请求转发到指定地址
        forwarders {
                // 这里我们把它转发到 dnscrypt-proxy.
                127.0.2.1;
        };
        // bind9 只做dns转发工作而不做解析工作.
        forward only;

        // 缓存dns记录最多占用多少内存, 单位 bytes.
        // 64MiB = 65536Bytes
        max-cache-size 65536;
};

// Access Control List，这里用来配置成 允许向bind9查询的客户端.
acl goodclients {
        127.0.0.1;
        localhost;
        localnets;
};

// bind9 日志
logging {
        // 配置一个channel，该channel会记录详细的dns请求和响应.
        channel channel_debug {
                // size 10m: 每个日志文件最大 10MiB.
                // versions 3: 超过10MiB就创建新的日志文件.
                file "/var/log/named/channel_debug.log" versions 3 size 10m;
                severity debug 3;
                print-time yes;
                print-severity yes;
        };
        // 配置一个channel，该channel不会记录dns请求和响应.
        channel channel_default {
                file "/var/log/named/channel_debug.log" versions 3 size 10m;
                severity info;
                print-time yes;
                print-severity yes;
        };
        // 默认使用 channel_default.
        category default {
                channel_default;
        };
};
```

为部分网站使用大陆DNS
如果所有DNS请求都通过dnscrypt-proxy的话 就没法享受大陆CDN加速了，一个明显的现象就是 dns解析完了但网页得等好久才打开。
为了解决这个问题，GitHub上有个项目 felixonmars/dnsmasq-china-list 用来配置dnsmasq专门为大陆网站指定大陆的dns。但dnsmasq的配置文件没法用在bind9上，所以我写了个py脚本，用来把 dnsmasq配置文件 转成 bind9 zone配置文件。
```
#!/usr/bin/env python3
import sys
import argparse


def __dnsmasqconf2bind9zone(dnsmasq_conf: str, dns_server: str) -> str:
    TEMPLATE_BIND9_ZONE = ("zone \"{DOMAIN}\" IN {{\n"
                           "    type forward;\n"
                           "    forwarders {{\n"
                           "        {DNS_SERVER};\n"
                           "    }};\n"
                           "}};\n")

    lSetBind9zone = []
    n = 0
    for line in dnsmasq_conf.split("\n"):
        lStrSplit = line.split("/")
        if len(lStrSplit) != 3:
            continue
        lSetBind9zone.append(TEMPLATE_BIND9_ZONE.format(DOMAIN=lStrSplit[1], DNS_SERVER=dns_server))
        pass
    return "".join(lSetBind9zone)


def __init_argparse() -> argparse.Namespace:
    parser = argparse.ArgumentParser(formatter_class=argparse.RawTextHelpFormatter,
                                     prog="dnsmasqconf2bind9zone.py",
                                     description="dnsmasq.conf to bind9 zone utility.\n"
                                                 "This utility will read dnsmasq.con from stdin then output put to stdout.\n"
                                                 "\n"
                                                 "For accelerated domains for china check out: \"https://github.com/felixonmars/dnsmasq-china-list\".\n"
                                                 "Quick download url: https://github.com/felixonmars/dnsmasq-china-list/blob/master/accelerated-domains.china.conf?raw=true")

    parser.add_argument("--forward-to", default="223.5.5.5",
                        help="Where forward DNS request to. The default is \"223.5.5.5\".")
    return parser.parse_args()


def main(argv):
    args = __init_argparse()
    print(__dnsmasqconf2bind9zone(sys.stdin.read(), args.forward_to))
    pass

if __name__ == '__main__':
    main(sys.argv)
```

1. 将上面的代码保存到 dnsmasqconf2bind9zone.py.

2. 到 felixonmars/dnsmasq-china-list 下载 accelerated-domains.china.conf 文件.

3. 执行命令，将 dnsmasq conf 文件转为 bind 9 zone 配置文件.
```
cat accelerated-domains.china.conf | python3 dnsmasqconf2bind9zone.py > named.conf.accelerated-domains-china-zone
# 也可以单独指定使用哪个DNS服务器，加个参数即可：
python3 dnsmasqconf2bind9zone.py --forward-to <DNS服务器IP地址>
```

4. 配置 named.conf，在它结尾加一行:
```
include "/etc/bind/named.conf.accelerated-domains-china-zone
```

5. 执行命令重启 bind9 服务：systemctl restart bind9.

自定义 bind 9 zone

felixonmars/dnsmasq-china-list 项目内的 accelerated-domains.china.conf 里难免有漏网之鱼，通过自己配置 named.conf.accelerated-domains-china-zone 把漏掉的添加进去就好了。格式大概是这样：

```
zone "<域名>" IN {
    type forward;
    forwarders {
        <DNS服务器IP地址>;
    };
};
```

然后使用 systemctl reload bind9 使配置生效。

References
=========================
<https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-caching-or-forwarding-dns-server-on-ubuntu-14-04>
<https://github.com/felixonmars/dnsmasq-china-list>
<https://www.dommyet.me/dnsmasq-dnscrypt-smart-dns>

