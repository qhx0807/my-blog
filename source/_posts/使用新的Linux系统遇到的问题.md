---
title: 使用新的Linux系统遇到的问题
date: 2017-09-01 10:39:40
categories: linux
---

系统 linux centos 6.3

#### `Cannot find a valid baseurl for repo: base`

出现这个问题是因为yum在安装包的过程中，虽然已经联网，但是没法解析远程包管理库对应的域名，所以只需要在网络配置中添加上DNS对应的ip地址即可。

1. 打开 `ifcfg-eth0` 文件
```bash
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

2. 在文件末尾追加DNS

```bash
DNS1=8.8.8.8
DNS2=4.2.2.2
```

3. 重启网络

```bash
ifup eth0
```

> `eth0` 是网卡名称， 可以使用命令 `ip addr` 查看。

#### wget: unable to resolve host address

wget：无法解析主机地址。这就能看出是DNS解析的问题

1. 打开 `/etc/resolv.conf`

```bash
vim /etc/resolv.conf
```

2. 修改内容为下

```bash
nameserver 8.8.8.8
search localdomain
```

3. 然后重启network服

```bash
service network restart
```

#### git clone 提示 fatal: HTTP request failed

1. git版本太低 升级版本


#### yum安装时出现：Cannot retrieve metalink for repository: epel. Please verify its path and try again

1. 打开 `/etc/yum.repos.d/epel.repo`

2. 将 

```bash
    [epel]
    name=Extra Packages for Enterprise Linux 6 – $basearch
    #baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
    mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
```
修改为:

```bash
    [epel]
    name=Extra Packages for Enterprise Linux 6 – $basearch
    baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
    #mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
```
3. 执行clean, 再重新安装

```bash
yum clean all
yum install 包名
```
