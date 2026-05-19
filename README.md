# opencode-image-proxy

OpenCode 插件：让不支持图片的模型也能「看懂」你粘贴的图片。

> **Fork 自** [samiulsami/opencode-image-proxy](https://github.com/samiulsami/opencode-image-proxy)，原作者仓库已长期未更新，在此基础之上进行了现代化升级和问题修复。

## 相比原版的改进

| 改进 | 说明 |
|------|------|
| 🔄 SDK 升级到 v1.15 | 适配最新的 OpenCode 插件 API（`@opencode-ai/plugin@1.15+`） |
| 🐛 修复内存泄漏 | 分析完成后的 Map 条目会被正确清理，长时间运行不再泄漏 |
| 🐛 修复临时会话泄漏 | 图片分析的临时会话无论成功还是失败都会被删除 |
| 🔒 类型安全升级 | 使用 SDK 官方类型，替换了原来的自定义类型定义 |
| 🆕 新增 glm-5.1 | 默认不支持图片的模型列表新增 `zai-coding-plan/glm-5.1` |
| 🔍 更严谨的类型守卫 | 图片/文本类型检测增加了 `typeof` 校验，运行时更可靠 |

## 安装方法

在 OpenCode 配置文件中添加（通常是 `~/.config/opencode/opencode.json`）：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "OCDcreator/opencode-image-proxy"
  ]
}
```

OpenCode 会自动从 GitHub 加载此插件。

## 工作原理

1. 当你在聊天中粘贴图片时，插件检查当前使用的模型是否支持图片
2. **不支持图片的模型**：自动把图片发给一个支持视觉的模型来描述内容，然后用文字描述替换原图
3. **支持图片的模型**：图片原样传递，不做任何处理

整个过程使用 OpenCode 已有的认证，不需要额外配置 API Key。

## 自定义配置（可选）

不创建配置文件也能用，插件会使用内置默认值。如果需要自定义，创建以下文件：

`~/.config/opencode/opencode-image-proxy.json`

> 查看可用模型：`opencode models`

```json
{
  "imageIncapableModels": [
    "zai-coding-plan/glm-4.5",
    "zai-coding-plan/glm-4.5-air",
    "zai-coding-plan/glm-4.5-flash",
    "zai-coding-plan/glm-4.6",
    "zai-coding-plan/glm-4.7",
    "zai-coding-plan/glm-4.7-flash",
    "zai-coding-plan/glm-5",
    "zai-coding-plan/glm-5.1"
  ],
  "imageReaderModel": {
    "providerID": "opencode",
    "modelID": "kimi-k2.5-free"
  },
  "analysisPrompt": "The user has pasted an image into their chat. Describe what you see as if you are directly observing the image. Be thorough but concise. Include:\n- All visible elements (objects, text, UI elements, people, etc.)\n- Exact transcription of any text\n- The context and purpose of the image\n- Any relevant technical details\n\nDescribe it naturally, as if explaining to someone what you're looking at right now."
}
```

### 配置项说明

| 配置项 | 说明 |
|--------|------|
| `imageIncapableModels` | 不支持图片的模型列表，格式为 `provider/model` |
| `imageReaderModel` | 用来「读图」的视觉模型，必须支持图片输入 |
| `analysisPrompt` | 读图时使用的系统提示词（可以改成中文） |

### 行为对照

| 当前模型 | 行为 |
|----------|------|
| 在「不支持图片」列表中 | 图片 → 视觉模型描述 → 文字替代 |
| 不在列表中 | 图片原样传递，不处理 |

## 使用示例

```
[User pasted image: screenshot.png]
This is a terminal window showing a Node.js error. The error message reads:
"TypeError: Cannot read property 'map' of undefined" at line 42 in app.js.
The stack trace below shows the error originated in the UserList component...
```

## 前提条件

- `imageReaderModel` 配置的模型必须已在 OpenCode 中认证且可用
- 该模型必须支持图片输入

## 构建

```bash
npm install
npm run build
```

## License

MIT
