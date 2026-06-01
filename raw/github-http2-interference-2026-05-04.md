---
title: GitHub HTTP/2 干扰问题（2026-05-04）
created: 2026-05-04
updated: 2026-05-04
type: log
tags: [network, github, troubleshooting, linux, curl, git]
date: 2026-05-04
confidence: medium
---

# GitHub HTTP/2 干扰问题

## 现象

- `curl https://github.com` 超时（HTTP 000），但 `wget https://github.com` 正常
- `curl https://api.github.com` 正常
- `openssl s_client` TLS 握手有时超时有时成功（不稳定）
- 7897 代理对 github.com 也超时

## 根因

**HTTP/2 协议层在这台机器上被间歇性干扰，HTTP/1.1 正常。**

| 测试方式 | 协议 | 结果 |
|----------|------|------|
| curl 默认 | HTTP/2 (ALPN h2) | ❌ 超时 |
| curl --no-alpn | HTTP/1.1 | ✅ 200 OK (1.3s) |
| wget | HTTP/1.1 | ✅ 200 OK |
| openssl s_client | TLS 握手 | ⚠️ 不稳定 |

## 解决方案

```bash
# 强制 Git 全局走 HTTP/1.1
git config --global http.version HTTP/1.1
git config --global https.version HTTP/1.1
```

验证：`git ls-remote` 日志显示 `using HTTP/1.x`，git clone 恢复正常。

## 关键发现

- `curl -v` 显示 ALPN 协商成功（server accepted h2），但 HTTP/2 数据传输阶段卡死
- wget 默认不用 ALPN，直接走 HTTP/1.1，所以一直正常
- 之前 `curl -sL` 下载 tarball 成功是因为那个命令恰好触发了 HTTP/1.1 路径
- 与 Clash Verge 配置无关，是本机出口路由/ISP 对 HTTP/2 over TLS 到 github.com 的干扰

## 教训

用 `curl --no-alpn` 快速诊断 HTTP/2 问题，比 strace/traceroute 更快定位协议层干扰。
