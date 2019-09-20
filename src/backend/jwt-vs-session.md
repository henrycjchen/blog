# Session vs JWT
常见的认证授权方式有两种：session-cookie 和 JWT

## Session
session 的流程:
1. 前端将用户名及密码发送给 server
2. server 会生成身份认证相关的 session 数据，并生成对应的 id
3. server 将 id 作为 cookie 返回给用户，并记录在浏览器
4. 用户后面每次请求，都会带上 cookie，server 由此获取用户身份相关 session

在单服务器情况下，这个方案是极好的。

但当是多服务集群的时候，为了保证每台服务器都能获取到 session，这时候将需要将 session 进行持久化，保存在如 redis 下。

还有另外一种不需要持久化的方案，即下面要介绍的 JWT

## JWT（JSON Web Tokens）
JWT 是用户登录后由服务端生成的 token，数据结构为：`Header.Payload.Signature`，含义如下：
- Header：声明签名的算法和当前 token 的类型，默认值为：`{ "alg": "HS256", "typ": "JWT" }`，并由 Base64URL 转成字符串
- Payload：当前 token 所携带的用户信息，由 Base64URL 转成字符串
- Signature：针对前两部分的签名，使用存储在服务器的密钥 secret 进行防篡改签名，默认公式：`HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)`

### JWT 使用方式
客户端拿到服务端返回的 JWT 后，可以存储在 cookie 或 localStorage 里，并在后面的请求中由 cookie 或请求头 `Authorization` 带到服务端

JWT 与 Session 的不同点在于，JWT 是将用户信息存储在用户浏览器，而 Session 是将用户信息存储在服务器内存，因此，即使在集群下，服务器也能通过解析 JWT 拿到用户信息，而不用查询数据库获取 session

### JWT 的缺点
同由于 JWT 存储在浏览器，因此也带来了一定的风险：
1. 由于 payload 是 base64 加密的，因此过于敏感的信息，不宜放在 JWT 中
2. JWT 一旦签发了，在过期之前就一直有效，因此需要有另外的机制（黑名单措施）来处理这个问题

## 参考资料：
1. [《JSON Web Token 入门教程》](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)，阮一峰，2018年7月23日

