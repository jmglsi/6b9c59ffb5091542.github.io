---
title: 给网站接入 oauth2
toc: true
date: 2023-04-09 15:14:36
categories: 随笔
tags:
- oauth2
---
正好前几天有小伙伴问我，那就记录一下吧。
<!--more-->

# 什么是 oauth2

这个解释的太多了，参考 [理解OAuth 2.0](https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)，就不再赘述了。

# 实战一下
> 其实有很多种模式，这里直接用最简单的方式 (1次跳转 + 2次请求)，用 GitHub 示例。

## 申请 oauth2 应用

[点击这里](https://github.com/settings/developers) 申请，按照提示填好。把 `Client ID` 和 `Client secrets` 记录一下，后续需要。

## 拼接授权的链接
> [GitHub 官方文档](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)

拼接完成大概是一个这样的链接：

``` shell
GET https://github.com/login/oauth/authorize?
  client_id=xxx&
  redirect_uri=xxx&
  scope=xxx&
  state=xxx

#client_id 上方记录的 Client ID
#redirect_uri 你填的回调地址，需要 url编码 一下
#scope 申请的权限，如果只是绑定，不是很建议申请太多
#state 防重放，可以md5一下时间戳
```

`前端` 重定向到这个链接，让用户授权。

## code 换取 accessToken

上一步用户授权允许以后，会把 `code` 回调给你填写的回调地址，比如我的回调地址是 `http://localhost/oauth2/github/callback`。

`后端` 发送以下请求：

``` shell
POST https://github.com/login/oauth/access_token
client_id=xxx&client_secret=xxx&code=xxx&redirect_uri=xxx

#client_id 上方记录的 Client ID
#client_secret 上方记录的 Client secrets
#code callback url 里的参数
#redirect_uri 你填的回调地址，需要 url编码 一下
```

如果需要返回的格式为 json，user-agent 需要带上 `Accept: application/json`。

## accessToken 获取用户信息

上一步拿到 `accessToken` 以后，就可以获取用户信息了。

`后端` 发送以下请求：

``` shell
GET https://api.github.com/user
```

user-agent 需要带上 `Authorization: Bearer xxx`，`xxx` 是上方的 `accessToken`，中间有个空格。

然后就能拿到用户信息了。到这里，算是接入成功了，剩下的就是自己的一些业务逻辑了。

# oauth2 整理
> 我站接了 10 几个，所以顺带整理了一下，[前往体验](https://pvp.91m.top/login)。

| 平台<img width=50/> | 开发文档<img width=25/>                                                                                    | 申请地址<img width=25/>                                                                       | 注意事项                                             |
|---------------------|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|------------------------------------------------------|
| 爱发电              | [开发文档](https://afdian.net/p/010ff078177211eca44f52540025c377)                                          | [私信站长](https://afdian.net/message/27f7cea2370d11e8ae8852540025c377)                       | 告知站长申请 `oauth2`                                |
| 百度                | [开发文档](https://developer.baidu.com/wiki/index.php?title=docs/oauth/application)                        | [网页创建](https://developer.baidu.com/console#app/project)                                   | 创建以后无法删除，刷新不了验证码                      |
| coding              | [开发文档](https://coding.net/help/openapi#oauth)                                                          | [网页创建](https://jmglsi.coding.net/user/account/setting/applications)                       | `jmglsi` 改成自己的团队                              |
| discord             | [开发文档](https://discord.com/developers/docs/topics/oauth2)                                              | [网页创建](https://discord.com/developers/applications)                                       |                                                      |
| gitee               | [开发文档](https://gitee.com/api/v5/oauth_doc)                                                             | [网页创建](https://gitee.com/oauth/applications)                                              |                                                      |
| github              | [开发文档](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)          | [网页创建](https://github.com/settings/developers)                                            |                                                      |
| google              | [开发文档](https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow?hl=zh-cn)      | [网页创建](https://console.cloud.google.com/apis/credentials)                                 |                                                      |
| microsoft           | [开发文档](https://learn.microsoft.com/zh-cn/azure/active-directory/develop/v2-oauth2-auth-code-flow)      | [网页创建](https://portal.azure.com/#view/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) | 创建的时候必须选择 `任何组织目录`，否则其他人没法授权 |
| qq                  | [开发文档](https://wiki.connect.qq.com/%e4%bd%bf%e7%94%a8authorization_code%e8%8e%b7%e5%8f%96access_token) | [网页创建](https://connect.qq.com/manage.html)                                                |                                                      |
| telegram            | [开发文档](https://core.telegram.org/widgets/login#widget-configuration)                                   | [网页创建](https://core.telegram.org/widgets/login#widget-configuration)                      | 返回到 `/#/` 锚点，得特殊处理下                       |
| twitter             | [开发文档](https://developer.twitter.com/en/docs/authentication/oauth-2-0/user-access-token)               | [网页创建](https://developer.twitter.com/en/portal/dashboard)                                 | 最傻b的，下面会说                                     |
| weibo               | [开发文档](https://open.weibo.com/wiki/Oauth2/authorize)                                                   | [网页创建](https://open.weibo.com/apps)                                                       |                                                      |
| yuque               | [开发文档](https://www.yuque.com/yuque/developer/authorizing-oauth-apps)                                   | [网页创建](https://www.yuque.com/settings/applications)                                       |                                                      |

# 注意事项
> 因为一些你懂都懂的原因，后端访问国外的 oauth2 体验并不佳 (除非你的服务器在国外)，可能需要服务器上挂一个代理。

## twitter 的第一步
> 跳转到授权页面

跳转链接后面需要加上 `&code_challenge=challenge&code_challenge_method=plain` 否则必定错误。

## twitter 的第二步
> 获取 accessToken

POST内容需要加上 `&code_verifier=challenge`，user-agent 需要加上 `Authorization: Basic xxx`，`xxx` 需要 `base64_encode("{$client_id}:{$client_secret}")` 否则必定报错。

