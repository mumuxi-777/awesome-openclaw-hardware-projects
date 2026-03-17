# ESPClaw: 在 $2 芯片上运行 AI Agent

> 纯 C 语言实现的 ESP32 AI 助手固件，20 个工具 + 10 个通知通道，无需 PSRAM 即可运行

- **Author:** @tianrking
- **GitHub:** [tianrking/espclaw](https://github.com/tianrking/espclaw)
- **License:** MIT
- **Status:** Active Development (v0.1.0)

![License](https://img.shields.io/badge/License-MIT-yellow)
![Language](https://img.shields.io/badge/Language-C-blue)
![ESP-IDF](https://img.shields.io/badge/ESP--IDF-5.5-red)
![Tools](https://img.shields.io/badge/Tools-20-orange)
![Channels](https://img.shields.io/badge/Channels-10-purple)

---

<p align="center">
  <img src="media/wechat_group.JPG" width="180" alt="WeChat Group QR Code">
</p>

## 目录

- [项目简介](#项目简介)
- [Demo 演示](#demo-演示)
- [功能特性](#功能特性)
- [支持的硬件](#支持的硬件)
- [快速开始](#快速开始)
- [LLM 配置](#llm-配置)
- [MQTT 集成](#mqtt-集成)
- [串口命令](#串口命令)
- [WiFi 注意事项](#wifi-注意事项)
- [使用示例](#使用示例)
- [架构设计](#架构设计)
- [为什么选择 ESPClaw](#为什么选择-espclaw)

---

## 项目简介

**ESPClaw** 是一个基于 ESP-IDF 5.5 开发的纯 C 语言 AI 助手固件。它能在仅售 $2 的 ESP32-C3/C5/S3 微控制器上运行完整的 ReAct Agent，让开发者以极低的成本构建智能物联网设备。

### 核心亮点

| 指标 | 数值 |
|------|------|
| 代码量 | 8,478 行纯 C |
| 二进制大小 | ~920KB |
| 外部依赖 | 0（零第三方 AI 库）|
| 最低配置 | ESP32-C3 + 4MB Flash（无 PSRAM）|

---

## Demo 演示

### 视频演示

[Serial](https://github.com/user-attachments/assets/4ca20a3d-8336-4232-ae38-6b1f12aa64bc)

[chat](https://github.com/user-attachments/assets/6e035a13-102d-4a67-9170-0a83a2695f84)

### 持久记忆演示

ESPClaw 通过 NVS 存储在重启后仍能记住用户偏好：

![Memory Demo](https://github.com/tianrking/espclaw/raw/main/media/memory.png)

### MQTT 集成演示

ESPClaw 支持双向 MQTT 通信，可对接 Home Assistant、Node-RED 等 IoT 平台：

![MQTT Demo](https://github.com/tianrking/espclaw/raw/main/media/espc5_mqtt.png)

---

## 功能特性

### 完整的 ReAct Agent 实现

ESPClaw 实现了完整的 ReAct（Reasoning + Acting）循环：

1. **推理** - 调用 LLM 理解用户意图
2. **行动** - 通过工具执行实际操作
3. **观察** - 获取执行结果
4. **循环** - 直到任务完成

### 20 个内置工具

| 分类 | 工具 | 功能 |
|------|------|------|
| **GPIO 控制** | `gpio_write`, `gpio_read`, `gpio_read_all`, `delay` | 硬件控制 |
| **持久记忆** | `memory_set`, `memory_get`, `memory_delete`, `memory_list` | NVS 存储 |
| **定时任务** | `cron_schedule`, `cron_list`, `cron_cancel`, `cron_cancel_all` | 任务调度 |
| **时间管理** | `get_time`, `set_timezone` | 时区设置 |
| **系统诊断** | `get_diagnostics`, `get_version` | 健康检查 |
| **人格设置** | `set_persona`, `get_persona` | AI 性格 |
| **网络工具** | `wifi_scan`, `get_network_info` | 网络诊断 |

### 10 个通知通道

| 通道 | 类型 | 协议 | 状态 |
|------|------|------|------|
| Serial | 双向 | UART 串口 | 已验证 |
| Telegram | 双向 | Bot API 长轮询 | 已验证 |
| MQTT | 双向 | IoT 标准协议 | 已验证 |
| 钉钉 | 单向 | Webhook + HMAC | 开发中 |
| Discord | 单向 | Webhook | 开发中 |
| Slack | 单向 | Incoming Webhook | 开发中 |
| 企业微信 | 单向 | 群机器人 | 开发中 |
| 飞书 | 单向 | 机器人 + 签名 | 开发中 |
| Pushplus | 单向 | 统一推送 | 开发中 |
| Bark | 单向 | iOS 推送 | 开发中 |

### 灵活的 LLM 后端

支持任何 OpenAI 兼容的 API 端点：

| 后端 | 协议格式 | 测试状态 |
|------|----------|----------|
| Anthropic | Messages API | 已测试 |
| OpenAI | Chat Completions | 已测试 |
| OpenRouter | OpenAI 兼容 | 已测试 |
| Ollama | OpenAI 兼容 | 已测试 |
| GLM/智谱 | OpenAI 兼容 | 已测试 |
| DeepSeek | OpenAI 兼容 | 已测试 |
| Qwen/通义 | OpenAI 兼容 | 已测试 |

---

## 支持的硬件

### 支持的 ESP32 芯片系列

ESPClaw 支持乐鑫 ESP32 全系列芯片：

| 芯片 | 核心数 | SRAM | PSRAM | Flash | 功能模式 |
|------|--------|------|-------|-------|----------|
| ESP32-C3 | 1 | 400KB | 无 | 4MB+ | MINIMAL |
| ESP32-C5 | 1 | 400KB | 可选 | 4MB+ | MINIMAL/FULL |
| ESP32-C6 | 1 | 512KB | 无 | 4MB+ | MINIMAL |
| ESP32-S3 | 2 | 512KB | 可选 | 4MB+ | MINIMAL/FULL |

> **功能模式说明**：
> - **MINIMAL**：基础功能，无需 PSRAM 即可运行
> - **FULL**：解锁 OTA、LittleFS、WebSocket 等高级功能，需要 8MB PSRAM

### 通用开发板

任何基于上述芯片的开发板均可使用：

| 开发板类型 | 芯片 | 说明 |
|------------|------|------|
| ESP32-C3 DevKit | C3 | 官方开发板，入门首选 |
| ESP32-C5 DevKit | C5 | 支持 WiFi 6 |
| ESP32-C6 DevKit | C6 | 支持 WiFi 6 + Zigbee |
| ESP32-S3 DevKit | S3 | 双核高性能，推荐带 PSRAM 版本 |

### 推荐开发板：Seeed XIAO 系列

XIAO 系列体积小巧、性价比高，适合快速原型开发：

| 型号 | 芯片 | Flash | PSRAM | 特点 |
|------|------|-------|-------|------|
| XIAO ESP32C3 | C3 | 4MB | - | 最便宜，入门之选 |
| XIAO ESP32C5 | C5 | 8MB | 8MB Quad | WiFi 6，性价比高 |
| XIAO ESP32C6 | C6 | 4MB | - | WiFi 6 + Zigbee |
| XIAO ESP32S3 | S3 | 8MB | 8MB Octal | 标准版 |
| XIAO ESP32S3 Sense | S3 | 8MB | 8MB Octal | 带摄像头+SD卡槽 |
| **XIAO ESP32S3 Plus** | S3 | 16MB | 8MB Octal | 功能最全，强烈推荐 |

---

## 快速开始

### 一键构建脚本

项目提供了便捷的构建脚本，支持多种开发板：

```bash
# ESP32-S3 系列（推荐）
./build.sh xiao_s3_plus flash    # XIAO ESP32S3 Plus - 功能最全
./build.sh xiao_s3_sense flash   # XIAO ESP32S3 Sense - 带摄像头
./build.sh xiao_s3 flash         # XIAO ESP32S3 标准版
./build.sh s3 flash              # 通用 ESP32-S3 DevKit

# ESP32-C5/C6 系列（WiFi 6）
./build.sh xiao_c5 flash         # XIAO ESP32C5
./build.sh xiao_c6 flash         # XIAO ESP32C6
./build.sh c5 flash              # 通用 ESP32-C5 DevKit

# ESP32-C3 系列（入门首选）
./build.sh xiao_c3 flash         # XIAO ESP32C3
./build.sh c3 flash              # 通用 ESP32-C3 DevKit
```

### 手动构建（通用方法）

适用于任何 ESP32 开发板：

```bash
# 1. 首次烧录建议先擦除 flash（清除旧配置）
idf.py erase-flash

# 2. 设置目标芯片
idf.py set-target esp32s3   # 或 esp32c3 / esp32c5 / esp32c6

# 3. 配置参数
idf.py menuconfig
# -> ESPClaw Configuration -> LLM Settings（API Key、模型等）
# -> ESPClaw Configuration -> WiFi（SSID、密码）
# -> ESPClaw Configuration -> Channels（启用需要的通道）

# 4. 编译烧录
idf.py build flash monitor
```

> **提示**：切换芯片后需删除 `sdkconfig` 重新配置：
> ```bash
> rm -rf sdkconfig build && idf.py set-target esp32s3 && idf.py build
> ```

---

## LLM 配置

### 配置方式

通过 `menuconfig` 配置 LLM 后端：

```bash
idf.py menuconfig
# -> ESPClaw Configuration -> LLM Settings
#    -> LLM Backend: 选择后端类型
#    -> API Key: 输入你的 API 密钥
#    -> Base URL: 自定义端点（可选）
#    -> Model name: 模型名称
```

### 配置示例

#### 使用 GLM 智谱

```
LLM Backend: Custom (OpenAI-compatible)
API Key: your_glm_api_key
Base URL: https://open.bigmodel.cn/api/paas/v4/chat/completions
Model name: glm-4-flash
```

#### 使用 DeepSeek

```
LLM Backend: Custom (OpenAI-compatible)
API Key: your_deepseek_api_key
Base URL: https://api.deepseek.com/v1/chat/completions
Model name: deepseek-chat
```

#### 使用 Ollama 本地模型

```
LLM Backend: Custom (OpenAI-compatible)
API Key: ollama（任意值）
Base URL: http://你的电脑IP:11434/v1/chat/completions
Model name: llama3
```

---

## MQTT 集成

ESPClaw 支持双向 MQTT 通信，可对接 Home Assistant、Node-RED 等 IoT 平台。

### 启用 MQTT

```bash
idf.py menuconfig
# -> ESPClaw Configuration -> Channels
#    -> [*] Enable MQTT Channel
#    -> MQTT Broker URL: mqtt://broker.emqx.io
#    -> MQTT Username:（匿名访问留空）
#    -> MQTT Password:（匿名访问留空）
```

### Topic 格式

| 方向 | Topic 模式 |
|------|------------|
| 订阅（接收命令）| `espclaw/{client_id}/cmd` |
| 发布（发送响应）| `espclaw/{client_id}/response` |

`{client_id}` 从 MAC 地址自动生成，例如 `espclaw_feffe2`。

### 公共 MQTT Broker

| Broker | URL | 说明 |
|--------|-----|------|
| EMQX | `mqtt://broker.emqx.io` | 推荐，无需认证 |
| Mosquitto | `mqtt://test.mosquitto.org` | 备选 |
| HiveMQ | `mqtt://broker.hivemq.com` | 备选 |

### 测试命令

```bash
# 安装 mosquitto 客户端
brew install mosquitto    # macOS
apt install mosquitto-clients  # Ubuntu

# 订阅 ESP32 的响应
mosquitto_sub -h broker.emqx.io -t "espclaw/+/response" -v

# 发送命令到 ESP32（client_id 从串口日志查看）
mosquitto_pub -h broker.emqx.io -t "espclaw/espclaw_feffe2/cmd" -m "你好"
```

---

## 串口命令

通过串口终端可直接与 ESPClaw 交互：

| 命令 | 说明 |
|------|------|
| `/help` | 显示可用命令 |
| `/tools` | 列出已注册工具 |
| `/heap` | 显示剩余堆内存 |
| `/gpio` | 显示允许的 GPIO 引脚范围 |
| `/reset` | 软件重启 |

---

## WiFi 注意事项

### ESP32-C5 特殊说明

ESP32-C5 要求路由器设置为 **WPA2-PSK** 或 **WPA2/WPA3 混合模式**。"仅 WPA" 模式会导致认证失败。

### 常见问题排查

#### WiFi 连接失败 (reason=201)

| 原因 | 解决方案 |
|------|----------|
| 旧配置干扰 | 擦除 flash：`idf.py erase-flash` |
| 5GHz 网络 | ESP32 只支持 2.4GHz，确认路由器频段 |
| SSID 不可见 | 确认网络名称正确（区分大小写）|

**快速验证**：使用手机热点（开启"最大兼容性"模式）测试代码是否正常工作。

---

## 使用示例

### 持久记忆

ESPClaw 能记住你的偏好，重启后依然有效：

```
espclaw> 我叫小明，请记住
好的，我记住了你的名字是小明。

[重启设备后]

espclaw> 你还记得我是谁吗？
记得，你是小明！
```

### 定时任务

自然语言创建定时提醒，支持秒级精度：

```
espclaw> 每15秒提醒我检查
espclaw> 每天早上9点提醒我站起来
espclaw> 30分钟后提醒我查看烤箱
```

### GPIO 控制

通过自然语言控制硬件：

```
espclaw> 把 GPIO3 设为高电平
espclaw> 读取所有 GPIO 状态
espclaw> 等待 1000 毫秒后把 GPIO4 设为低电平
```

### 人格设置

切换 AI 的回复风格：

```
espclaw> 把你的人格设为幽默
espclaw> 当前的人格是什么？
```

支持的人格：`neutral`（中性）、`friendly`（友好）、`technical`（技术）、`witty`（幽默）

---

## 架构设计

```
+---------------------------------------------------------------------+
|                        Channels (条件编译)                           |
|  Serial | Telegram | MQTT | 钉钉 | Discord | Slack | ...           |
+-----------------------------+---------------------------------------+
                              |
                    +---------v---------+
                    |   Message Bus     | FreeRTOS Queue
                    |  inbound/outbound |
                    +---------+---------+
                              |
                    +---------v---------+
                    |   Agent Loop      | ReAct 循环
                    |  Session + Context|
                    +---------+---------+
                              |
        +---------------------+---------------------+
        |                     |                     |
+-------v-------+    +-------v--------+    +-------v------+
|    Provider   |    |  Tool Registry |    |   Service    |
|  Anthropic    |    |  20 个工具     |    |  cron        |
|  OpenAI       |    |  GPIO/Memory/  |    |  ratelimit   |
|  Ollama       |    |  Cron/Network  |    |  wifi_mgr    |
+---------------+    +-------+--------+    +--------------+
                             |
                    +--------v--------+
                    |      HAL        | 安全护栏
                    |  GPIO 白名单    |
                    +-----------------+
```

### 文件结构

```
main/
├── main.c                    # 入口
├── config.h                  # 编译时常量
├── platform.h                # C3/C5/S3 条件编译
├── agent/                    # Agent 核心
│   ├── agent_loop.c          # ReAct 循环
│   ├── session.c             # 对话历史
│   ├── context_builder.c     # 系统提示词
│   └── persona.c             # AI 人格系统
├── channel/                  # 通知通道
│   ├── channel_serial.c      # 串口
│   ├── channel_telegram.c    # Telegram
│   ├── channel_mqtt.c        # MQTT
│   └── ...                   # 其他通道
├── provider/                 # LLM 后端
│   ├── provider_anthropic.c  # Anthropic
│   └── provider_openai.c     # OpenAI 兼容
├── tool/                     # 工具实现
│   ├── tool_gpio.c           # GPIO 工具
│   ├── tool_memory.c         # 记忆工具
│   ├── tool_cron.c           # 定时工具
│   └── ...                   # 其他工具
├── service/                  # 后台服务
│   ├── cron_service.c        # 任务调度
│   └── ratelimit.c           # API 限流
└── hal/                      # 硬件抽象
    └── hal_gpio.c            # GPIO 安全护栏
```

---

## 为什么选择 ESPClaw？

### 对比其他方案

| 特性 | ESPClaw | MicroPython + AI 库 | 云端 AI |
|------|---------|---------------------|---------|
| 内存占用 | < 400KB SRAM | > 1MB | 0 |
| 延迟 | < 1s | 2-5s | 100-500ms |
| 离线能力 | 部分支持 | 部分支持 | 无 |
| 成本 | $2-10 | $5-15 | 按调用计费 |
| 开发门槛 | C 语言 | Python | API 调用 |
| 生产就绪 | 是 | 有限 | 是 |

### 适用场景

- **智能家居控制器** - 通过 MQTT 对接 Home Assistant
- **工业物联网网关** - 低成本边缘 AI 推理
- **教育机器人** - GPIO 控制与自然语言交互
- **可穿戴设备** - 小型开发板体积紧凑
- **原型验证** - 快速验证 AIoT 概念
- **嵌入式 AI 产品** - 商业级固件，MIT 开源协议

---

## 项目资源

| 资源 | 链接 |
|------|------|
| GitHub 仓库 | [tianrking/espclaw](https://github.com/tianrking/espclaw) |
| 开发计划 | [PLAN.md](https://github.com/tianrking/espclaw/blob/main/PLAN.md) |
| 板级配置 | [BOARDS.md](https://github.com/tianrking/espclaw/blob/main/BOARDS.md) |
| License | MIT |
| 版本 | v0.1.0 |

### 技术交流

欢迎加入微信群交流技术问题：

<p align="center">
  <img src="https://github.com/tianrking/espclaw/raw/main/media/wechat_group.JPG" width="180" alt="微信群二维码">
</p>

---

*ESPClaw - 让 AI 运行在每一颗芯片上*
