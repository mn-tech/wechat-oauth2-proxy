# wechat-oauth2-proxy

## 为什么要有这个项目？
在微信中进行网页授权时，会调用如下地址：

```
https://open.weixin.qq.com/connect/oauth2/authorize?appid=APP_ID&redirect_uri=REQEUST_URI&response_type=code&scope=SCOPE_TYPE&state=STATE#wechat_redirect
```

在微信公众号后台中，只能填写唯一的网页授权回调域名（也就是上述 REQUEST_URI 中的域名部分）。这种限制对于多个公众号的开发带来很多麻烦：
+ 需要申请多个公众号
+ 需要维护多个网页授权回调域名

wechat-oauth2-proxy 的出现是为了破除这种限制。

## 部署指南
### 需求
+ Node >= 7.6.0

### 环境变量
该应用使用环境变量控制应用运行状态。

| 环境变量 | 默认值 | 说明 |
| ------------ | ------------- | -------- |
| `PROXY` | `true` | 是否信任反向代理的代理头 |
| `LISTEN_HOST` | `0.0.0.0` | 监听地址 |
| `LISTEN_PORT` | `3000` | 监听端口 |

### 网页授权验证文件
对于形如 `MP_verify_xxxxxxxx.txt` 的网页授权验证文件，只需将其存储到 `public` 目录，即可完成验证。

## 使用指南
为了不引入额外的复杂度，依旧使用正常的微信网页授权地址：

```
https://open.weixin.qq.com/connect/oauth2/authorize?appid=APP_ID&redirect_uri=REQEUST_URI&response_type=code&scope=SCOPE_TYPE&state=STATE#wechat_redirect
```

唯一的不同点在 `REQUEST_URI` 参数。

举个例子，假设：
+ wechat-oauth2-proxy 部署在 `https://op.example.com`
+ 回调地址为 `https://mall.example.com/user/profile?fields=username,age,email`

在使用 wechat-oauth2-proxy 前，REQUEST_URI 为：

```
// encodeURIComponent('https://mall.example.com/user/profile?fields=username,age,email')
https%3A%2F%2Fmall.example.com%2Fuser%2Fprofile%3Ffields%3Dusername%2Cage%2Cemail
```

在使用 wechat-oauth2-proxy 后，REQUEST_URI 为：

```
// encodeURIComponent('https://op.example.com?redirect=https://mall.example.com/user/profile?fields=username,age,email')
https%3A%2F%2Fop.example.com%3Fredirect%3Dhttps%3A%2F%2Fmall.example.com%2Fuser%2Fprofile%3Ffields%3Dusername%2Cage%2Cemail
```

## 细节
以使用指南中的情境为例，在用户授权后，微信服务器将 code 和 state 添加到回调地址，并发起如下调用：

```
GET https://op.example.com?redirect=https://mall.example.com/user/profile?fields=username,age,email&code=CODE&state=STATE
```

wechat-oauth2-proxy 对 Query String 进行处理，重定向到：

```
https://mall.example.com/user/profile?fields=username,age,email&code=CODE&state=STATE
```

## 其他类似项目
+ [HADB/GetWeixinCode](https://github.com/HADB/GetWeixinCode)

## LICENSE
MIT
