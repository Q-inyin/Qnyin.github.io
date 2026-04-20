---
title: 实施工程师网络排查指南
date: 2026-04-21 02:45:42
tags: ['实施','运维']
---

# 实施工程师网络排查指南

网络排查是实施工程师必备技能。本指南涵盖从基础连通性到端口、DNS、路由排查的常用方法。

---

## 目录

1. [网络基础检查](#network-basic)
2. [IP 配置与网关](#ip-gateway)
3. [Ping 和连通性测试](#ping-test)
4. [端口与服务检测](#port-service)
5. [路由与 traceroute](#route-traceroute)
6. [DNS 排查](#dns-troubleshoot)
7. [日志与抓包分析](#log-packet)
8. [网络排查流程示例](#network-flow)

---

## 网络基础检查 

<details><summary>展开/收起</summary>

1. 检查本机 IP 配置：
```bash
# Linux
ip addr show
ifconfig

# Windows
ipconfig
```
2. 检查网卡状态：
```
# Linux
ip link show

# Windows
netsh interface show interface
```
3. 查看网络接口是否启用

</details>

## IP 配置与网关

<details> <summary>展开/收起</summary>

1. 查看默认网关：

```
# Linux
ip route
route -n

# Windows
route print
```

2. 设置临时 IP（测试用）：

```
# Linux
sudo ip addr add 192.168.1.50/24 dev eth0

# Windows
netsh interface ip set address name="Ethernet" static 192.168.1.50 255.255.255.0 192.168.1.1
```

3. 测试网关连通性：

```
ping 192.168.1.1
```

</details>

------

## Ping 和连通性测试

<details> <summary>展开/收起</summary>

1. 测试目标主机是否可达：

```
ping 8.8.8.8 -c 4   # Linux
ping 8.8.8.8        # Windows
```

2. 使用 `ping` 测试延迟与丢包情况
3. 使用 `arp -a` 查看局域网内 MAC 地址

</details>

------

## 端口与服务检测 

<details> <summary>展开/收起</summary>

1. 检查端口是否开放：

```
# Linux
nc -zv 192.168.1.100 80
telnet 192.168.1.100 3306

# Windows
Test-NetConnection -ComputerName 192.168.1.100 -Port 3306  # PowerShell
```

2. 查看本机监听端口：

```
# Linux
netstat -tulnp

# Windows
netstat -ano
```

</details>

------

## 路由与 traceroute 

<details> <summary>展开/收起</summary>

1. 查看路由路径：

```
# Linux
traceroute www.baidu.com

# Windows
tracert www.baidu.com
```

2. 查看路由表：

```
# Linux
route -n
ip route

# Windows
route print
```

</details>

------

## DNS 排查

<details> <summary>展开/收起</summary>

1. 查询域名解析：

```
# Linux
nslookup www.baidu.com
dig www.baidu.com

# Windows
nslookup www.baidu.com
```

2. 刷新 DNS 缓存：

```
# Linux (systemd)
sudo systemd-resolve --flush-caches

# Windows
ipconfig /flushdns
```

</details>

------

## 日志与抓包分析 

<details> <summary>展开/收起</summary>

1. 查看系统日志：

```
# Linux
journalctl -f
tail -f /var/log/syslog
tail -f /var/log/messages

# Windows
Event Viewer
```

2. 使用抓包工具分析：

```
# Linux
tcpdump -i eth0 port 3306

# Windows
Wireshark GUI
```

</details>

------

## 网络排查流程示例 

<details> <summary>展开/收起</summary>

1. 检查本机 IP 与网关
2. Ping 内网网关和服务器
3. 测试端口是否开放（Telnet / NC / PowerShell）
4. 使用 traceroute 确认路由路径
5. 查询 DNS 是否正确解析
6. 查看日志和抓包分析异常
7. 如果需要，调整 IP 或防火墙策略

</details> ```

****

覆盖 **基础检查 → IP/网关 → Ping → 端口 → 路由 → DNS → 日志抓包 → 排查流程**

兼顾 Linux 和 Windows 常用命令