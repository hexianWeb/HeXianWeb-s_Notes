# 浅谈Cookie-session 、Jwt 两种身份认证机制

> 作者：何贤



*场景概述*

> 因为HTTP是无状态（stateless）协议：所以针对登陆后的场景，如果每次页面的跳转 重定向都需要一定的空间储存用户名以及密码，这会增大服务器的负担 、所以就引入了各种身份认证机制

## Cookie-session 机制

### Cookie传递过程



> 一种保存在浏览器的小型脚本 只能保存ASCII字符串，其他需要先进行编码

```
1. 用户在某一时间段第一次请求服务器时，得到的返回结果并不只是response，还包含cookie
2.这段小型文本会在下次请求时再次发送给客服端，进行身份表明，服务端就知道已经处理过用户的登录请求了
3.过期的Cookie会被删除
```



### Session 是什么

> 一中能够存取任何类型数据的文本、

### 基于 Cookie-Session 身份验证机制的过程
````
1. 用户输入登录信息
2. 服务端验证登录信息是否正确，如果正确就在服务器端为这个用户创建一个 Session，并把 Session 存入数据库
3. 服务器端会向客户端返回带有 sessionID 的 Cookie
4. 客户端接收到服务器端发来的请求之后，看见响应头中的 `Set-Cookie` 字段，将 Cookie 保存起来
5. 接下来的请求中都会带上这个 Cookie，服务器将 sessionID 和 数据库中的相匹配，如果有效则处理该请求
6. 如果用户登出，Session 会在客户端和服务器端都被销毁
```
### Session-Cookie 机制的缺陷
```
1. 扩展性不好，当拥有多台服务器的情况下，如何共享 Session 会成为一个问题，也就是说，用户第一个访问的时候是服务器 A，而第二个请求被转发给了服务器 B，那服务器 B 无法得知其状态。（举例来说，A 网站和 B 网站是同一家公司的关联服务。用户只要在其中一个网站登录，再访问另一个网站自动登录）
2. 安全性不好，攻击者可以利用本地 Cookie 进行欺骗和 CSRF 攻击。
3. Session 保存在服务器端，如果短时间内有大量用户，会影响服务器性能。
4. [跨域问题](https://juejin.im/post/5c23993de51d457b8c1f4ee1#heading-11)，Cookie 属于同源策略限制的内容之一。

```

## Jwt 机制

### Jwt组成

JWT 由三个部分组成：header、payload、signature 每个部分中间使用 `.` 来分隔，其中，header 和 payload 使用 Base64URL 进行编码：

```text
base64UrlEncode(header).base64UrlEncode(payload).signature
```

#### header

header 部分是一个 JSON 对象，用来描述 JWT 的元数据：

```text
{
  "typ": "JWT",   //  表示对象是一个 JWT
  "alg": "HS256"  //  表示使用哪种 Hash 算法来创建签名，这里是 HMAC-SHA256
}
复制代码
```

#### payload

payload 部分也是一个 JSON 对象，实际需要传递的数据被存放在这里。我们除了使用[官方提供的七个字段](https://en.wikipedia.org/wiki/JSON_Web_Token#Standard_fields)之外，也可以使用自定义的私有字段。

```text
{
  "sub": "title",
  "name": "Yeoman"
}
复制代码
```

**JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。**

### signature

signature 是对前两个部分的签名，防止数据被篡改。

```javascript
data = base64urlEncode( header ) + "." + base64urlEncode( payload );
signature = Hash( data, secret );
```

使用 Base64URL 编码的 header 和 payload 中间用 `.` 隔开，再使用 header 中指定的 Hash 算法，加上密钥对这个字符串进行 Hash 得到 signature

### [#](https://hytonightyx.github.io/fedoc/04-游览器与BOM/Cookie-Session ,JWT认证机制.html#工作流程)工作流程

1. 前端将自己的用户名和密码发送到后端的接口
2. 后端核对用户名和密码之后，将用户的一些信息作为 payload，生成 JWT
3. 后端将 JWT 作为登录成功的返回结果返回给前端。前端可以将其结果保存在 localStorage/sessionStorage 中，登出时删除 JWT 即可。（最好不要保存在 Cookie 中，用了 Cookie 就不能设置 HTTPonly，并且存在跨域问题）
4. 每一次请求都将 JWT 放在 HTTP 请求头中的 `Authorization` 位，这样相比放在 Cookie 中可以跨域。

```text
Authorization: Bearer <token>
```

1. 服务器解码 JWT，如果 token 有效，那么处理这个请求
2. 用户登出，在客户端删除 token 即可，与服务端无关

## [#](https://hytonightyx.github.io/fedoc/04-游览器与BOM/Cookie-Session ,JWT认证机制.html#jwt-特点)JWT 特点

1. JWT 默认是不加密的
2. JWT 的目的是用来验证来源可靠性，并不是保护数据和防止未经授权的访问。（可以类比成一张电影票，只能验证电影票是否是真的，电影票也有一些基本信息，但是他人也可以使用你的电影票，如果可能的话）一旦暴露，任何人都可以获得权限。为了减少盗用，JWT 的有效期应该设置得比较短，对于一些比较重要的权限，使用时应该再次对用户进行认证。
3. 最大的缺点是 token 过期处理问题，由于服务器不保存 Session 状态，因此无法在使用过程中废止或者更改权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。

**一个保存在浏览器端，一个保存在服务器端**

## 在Express中使用JWT

### 1.安装JWT相关的包

> 运行如下命令

```
npm install jsonwebtoken express-jwt
```

- jsonwebtoken 生产JWT字符串
- express-jwt用于解析JWT字符串

### 2.使用JWT模块

>  只需要引入即可

```js
// 引入JWT模块

const jwt = require("jsonwebtoken"); //生成jwt的中间件
const expressJwt = require("express-jwt"); //解析jwt的中间件

```

### 3.定义secret密钥

**保证JWT字符的安全性**

1. 生成时需要用到sercet密钥
2. 还原解析时，也需要sercet密钥

```js
const sercetKey = "<密钥内容>"
```

### 4.生成token

> 参数1：用户的信息对象
>
> 参数2：加密的密钥
>
> 参数3：配置对象，可以配置当前token的有效期

```js
     const tokenStr = jwt.sign({ username: userinfo.username }, sercetKey, {
      expiresIn: "30s",//小时单位h
    });
 
```

*随后利用res.send方法将token送出*

```js
    res.send({
      status: 200,
      msg: "登录成功",
      token: tokenStr,
    });
 
```



**结果展示**

```
{
    "status": 200,
    "msg": "登录成功",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImhleGlhbiIsImlhdCI6MTY0Mjg0MzExMSwiZXhwIjoxNjQyODQzNDcxfQ.L8h5EW0Sci0GbDS04SCj-vyB72BXG3UpnjotlYL1qlY"
}
```

### 5.解析token

```js
注册JWT字符解析还原成JSON对象的中间件
// 只要配置成功了express-jwt这个中间件，就可以将其在req.user中解析出来
// 注意 在配置过程中需要指定某种加密算法这里用的是hash
app.use(expressjwt({ secret: sercetKey, algorithms: ["HS256"] }));

```

 tips: JWT 属于明文形式   即使有着加密，但还是有可能暴力破解，所以不建议将密码写入JWT 的payload中
