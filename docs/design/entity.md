---
title: 实体
---

# 实体

从具体的场景来思考系统可能涉及到的实体。系统主要是实现认证和授权两个功能。

## 授权场景

用户提供身份信息想系统证明他是他声明的用户，

* 用户
  * 用户
  * 用户组
  * 应用的授权
  * 组织和部门
* 权限
  * 权限空间
  * 角色
  * 资源
  * Permission
* 应用
  * OAuth2.0和OIDC协议中的应用的概念
  * 授权访问应用的用户
  * 应用的资源

用户，设计的场景是登录和访问控制。

* 登录 用户是否可以登录用户，是否可以强制退出登录


APP添加主体

是从namespace获取用户、组、角色、组织的数据。
