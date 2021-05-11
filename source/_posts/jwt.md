---
title: jwt token
date: 2020-12-09 17:06:43
excerpt: jwt
tags: [token]
categories: 基础知识
position:
image_url:
---
##### jwt token主要包含以下三个内容：
- 头部 Header      Base64URL加密
- 载荷 Payload     Base64URL加密
- 签名 Signature    

```
{Header 头部}.{Payload 负载}.{Signature 签名}

签名计算方法：
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```
