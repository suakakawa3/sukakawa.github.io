---
title: Linux systemd-resolved 过程
date: 2026-02-27 11:44:17
tags:
---
## DNS查询过程
## 0x01
### systemd-resolved 查询(Query)
``` bash
❯ dig baidu.com @127.0.0.53 +noall +answer
```

	baidu.com.              43      IN      A       111.63.65.247
	baidu.com.              43      IN      A       110.242.74.102
	baidu.com.              43      IN      A       111.63.65.103
	baidu.com.              43      IN      A       124.237.177.164

### 完整应答
``` bash
❯ dig baidu.com @127.0.0.53
```

	;; OPT PSEUDOSECTION:
	; EDNS: version: 0, flags:; udp: 65494
	;; QUESTION SECTION:
	;baidu.com.                     IN      A
	
	;; ANSWER SECTION:
	baidu.com.              596     IN      A       110.242.74.102
	baidu.com.              596     IN      A       111.63.65.247
	baidu.com.              596     IN      A       111.63.65.103
	baidu.com.              596     IN      A       124.237.177.164
	
	;; Query time: 2 msec
	;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
	;; WHEN: Fri Feb 27 11:43:42 CST 2026
	;; MSG SIZE  rcvd: 102
## 0x02
## TLD查询过程
### 通过root TLD获取子com TLD的IP地址
``` bash
❯ dig a.root-servers.net A +answer
```

	;; QUESTION SECTION:
	;baidu.com.                     IN      A
	
	;; AUTHORITY SECTION:
	com.                    172800  IN      NS      l.gtld-servers.net.
	
	;; ADDITIONAL SECTION:
	l.gtld-servers.net.     172800  IN      A       192.41.162.30


### 得到com TLD隶属l.gtld-servers.net.
### 并且l.gtld-servers.net.的A记录IP为192.41.162.30
### 再用l.gtld-servers.net.的IP 192.41.162.30查询Baidu.com的NS
``` bash
❯ dig baidu.com @192.41.162.30
```

	;; QUESTION SECTION:
	;baidu.com.                     IN      A
	
	;; AUTHORITY SECTION:
	baidu.com.              172800  IN      NS      ns2.baidu.com.
	
	;; ADDITIONAL SECTION:
	ns2.baidu.com.          172800  IN      A       220.181.33.31

### 得到Baidu的NS记录ns2.baidu.com.
### 并且ns2.baidu.com.的A记录IP为220.181.33.31
### 再用ns2.baidu.com.的IP 220.181.33.31查询Baidu.com的A记录IPv4
```bash
❯ dig baidu.com @220.181.33.31 +noall +answer
```

	baidu.com.              600     IN      A       111.63.65.103
	baidu.com.              600     IN      A       110.242.74.102
	baidu.com.              600     IN      A       124.237.177.164
	baidu.com.              600     IN      A       111.63.65.247


### 得到Baidu的IP    111.63.65.103  110.242.74.102 124.237.177.164 111.63.65.247

## 0x03
## 复盘
### a.root-servers.net得到com隶属l.gtld-servers.net. 
	l.gtld-servers.net. 的IP为192.41.162.30 → Baidu.com
### 递归查询为192.41.162.30得到Baidu.com的NS隶属ns2.baidu.com.
	ns2.baidu.com.            IP为220.181.33.31 → Baidu.com
### 通过ns2.baidu.com. IP220.181.33.31直接得到Baidu.com的所有IP
        baidu.com.              596     IN      A       110.242.74.102
        baidu.com.              596     IN      A       111.63.65.247
        baidu.com.              596     IN      A       111.63.65.103
        baidu.com.              596     IN      A       124.237.177.164
### 系统/DNS自动优选
