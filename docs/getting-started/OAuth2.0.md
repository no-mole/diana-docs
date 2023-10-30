---
title: OAuth 2.0
summary: 关于OAuth 2.0的简单介绍
---

说到认证和授权就离不开Oauth2.0协议。RFC地址：[The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749)。这里简单介绍一下Oauth2.0的四种授权模式及其使用场景。

# 授权模式


## 授权码模式

## 隐式模式

## 资源拥有者密码凭证模式

## 客户端模式

客户端可以直接使用`client_id`和`client_screct`获取令牌。在微软的文档中提到了具体的使用场景：

> This type is commonly used for server-to-server interactions that must run in the background, without immediate interaction with a user, and is often referred to as daemons or service accounts.
> 
> Form [Microsoft identity platform and the OAuth 2.0 client credentials flow](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-client-creds-grant-flow)

所以一般M2M（机器间）授权使用这种模式。
