---
title: Mac上部署OpenClaw流程记录
date: 2026-02-23 21:31:00
categories: 
  - AI
  - OpenClaw
  - 阿里云
tags:
  - OpenClaw
  - 阿里云PAI
  - API Key
  - Node.js
  - TLS证书问题
---

# Mac上部署OpenClaw流程记录

最近尝试了使用阿里云PAI的API来部署使用 [OpenClaw](https://github.com/openclaw/openclaw) 这个开源的AI个人助理框架。过程中，遇到了一些有趣的问题，特别是关于TLS证书验证的"Connection error"，最终通过设置环境变量 `NODE_TLS_REJECT_UNAUTHORIZED` 成功解决。本文将详细记录整个安装、部署和测试的完整过程，希望能帮助遇到同样问题的朋友。

## 什么是OpenClaw？

OpenClaw是一个开源的AI个人助理框架，它允许你：
- 创建个性化的AI助手
- 集成多种AI模型提供商（包括阿里云PAI）
- 通过各种渠道（Web、Telegram、WhatsApp等）与助手交互
- 扩展功能通过插件和技能系统

## 前提条件

在开始之前，请确保已经准备好以下内容：

1. **阿里云账号** 
2. **PAI服务已开通** - 在阿里云控制台中开通PAI服务
3. **API Key** - 从阿里云PAI控制台获取API Key
4. **Node.js环境**
5. **Git** - 用于克隆OpenClaw仓库

## Step 1: 安装OpenClaw

首先，我们需要安装OpenClaw：

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```
参考 https://awesome.tryopenclaw.asia/docs/01-basics/02-installation.html

## Step 2: 初始化OpenClaw工


```bash
openclaw onboard
# 选择 `QuickStart` 
# 参考阿里云文档进行配置：https://help.aliyun.com/zh/model-studio/openclaw#faq-model-not-found
```

完成后，会在主目录创建一个 `.openclaw` 文件夹，包含所有配置文件。

## Step 3: 配置阿里云PAI API Key

现在我们需要配置阿里云PAI的API凭证。OpenClaw支持多种AI模型提供商，我们需要在配置文件中指定使用阿里云PAI。

### 3.1 创建配置文件

在 `.openclaw` 目录中创建或编辑 `openclaw.json` 文件，我的配置文件如下：

```json
{
  "meta": {
    "lastTouchedVersion": "2026.2.15",
    "lastTouchedAt": "2026-02-16T13:04:09.838Z"
  },
  "wizard": {
    "lastRunAt": "2026-02-16T13:04:09.832Z",
    "lastRunVersion": "2026.2.15",
    "lastRunCommand": "configure",
    "lastRunMode": "local"
  },
  "models": {
    "mode": "merge",
    "providers": {
      "bailian": {
        "baseUrl": "https://dashscope.aliyuncs.com/compatible-mode/v1",
        "apiKey": "sk-your-key",
        "api": "openai-completions",
        "models": [
          {
            "id": "qwen3-max-2026-01-23",
            "name": "qwen3-max-2026-01-23",
            "reasoning": false,
            "input": [
              "text"
            ],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 262144,
            "maxTokens": 65536
          },
          {
            "id": "qwen3-coder-plus",
            "name": "qwen3-coder-plus",
            "reasoning": false,
            "input": [
              "text"
            ],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 131072,
            "maxTokens": 32768
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "bailian/qwen3-max-2026-01-23"
      },
      "models": {
        "bailian/qwen3-max-2026-01-23": {
          "alias": "qwen3-max-thinking"
        }
      },
      "workspace": "/path/to/.openclaw/workspace",
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  },
  "messages": {
    "ackReactionScope": "group-mentions"
  },
  "commands": {
    "native": "auto",
    "nativeSkills": "auto"
  },
  "gateway": {
    "port": 18788,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "your_token"
    },
    "tailscale": {
      "mode": "off",
      "resetOnExit": false
    }
  }
}
```

## Step 4: 解决TLS证书验证问题

在配置完成并启动gateway之后后，我遇到了一个恶心的问题，和模型交互时一直报错：**"Connection error"**。我通过以下命令反复重启Gateway Server都无法解决问题：
```bash
openclaw gateway install
openclaw gateway stop
openclaw gateway restart
```

最后经过排查，我发现这是由于Node.js的TLS证书验证机制导致的。

### 4.1 问题分析

我构造了一个node js程序来判断是否能直接用js来访问阿里云模型:
```javascript
test-openclaw.js
const { OpenAI } = require('openai'); // 确保安装了 openai 包: npm install openai

// 手动硬编码配置，完全绕过环境变量
const client = new OpenAI({
  apiKey: 'sk-your-key',
  baseURL: 'https://dashscope.aliyuncs.com/compatible-mode/v1',
});

async function test() {
  try {
    console.log('正在请求模型列表...');
    const list = await client.models.list();
    console.log('✅ 成功！获取到的模型:', list.data[0].id);
  } catch (error) {
    console.error('❌ 报错详情:', error.message);
    if (error.cause) {
      console.error('底层原因:', error.cause);
    }
  }
}

test();

```
但运行时发现遇到了nodejs的SSL证书相关报错

```
node test-openclaw.js
报错详情: Connection error.
底层原因: [TypeError: fetch failed] {
  [cause]: Error: unable to get local issuer certificate
      at TLSSocket.onConnectSecure (node:internal/tls/wrap:1648:34)
      at TLSSocket.emit (node:events:508:20)
      at TLSSocket._finishInit (node:internal/tls/wrap:1094:8)
      at ssl.onhandshakedone (node:internal/tls/wrap:880:12) {
    code: 'UNABLE_TO_GET_ISSUER_CERT_LOCALLY'
  }
}
```
可以判定是这里的SSL鉴权有问题。

### 4.2 解决方案

根据我在 [GitHub Issue #10806](https://github.com/openclaw/openclaw/issues/10806) 中的详细记录，解决方案是设置环境变量 `NODE_TLS_REJECT_UNAUTHORIZED=0`。
```bash
create ~/.openclaw/.env
add the line NODE_TLS_REJECT_UNAUTHORIZED=0
```

**重要警告**：这个设置会禁用TLS证书验证，**仅在开发和测试环境中使用**。在生产环境中，应该正确配置证书而不是禁用验证。

以及注意openclaw在启动gateway进程时不尊重我们的sys env，我们必须要在 ~/.openclaw/.env中进行设置。


## Step 5: 启动和测试OpenClaw

现在启动OpenClaw并进行测试：

```bash
openclaw gateway restart
```

### 5.1 Web界面测试

打开浏览器访问 `http://localhost:18789`，你应该能看到OpenClaw的Web界面。

### 5.2 命令行测试

你也可以通过命令行直接与OpenClaw交互：

```bash
openclaw tui
## 可以和模型进行对话了
```




## 参考资料
- [教程1](https://awesome.tryopenclaw.asia/docs/01-basics/02-installation.html)
- [OpenClaw官方文档](https://docs.openclaw.ai/gateway/troubleshooting#gateway-service-not-running)
- [阿里云PAI文档](https://help.aliyun.com/zh/model-studio/openclaw)
- [GitHub Issue #10806 - TLS证书问题讨论](https://github.com/openclaw/openclaw/issues/10806)

---

*本文基于实际部署经验编写，如有疑问或发现错误，欢迎联系作者。*