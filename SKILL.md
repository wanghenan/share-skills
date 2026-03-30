---
name: gemini-search
description: Use Chrome MCP browser to search Gemini (gemini.google.com) and return results. Activates when user asks to search Gemini, ask Gemini, or search on Gemini via Chrome MCP. Handles browser setup verification, page navigation, text input, and response extraction.
---

# Gemini Search via Chrome MCP

## Prerequisites

1. Chrome must be running with remote debugging port:
   ```bash
   /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
   ```
2. OpenClaw browser plugin must be enabled and configured with `user` profile using `chrome-mcp` transport.

## Workflow

### Step 1: Verify Browser Status

```bash
openclaw browser --browser-profile user status
```

Expected output: `running: true`, `transport: chrome-mcp`

### Step 2: Open Gemini

```javascript
browser(action=open, profile="user", targetUrl="https://gemini.google.com")
```

Save returned `targetId`.

### Step 3: Get Page Structure

```javascript
browser(action=snapshot, profile="user", targetId="<targetId>")
```

From snapshot, locate the search box ref — typically:
- `textbox "为 Gemini 输入提示" [ref=1_43]`

### Step 4: Submit Search Query

```javascript
browser(action=act, profile="user", targetId="<targetId>", request={
  kind: "type",
  ref: "1_43",
  text: "<用户查询>",
  submit: true
})
```

### Step 5: Wait for Response

```javascript
browser(action=snapshot, profile="user", targetId="<targetId>", timeoutMs=10000)
```

If results not ready yet, wait and snapshot again.

### Step 6: Extract Results

Read `statictext` content under the response section. Gemini wraps answers in `generic` blocks with `statictext` children.

## Example

**User**: "用 Gemini 搜索 OpenClaw 最新版本"

**Execution**:
1. `browser(action=open, profile="user", targetUrl="https://gemini.google.com")` → targetId: "7"
2. `browser(action=snapshot, profile="user", targetId="7")` → find search box ref `1_43`
3. `browser(action=act, ..., request={kind:"type", ref:"1_43", text:"OpenClaw 最新版本更新 2026", submit:true})`
4. `browser(action=snapshot, profile="user", targetId="7", timeoutMs=10000)` → read response

## Notes

- If `openclaw browser` command fails with "unknown command", add `"browser"` to `plugins.allow` in `~/.openclaw/openclaw.json` and restart Gateway.
- Gemini may show "立即回答" button if response is deferred — click or just wait for auto-load.
- Use `timeoutMs=10000` or higher when waiting for AI responses.
- The `user` profile uses Chrome MCP transport — ensure Chrome was launched with `--remote-debugging-port=9222`.
