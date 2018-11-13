# DID需求整理


## DID单点登录

1. 用户输入网址进入第三方网站点击登陆按钮
2. 服务端生成包含如下信息的二维码传到网页通过UUID发起长链接
```json
{
	destination: URL, // server handle url
	action: LOGIN,
	uuid: UUID // server random and cache
}
```
3. 用户用手机扫描二维码并将如下信息打包进JWT进行ES256k签名
```json
{
	time: expiration, // ten seconds cycle
	did: DID, // user did
	destination: URL, // server handle url
	action: LOGIN,
	uuid: UUID
}
```
4. 用户用手机将已签名的JWT发向destination(server handle url)
5. 服务端计算出公钥通过用户DID Document进行验证并将[UUID/DID]键值对存入缓存
6. 网页长链接检测到[UUID/DID]键值对成功登陆并删除键值对


## DID单点授权

1. 用户输入网址进入第三方网站选择相应的服务
2. 服务端生成包含如下信息的二维码或返回如下信息至网页
```json
{
	destination: URL, // server handle url
	action: CRUD, // server operation for jwt
	jwt-iss: DID, // jwt issuer
	jwt-aud: DID, // jwt audience
	jwt-sub: STRING // jwt subject
}
```
4. 手机和网页将以上信息用人类可读方式展现给用户
5. 提醒用户确认以上信息正确性之后将如下JWT进行ES256k签名
```json
{
	time: expiration, // ten seconds cycle
	did: DID, // user did
	destination: URL, // server handle url
	action: CRUD, // server operation for jwt
	jwt-iss: DID, // jwt issuer
	jwt-aud: DID, // jwt audience
	jwt-sub: STRING // jwt subject
}
```
6. 用户签名之后将JWT发向destination(server handle url)并建立websocket
7. 第三方服务端转发已签名的JWT到verifiable credentials服务器
8. verifiable credentials服务端计算出公钥通过用户DID Document验证JWT的签名有效性
9. verifiable credentials服务端验证JWT的时间有效性
10. verifiable credentials服务端验证用户DID的权限有效性
11. verifiable credentials服务端验证成功之后执行或返回第三方网站的请求
12. 第三方服务端执行上层应用的业务逻辑


## DID verifiable credentials

可验证凭证采用JWT形式实现，参数说明如下：

* `iss`: jwt签发者（必须）
* `sub`: jwt主题（必须）
* `aud`: 接收jwt的一方（必须）
* `exp`: jwt的过期时间，这个过期时间必须要大于签发时间（必须）
* `nbf`: 定义在什么时间之前，该jwt都是不可用的（可选）
* `iat`: jwt的签发时间（可选）
* `jti`: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击（可选）
* `net`: did的网络标示（必须，默认eth_ropsten）
* `ipfs`: jwt合规说明书链接（可选，默认存储在IPFS上）
* `context`: jwt上下文（可选，由上层应用或业务逻辑决定）

签名算法说明如下：

* `ES256k`
* `eth_sign`


## DID verifiable credentials Server

DID verifiable credentials Server 帮助用户存储和管理JWT

* JWT CRUD 权限配置
* JWT CRUD Handler
* JWT 数据库结构
* JWT 缓存
* 用户操作记录
* 系统日志
* 通知系统
* 人类可读转换组件
* DID解析组件
* IPFS解析组件





























