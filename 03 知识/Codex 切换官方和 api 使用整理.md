---
title: Codex 切换官方和 api 使用整理
type: 知识
tags:
  - Codex
date created: 2026-05-15 16:55:22
modify: 2026-05-15 16:55:58
---
## 1. 切换后会话不见了怎么办

### 1.1 现象

切换官方账号、API、中转后，可能出现：

左侧历史会话为空  
旧线程不见了  
切回来又出现

### 1.2 原因

Codex 会把会话和 provider / 账号状态关联起来。当你切换了 `model_provider` 、Base URL、官方账号或自定义 provider 后，Codex 可能会显示另一组会话列表。

### 1.3 处理方法

一般不是删除了，而是“当前配置下看不到”。

可以尝试：

1. 切回原来的 CC Switch 配置  
2. 重启 Codex App  
3. 查看历史线程是否恢复

如果只是为了切 API，尽量不要频繁改 `model_provider` 名称。例如多个中转可以尽量保持同一个 provider 名称，只替换 `base_url` 、 `model` 或 Key。

---

## 2. Codex 一直 Reconnecting 的处理

### 2.1 典型现象

打开新会话后一直显示：

Reconnecting...  
stream disconnected before completion  
每次都要重连几次才开始回答

### 2.2 常见原因

新版 Codex 会优先尝试 WebSocket。如果当前代理、网络、中转站或反代环境不支持 WebSocket，就会反复重连，之后才回退到 HTTPS / SSE。

---

### 2.3 方法一：给 Codex 加代理环境变量

在 `.codex` 目录下新建 `.env` 文件：

Windows：

C:\Users\你的用户名\. codex\. env

macOS / Linux：

~/. codex/. env

写入：

HTTP_PROXY=http://127.0.0.1:7890  
HTTPS_PROXY=http://127.0.0.1:7890  
ALL_PROXY=http://127.0.0.1:7890  
NO_PROXY=localhost, 127.0.0.1,::1

或者在 PowerShell 里执行：

$env:HTTP_PROXY="http://127.0.0.1:7890"  
$env:HTTPS_PROXY="http://127.0.0.1:7890"  
$env:ALL_PROXY="http://127.0.0.1:7890"  
$env:NO_PROXY="localhost, 127.0.0.1,:: 1"

端口 `7890` 改成你自己的代理端口。例如 Clash / Mihomo / Clash Verge 里实际显示的 HTTP 端口。

---

### 2.4 方法二：禁用 WebSocket，强制走 HTTPS

打开：

~/. codex/config. toml

或 Windows：

C:\Users\你的用户名\. codex\config. toml

添加：

model_provider = "openai_https"  
  
[model_providers. openai_https]  
name = "OpenAI"  
wire_api = "responses"  
requires_openai_auth = true  
supports_websockets = false

保存后重启 Codex App。

注意：

- 这相当于新建了一个自定义 provider。
    
- 旧会话可能会暂时隐藏。
    
- 如果移除这段配置并切回原 provider，旧会话通常还能回来。
    
- `supports_websockets = false` 适合解决“每次固定 Reconnecting 多次”的问题。
    

---

## 3. CCSwitch 切换官方号和 API

### 2.1 切换官方号

新建一个 OpenAI Official 样例，其他都放空白。

打开 codex app 或者 cli，直接选择账号登陆，CCS 样例中自动填写。

然后再按照 2 进行修改

### 2.2 切换 API

新建 API 样例，填入 api_key 后样例自动填写。

然后再按照 2 进行修改

---

###