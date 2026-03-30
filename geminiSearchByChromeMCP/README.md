# Gemini Search by Chrome MCP

通过 Chrome MCP 浏览器在 Gemini (gemini.google.com) 上搜索内容。

## 前置要求

### 1. Chrome 开启远程调试

```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
```

### 2. OpenClaw 配置

确保 `~/.openclaw/openclaw.json` 中 `plugins.allow` 包含 `browser`：

```json
{
  "plugins": {
    "allow": ["acpx", "minimax", "feishu", "browser"]
  }
}
```

重启 Gateway 后生效：
```bash
openclaw gateway restart
```

## 使用方法

在 OpenClaw 对话中直接说：

- "用 Gemini 搜索 xxx"
- "search Gemini xxx"
- "ask Gemini xxx"
- "用 Chrome MCP 搜索 Gemini"

Agent 会自动：
1. 打开 Chrome 浏览器（已有登录态）
2. 导航到 gemini.google.com
3. 输入搜索词并提交
4. 等待 Gemini AI 生成回答
5. 提取并返回结果

## 适用场景

- 需要 Gemini 实时回答的问题
- 需要 Gemini 搜索最新资讯
- 需要 Gemini 分析/解释内容

## 技术原理

- 使用 Chrome MCP 协议连接本地 Chrome
- 通过 accessibility tree 定位元素和交互
- 支持所有 Gemini 支持的搜索类型

## 文件说明

- `SKILL.md` — OpenClaw 技能定义文件
