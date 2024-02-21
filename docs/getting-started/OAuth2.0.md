---
title: OAuth 2.0
summary: 关于OAuth 2.0的简单介绍
---

说到认证和授权就离不开Oauth2.0协议。RFC地址：[The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749)。这里简单介绍一下Oauth2.0的四种授权模式及其使用场景。

# 授权模式


## 授权码模式
![授权码模式](../img/authorization-code.png)

### 基本流程

（A） 客户端通过将资源所有者的用户代理定向到授权端点。请求包括client_id, scope(描述被访问的资源), redirect_uri(即Client的URI), state(用于抵制CSRF攻击)。

（B） 授权服务器（通过用户代理）验证资源所有者，并确定资源所有者是批准还是拒绝客户端的访问请求。（approval_prompt=force时授权服务器提供一个交互页面，如果没有这个参数则默认是批准）

（C） 假设资源所有者授予访问权限，授权服务器使用前面提供的redirect_uri（在请求中或在客户端注册期间）将用户代理重定向回客户端。重定向URI包括授权代码authorization_code 和客户端提供的state。

（D） 客户端通过包括在上一步中接收到的授权代码authorization_code ，从授权服务器的令牌端点请求访问令牌。发出请求时，客户端通过授权服务器进行身份验证。客户端包括用于获取验证授权代码时的redirect_uri。

（E） 授权服务器验证客户端，验证授权代码authorization_code ，并确保接收到的redirect_uri与步骤（C）中用于重定向客户端的URI匹配。如果有效，授权服务器将使用访问令牌和刷新令牌（可选）进行响应。


### 使用场景

授权码模式适合有后端服务器的场景。需要能够安全存储客户端的密钥，用于置换token


### 端点请求示例

#### authorize端点

**request**
> 参数说明：
>
> response_type：必填。值必须为“code”
>
> client_id：必填。客户端的身份标识
>
> redirect_uri：选填。重定向uri
>
> scope：选填。访问请求的范围。
>
> state：不必填，但是推荐。是请求和回调之间的状态，用于防止csrf攻击。

```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
    Host: server.example.com
```

**response**
> 参数说明：
>
>code：必填。授权服务器生成的授权码，后面用于置换token
>
>state：如果request填了则为必填。值为客户端传的值。

```
HTTP/1.1 302 Found
     Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz
```

#### token端点

**request**
> 参数说明：
>
>grant_type：必填。值必须为“authorization_code”
>
>code：必填。授权后返回的授权码
>
>redirect_uri：必填。重定向uri，如果授权请求是填过，值必须是授权请求时所填的值
>
>client_id：必填。客户端的身份标识

```
POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
     &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```

**response**
```
HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }
```


## 隐式模式

***应用不需要用code换token，无需请求/token端点，直接通过认证端点返回token。***

![隐式模式](../img/implicit.png)

### 基本流程

(A). 客户端通过将资源所有者的用户代理定向到授权端点。

(B). 授权服务器进行认证资源所有者身份，并确定资源所有者是否批准客户端的请求。

(C). 认证通过重定向到回调地址，重定向URI包括token

(D). user-agent会遵循重定向指令，向基于web的客户端发起请求，frament由user-agent自行处理。

(E).基于web的客户端返回一个内置脚本的html页面，该页面可以访问这个回调地址，并且可以从frament中取出token

(F). 用户代理执行脚本

(G).将访问令牌传送给客户端

### 使用场景

简化流程，适用于前端应用（如单页应用），但安全性较低。

### 端点请求示例

#### authorize端点

**request**

> 参数说明：
>
> response_type：必填。值必须为“token”
>
> client_id：必填。客户端的身份标识
>
> redirect_uri：选填。重定向uri
>
> scope：选填。访问请求的范围。
>
> state：不必填，但是推荐。是请求和回调之间的状态，用于防止csrf攻击。

```
GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
    Host: server.example.com
```

**response**

> 参数说明：
>
> access_token：必填。授权服务器生成的访问令牌。
>
> token_type：必填。生成的访问令牌的类型，不区分大小写。bearer和mac。
>
> expires_in：推荐。访问令牌的过期时间。
>
> scope：如果和客户端选择的范围相同是可选的。否则，必填。
>
> state：如果request填了则为必填。值为客户端传的值。

```
HTTP/1.1 302 Found
     Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA
               &state=xyz&token_type=example&expires_in=3600
```


## 资源拥有者密码凭证模式

![资源拥有者密码凭证](../img/password.png)

### 基本流程

（A） 资源所有者向客户端提供其用户名和密码。

（B） 客户端将从资源所有者接收的凭据，发送到授权服务器的令牌端点请求访问令牌。

（C） 授权服务器验证客户端并验证资源所有者凭据，如果有效，则发出访问令牌。

### 使用场景

在特别信任的环境下使用，确保环境安全，否则会直接泄露用户的账号和密码。交互应用不推荐使用。推荐在其他模式都不满足需求时再考虑使用该模式。

### 端点请求示例

#### token端点

**request**

> 参数说明：
>
> grant_type：必填。值必须为“password”
>
> username：必填。用户的账号。
>
> password：必填。用户的密码。
>
> scope：选填。访问请求的范围。

```
POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=password&username=johndoe&password=A3ddj3w
```

**response**
```
HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }
```


## 客户端模式

![客户端模式](../img/client-credentials.png)

### 基本流程

（A） 客户端通过授权服务器进行身份验证，并从令牌端点请求访问令牌。

（B） 授权服务器对客户端进行身份验证，如果有效，则发出访问令牌。

### 端点请求示例

#### token端点

**request**

> 参数说明：
>
> grant_type：必填。值必须为“client_credentials”
>
> scope：选填。访问请求的范围

```
POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=client_credentials
```

**response**
```
HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "example_parameter":"example_value"
     }
```

客户端可以直接使用`client_id`和`client_screct`获取令牌。在微软的文档中提到了具体的使用场景：

> This type is commonly used for server-to-server interactions that must run in the background, without immediate interaction with a user, and is often referred to as daemons or service accounts.
> 
> Form [Microsoft identity platform and the OAuth 2.0 client credentials flow](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-client-creds-grant-flow)

所以一般M2M（机器间）授权使用这种模式。
