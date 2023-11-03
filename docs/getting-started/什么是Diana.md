---
title: 什么是Diana？
---

# 什么是Diana

在回答什么是Diana之前，需要弄清楚下面的一些概念：

* 认证
* 授权
* 凭证
* 访问控制

## 认证

认证（Authentication），即证明“我”是“我”的过程。一般以下方式：

* 账号和密码 
* 手机和邮箱验证码
* 生物特征 指纹和面容等


## 授权

授权（Authorization），即给一个用户权限来访问特点的资源。

## 凭证

凭证（Credentials），即用户完成认证和授权后的凭证。一般有两种方式：

* Cookie 浏览器（或者客户端）保存cookie，服务的维护session，是有状态的。
* JWT 一种令牌格式。通常放在Header中发送给服务端，是无状态的。


## 访问控制

访问控制（Access Control），即对用户访问系统进行控制。比如Linux文件系统的权限。
一般有两种方式：

* RBAC Role-based Access Control
* ABAC Attribution-based Access Control
* RBAC Rule-based Access Control


所以Diana是一个集合了认证、授权和访问控制的系统。提供以下功能：

* Oauth2.0和OIDC的实现
* CAS
* 访问控制

