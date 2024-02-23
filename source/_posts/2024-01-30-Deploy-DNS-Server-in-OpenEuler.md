---
title: Deploy DNS Server in OpenEuler
tags: [DNS, OpenEuler]
date: 2024-01-30 18:20:32
categories: 运维
---

## 0 起因

原有使用的 DNS 服务器是 Windonws Server 2008，已经不再提供安全更新了，想着索性升级到最新版，按照新的管理规定优先采用国产的服务器系统，
第一个想到的是OpenEuler，所以就决定采用国产的系统进行替换，刚好借此机会学习一下 Linux 上 DNS 服务的部署和配置方法。当然，这里又犯了爱折腾的毛病。

### 0.1 需要配置的主机和IP

```
nj-dns.cnty.com        192.168.7.120
nj-gitlab.cnty.com     192.168.7.121
nj-vcsa.cnty.com       192.168.7.44
nj-nextcloud.cnty.com  192.168.7.45
nj-exsi1.cnty.com      192.168.6.252
nj-exsi2.cnty.com      192.168.7.99
```

## 1 安装 OpenEuler

采用默认的方式安装，没有什么意外情况，图形界面的方式还是比较直观的。

## 2 安装 DNS Server

使用 `dnf` 安装 BIND(Berkeley Internet Name Domain)

```shell
sudo dnf install bind bind-utils -y
```

## 3 配置 DNS

### 3.1 /etc/named.conf

在 `include "/etc/named.rfc1912.zones";` 这一行上面添加如下的内容：

```
zone "cnty.com" IN {
  type master;
  file "forward.cnty.com";
  allow-update { none; };
};

zone "168.192.in-addr.arpa" IN {
  type master;
  file "reverse.cnty.com";
  allow-update { none; };
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

### 3.2 /var/nanmed/forward.cnty.com

```
$TTL 86400
$ORIGIN cnty.com.
@   IN  SOA     nj-dns.cnty.com. root.cnty.com. (
        2011071001  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
                   IN  NS  nj-dns.cnty.com.
nj-dns           IN  A   192.168.7.120
nj-vcsa          IN  A   192.168.7.44
nj-gitlab        IN  A   192.168.7.121
nj-nextcloud     IN  A   192.168.7.45
nj-exsi1         IN  A   192.168.6.252
nj-exsi2         IN  A   192.168.7.99
oa               IN  A   192.168.x.x
www              IN  A   115.231.8.xxx
```

### 3.3 /var/named/reverse.cnty.com

```
$TTL 86400
@   IN  SOA     nj-dns.cnty.com. root.cnty.com. (
        2011071001  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)

@      IN  NS          nj-dns.cnty.com.
@      IN  A           192.168.7.120

120.7    IN  PTR         nj-dns.cnty.com.
44.7     IN  PTR         nj-vcsa.cnty.com.
121.7    IN  PTR         nj-gitlab.cnty.com.
45.7     IN  PTR         nj-nextcloud.cnty.com.
99.7     IN  PTR         nj-exsi2.cnty.com.
252.6    IN  PTR         nj-exsi1.cnty.com.
```

## 4 检查配置文件

```
sudo named-checkconf /etc/named.conf
sudo named-checkzone forward.cnty.com /var/named/forward.cnty.com
sudo named-checkzone reverse.cnty.com /var/named/reverse.cnty.com
```

## 5 配置防火墙

```
sudo firewall-cmd --add-service=dns --perm
sudo firewall-cmd --reload
```

## 6 启动 DNS Server

```
sudo systemctl enable named
sudo systemctl start named
```

## 7 测试 DNS 服务

```
dig nj-vcsa.cnty.com
dig -x 192.168.7.44
```

## 参考

1. [如何使用 bind 设置 DNS 服务器](https://zhuanlan.zhihu.com/p/113302346)
2. [3. Configurations and Zone Files](https://bind9.readthedocs.io/en/latest/chapter3.html)
3. [DNS 多网段的反向记录](https://blog.csdn.net/weixin_34037515/article/details/93043175)
