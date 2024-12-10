---
title: 使用natmap实现内网访问
author: quizhi
date: 2024-12-10 11:23:00 +0800
categories: [Blogging]
tags: [natmap]

---

## 前言

本来有动态公网的电信宽带，突然收回了动态公网，并且也不给申请，在网上冲浪发现了natmap这个工具。
相关原理我没有去研究，反正这是一个打洞的工具，相关介绍请看作者介绍[Github](https://github.com/heiher/natmap)

看到这样的工具，正好符合我的目前情况的需求

## 安装

由于我是使用openwrt的软路由来拨号，所以按官方的说明直接指令安装即可
```
opkg install natmap luci-app-natmap
```

大部分的安装可以根据官方的示例来举一反三[Wiki](https://github.com/heiher/natmap/wiki)

这里主要讲述一下，官方教程中Cloudflare部分是怎么配置的

## Cloudflare

这里贴出一下官方教程中web示例部分的Cloudflare配置代码，在此基础上我加一下注释以便理解

更多的还是去看Cloudflare的api文档较好

```
#!/bin/sh

# 区域ID，可以在域名界面的右下角看到
ZONE=''
# DNS解析记录id，这个需要使用api来列出找到，下面会说怎么找到
RECORD=''
# 规则集ID，也是需要api来找到
RULE=''
# Cloudflare账号邮箱地址
EMAIL=''
# api令牌token
AUTH=''
# 域名，例如你想绑定的test.youdomain.com，那么这里就写test.youdomain.com
DOMAIN=''

ADDR=${1}
PORT=${2}

# DNS
while true; do
    curl -X PUT "https://api.cloudflare.com/client/v4/zones/${ZONE}/dns_records/${RECORD}" \
        -H "X-Auth-Email: ${EMAIL}" \
        -H "Authorization: Bearer ${AUTH}" \
        -H "Content-Type:application/json" \
        --data "{\"type\":\"A\",\"name\":\"${DOMAIN}\",\"content\":\"${ADDR}\",\"ttl\":60,\"proxied\":false}" > /dev/null 2> /dev/null
    if [ $? -eq 0 ]; then
        break
    fi
done

# Origin rule
while true; do
    curl -X PUT "https://api.cloudflare.com/client/v4/zones/${ZONE}/rulesets/${RULE}" \
        -H "Authorization: Bearer ${AUTH}" \
        -H "Content-Type:application/json" \
        --data "{\"rules\":[{\"expression\":\"(http.host eq \\\"${DOMAIN}\\\")\",\"description\":\"natmap\",\"action\":\"route\",\"action_parameters\":{\"origin\":{\"port\":${PORT}}}}]}" > /dev/null 2> /dev/null
    if [ $? -eq 0 ]; then
        break
    fi
done
```

针对上面说的两个需要api获取的id，我们需要使用cloudflare的api来获取

### DNS解析记录ID获取

根据cloudflare[文档](https://developers.cloudflare.com/api/operations/dns-records-for-a-zone-list-dns-records)，可以使用以下请求来获取所有的DNS记录信息
```
curl -X GET "https://api.cloudflare.com/client/v4/zones/${ZONE}/dns_records" \
    -H "X-Auth-Email: ${EMAIL}" \
    -H "Authorization: Bearer ${AUTH}" \
    -H "Content-Type:application/json" \
```

使用前，需在cloudflare网页控制台添加一个DNS记录，上面的请求代码需把对应变量替换成自己的，这些上面都有解释是什么

成功会返回json信息，可以在`result`字段中，按照你添加的DNS记录来找到，里面也会有个id字段，这个值就是我们要的东西

### 规则集ID获取

类同，可以这个cloudflare API[文档](https://developers.cloudflare.com/api/operations/listZoneRulesets)中找到相关信息
```
curl -X GET "https://api.cloudflare.com/client/v4/zones/${ZONE}/rulesets" \
    -H "X-Auth-Email: ${EMAIL}" \
    -H "Authorization: Bearer ${AUTH}" \
    -H "Content-Type:application/json" \
```

同样的，你需要在使用前去cloudflare域名控制台中`规则->Origin rule`中添加随意一个规则
同样，也是在成功返回的`result`字段中找到，一般会有多个，里面`phase`字段值是`http_request_origin`的就是了，记录这个的id


不过官方中的脚本中，DNS中的`\"proxied\":false`需改成`\"proxied\":true`
DNS记录没有Cloudflare代理的话，端口的转发不会生效

## 结束

配置好后，这样虽然可以直接访问域名地址不加端口来访问，但因为是有Cloudflare代理，国内的访问延迟会慢些，
算是美中的不足
