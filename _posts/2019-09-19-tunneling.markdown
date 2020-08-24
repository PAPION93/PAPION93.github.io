---
layout: post
title: Linux iptables로 포트 포워딩하기
subtitle: Linux iptables NAT 사용방법 기록
date: 2019-09-19 00:00:00 +0300
description: # Add post description (optional)
tags: [network, linux] # add tag
categories: [network, linux] # add categories
---

내부 네트워크내에 전용선으로 연결된 서버들을 외부의 특정 서버와 통신을 가능토록 하기 위한 프록시 서버가 필요하여 세팅해보았습니다.  
리눅스의 `iptables`를 통해 포트포워딩하는 방법에 대해 알아보겠습니다.  
`iptables`는 `패킷 필터링(Packet Filtering)` 도구로서 방화벽 구성이나 `NAT(Network Address Translation)`에 사용됩니다.

### ✔ 개요

사설망내의 서버(client 지칭)를 프록시 서버를 통해 외부서버(server 지칭)와 통신하도록 하는 것이 목적입니다.  
client 에서 데이터를 전송하는 부분과 반대로 server에서 client로 전송하는 부분에 대한 설정을 기록할 것입니다.

먼저 client 에서 server로 데이터를 전송하는 부분입니다.  
아래 IP는 내부 및 외부 구분용으로 봐주시면 됩니다. 포트는 원하는 포트로 바꿔서 사용하면 됩니다.

> client(1.1.1.1) ➡ 프록시 서버(eth0:2.2.2.2, eth1:192.168.0.2) ➡ server(3.3.3.3:80)

1. client는 프록시 서버로 데이터 전송.

    > client(1.1.1.1) ➡ 프록시 서버(192.168.0.2)

2. 프록시 서버에서 목적지 IP를 외부 서버의 IP로 변경(`DNAT`), 소스지 IP는 프록시 서버의 외부IP(2.2.2.2)로 변경하여 데이터 전송(`SNAT`).
    > 프록시 서버(2.2.2.2) ➡ 외부 서버(3.3.3.3)

위의 1, 2 과정을 iptables 에 적용하면 아래와 같습니다.

```
# DNAT(LAN ➡ EX)
-A PREROUTING -i eth1 -d 192.168.0.2 -p tcp -m tcp --dport 80 -j DNAT --to-destination 3.3.3.3:80
# SNAT
-A POSTROUTING -s 1.1.1.1 -p tcp -m tcp -j SNAT --to-source 2.2.2.2
```

✔ 이제 반대로 server에서 client로 데이터 전송시 포워딩할 내용입니다.

> 외부 서버(3.3.3.3) ➡ 프록시 서버(192.168.0.2) ➡ client(1.1.1.1)

프록시 서버는 목적지 IP를 client IP로 변경하고, 소스지 IP를 자신의 내부IP로 변경합니다.  
iptables 설정을 적용하면 아래와 같습니다.

```
# DNAT
-A PREROUTING -i eth0 -s 3.3.3.3 -p tcp -m tcp -j DNAT --to-destination 1.1.1.1:80

# SNAT
-A POSTROUTING -s 3.3.3.3 -p tcp -m tcp -j SNAT --to-source 192.168.0.2
```

✔ 마스커레이딩 기능 추가

> -A POSTROUTING -o eth0 -s 2.2.2.2 -j MASQUERADE

-   내부 네트워크에 있는 컴퓨터가 방화벽 기능을 하는 리눅스 서버(또는 게이트웨이/프록시)를 통해서 외부로 데이터 등을 보내려고 할 때, 리눅스 서버가 그 컴퓨터를 '마스커레이딩'한다고 말한다.

✔ 추가 내용

-   iptables chain `PREROUTING ➡ route ➡ FORWARD ➡ POSTROUTING`
-   server에서 client로 데이터를 전송할 당시 프록시 서버는 client의 정보를 알지 못해 라우팅하지 못하는 상황이였습니다.
-   그리하여 routing table에 default gateway 를 추가하고 rc.local 등록하여 마무리하였습니다.

> route add -net 1.1.1.1 netmask 255.255.255.255 gw 192.168.0.1 dev eth1

또 하나 배웠지만 엄청나게 무지한 나를 발견한 순간이였습니다. 공부하자..
