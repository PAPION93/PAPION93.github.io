---
layout: post
title: HTTP 헤더 알아보기
subtitle: HTTP의 헤더 구조를 정리해보자
date: 2019-09-19 00:00:00 +0300
description: # Add post description (optional)
tags: [web, http] # add tag
categories: [web] # add categories
---

## ✔ Header 간단 정의

HTTP 헤더는 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 해줍니다.  
먼저 HTTP Request에 대해 먼저 알아보면 요청 메시지는 스펙상 다음과 같습니다.

```
Request-Line
*(( general-header | request-header | entity-header ) CRLF)
CRLF
[ message-body ]
```

첫번째 `Request Line` 은 스펙상 `[Method SP Request-URI SP HTTP-Version CRLF]` 형식입니다.  
Request Line 을 풀어보면 아래와 같습니다.

1 . Method 의 종류는 OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECT, PATCH 등  
2 . Request-URI 말그대로 URI 입니다. URI와 URL의 차이는 [여기로](https://papion93.github.io/web/2019/09/18/url_uri_diff.html)  
3 . 그 다음은 HTTP VERSION 입니다. HTTP/1.0과 HTTP/1.1, HTTP/2이 존재하며, HTTP/3 도 출시가 되었다.. 돌겄다!  
4 . CRLF 줄바꿈.

그 다음 헤더의 차례인데 Request 에는 `general-header`, `request-header`, `entity-header` 3종류의 헤더가 있고 필요한 헤더를 사용하면 됩니다.  
추가로 Response 에는 `general-header`, `response-heade`, `entity-header` 3가지이다. general, entity 는 Request와 동일합니다.

## ✔ Header 의 종류

-   General Header : Cache-Control, Connection, Date, Pragma, Trailer, Transfer-Enco, Upgrade, Via, Warning
-   Entity Header : Allow, Content-Encoding, Content-Language, Content-Length, Content-Location, Content-MD5, Content-Range, Content-Type, Expires, Last-Modified, extension-header
-   Request Header : Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match, If-Modified-Since, If-None-Match, If-Range, If-Unmodified-Since, Max-Forwards, Proxy-Authorization, Range, Referer, TE, User-Agent
-   Response Header : Accept-Ranges, Age, ETag, Location, Proxy-Authenticate, Retry-After, Server, Vary, WWW-Authenticate

## ✔ Reference

-   [HTTP 헤더](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)
-   [HTTP 요청/응답 스펙 간략히 이해하기](https://blog.outsider.ne.kr/888)
