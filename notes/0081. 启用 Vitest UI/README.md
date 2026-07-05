# [0081. 启用 Vitest UI](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0081.%20%E5%90%AF%E7%94%A8%20Vitest%20UI)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装](#3-安装)
- [4. 启动方式](#4-启动方式)
- [5. 配置](#5-配置)
- [6. UI 地址](#6-ui-地址)
- [7. Watch 模式](#7-watch-模式)
- [8. 端口配置](#8-端口配置)
- [9. 与 CLI 的区别](#9-与-cli-的区别)
- [10. 适用场景](#10-适用场景)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 `@vitest/ui`
- 启动方式（`vitest --ui`）
- 配置 `ui: true`
- UI 地址
- Watch 模式
- 端口配置
- 与 CLI 的区别

## 2. 评价

Vitest UI 提供 Web 界面查看测试结果，比 CLI 输出更直观。适合本地开发调试。

## 3. 安装

```bash
pnpm add -D @vitest/ui
```

## 4. 启动方式

```bash
# 直接启动 UI
vitest --ui

# 或在 package.json 中配置
{
  "scripts": {
    "test:ui": "vitest --ui"
  }
}
```

## 5. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    ui: true,
    open: true, // 自动打开浏览器
  },
})
```

## 6. UI 地址

默认地址：`http://localhost:51204/__vitest__/`

## 7. Watch 模式

UI 默认在 Watch 模式下运行，文件变化时自动更新：

```bash
vitest --ui --watch
```

## 8. 端口配置

```typescript
test: {
  ui: true,
  api: {
    port: 51204,
  },
}
```

## 9. 与 CLI 的区别

| 维度     | CLI        | UI       |
| -------- | ---------- | -------- |
| 输出方式 | 终端文本   | Web 界面 |
| 交互性   | 命令行过滤 | 点击操作 |
| 可视化   | 有限       | 丰富     |
| 调试     | 较难       | 较易     |

## 10. 适用场景

- 本地开发调试
- 查看测试详情
- 覆盖率可视化
- 失败测试定位
