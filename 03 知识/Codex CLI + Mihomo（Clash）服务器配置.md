---
title: Codex CLI + Mihomo（Clash）服务器配置教程
type: 知识
tags:
  - Codex
  - AI
date created: 2026-04-16 23:57:10
modify: 2026-04-16 23:59:54
---
## 一、为什么需要代理  
  
很多 GPU 服务器（国内云服务器）无法直接访问 OpenAI API，所以 Codex CLI 会出现：  
  
- 一直卡住不回答  
- 网络超时  
- 无法登录  
  
网络结构如下：  
  
服务器 → OpenAI API ❌  
  
解决方法是：  
  
服务器 → Mihomo（Clash 内核代理）→ 机场节点 → Internet → OpenAI API  
  
因此我们需要：  
  
1. 在服务器安装 Mihomo  
2. 导入 Clash 订阅  
3. 设置系统代理  
4. 复制 Codex 登录信息  
  
---  
  
## 二、安装 Mihomo  
  
下载 Mihomo：  
   
wget https://github.com/MetaCubeX/mihomo/releases/download/v1.19.21/mihomo-linux-amd64-compatible-v1.19.21.gz

解压：

gunzip mihomo-linux-amd64-compatible-v1.19.21.gz

重命名：

mv mihomo-linux-amd64-compatible-v1.19.21 mihomo

添加执行权限：

chmod +x mihomo

测试：

./mihomo -v

如果看到版本号说明安装成功。

---

## 三、创建配置目录

mkdir -p ~/.config/mihomo

---

## 四、导入 Clash 订阅

下载你的订阅配置：

wget -O ~/.config/mihomo/config.yaml "你的订阅链接"

说明：

这个订阅链接通常来自你的机场。

---

## 五、启动 Mihomo

运行：

./mihomo -d ~/.config/mihomo

如果成功会看到：

Mixed(http+socks) proxy listening at: 127.0.0.1:7890

说明服务器代理端口是：

127.0.0.1:7890

---

## 六、设置服务器代理

设置环境变量：

export http_proxy=http://127.0.0.1:7890  
export https_proxy=http://127.0.0.1:7890

测试：

curl https://api.openai.com

如果返回：

Welcome to the OpenAI API

说明服务器已经可以访问 OpenAI。

---

## 七、后台运行 Mihomo

为了防止关闭终端代理停止：

nohup ~/mihomo -d ~/.config/mihomo > ~/mihomo.log 2>&1 &

检查：

ps aux | grep mihomo

如果看到 mihomo 进程说明成功。

---

## 八、开机自动启动代理

把代理写入 `~/.bashrc`：

echo 'nohup ~/mihomo -d ~/.config/mihomo > ~/mihomo.log 2>&1 &' >> ~/.bashrc

---

## 九、自动设置代理

写入：

echo 'export http_proxy=http://127.0.0.1:7890' >> ~/.bashrc  
echo 'export https_proxy=http://127.0.0.1:7890' >> ~/.bashrc

立即生效：

source ~/.bashrc

以后登录服务器自动使用代理。

---

## 十、复制 Codex 登录信息

如果服务器无法登录 Codex，需要从本地复制登录凭证。

本地目录：

~/.codex/

复制两个文件：

- `auth.json`
- `config.toml`

复制到服务器：

~/.codex/

例如：

scp ~/.codex/auth.json server:~/.codex/  
scp ~/.codex/config.toml server:~/.codex/

---

## 十一、运行 Codex

运行：

codex

然后输入问题，例如：

write a python quicksort

如果可以回答说明配置成功。

---

## 十二、验证网络

测试 OpenAI：

curl https://api.openai.com

测试 GitHub：

curl https://github.com

如果都能访问说明代理正常。

---

## 十三、最终服务器结构

服务器  
├── mihomo 代理  
│   └── 127.0.0.1:7890  
├── 系统代理  
│   └── http_proxy / https_proxy  
├── Codex CLI  
│   └── ~/.codex/auth.json  
└── 外网访问  
    ├── OpenAI  
    ├── GitHub  
    └── HuggingFace

---

## 十四、以后使用流程

以后只需要：

1. SSH 登录服务器
2. 直接运行：

codex

代理会自动启动。

---

## 十五、常见问题

### 1. Codex 不回答

检查：

curl https://api.openai.com

如果失败说明代理没有启动。

### 2. Mihomo 没有运行

检查：

ps aux | grep mihomo

如果没有进程：

./mihomo -d ~/.config/mihomo

### 3. 订阅失效

重新下载：

wget -O ~/.config/mihomo/config.yaml "订阅链接"