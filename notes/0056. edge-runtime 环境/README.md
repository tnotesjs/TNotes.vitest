# [0056. edge-runtime 环境](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0056.%20edge-runtime%20%E7%8E%AF%E5%A2%83)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 什么是 edge-runtime](#3-什么是-edge-runtime)
- [4. 安装](#4-安装)
- [5. 配置](#5-配置)
- [6. 使用场景](#6-使用场景)
- [7. API 限制](#7-api-限制)
- [8. 测试示例](#8-测试示例)
- [9. 与 Node 环境的区别](#9-与-node-环境的区别)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 模拟边缘运行时（Cloudflare Workers/Vercel Edge Functions）
- 安装配置
- 使用场景
- API 限制（无 fs/path）
- 与 Node 环境的区别

## 2. 评价

edge-runtime 环境用于测试边缘兼容的代码，限制较多，只提供部分 Web API。大部分测试仍应使用 node 或 jsdom 环境。

## 3. 什么是 edge-runtime

`edge-runtime` 模拟边缘运行时环境（如 Cloudflare Workers、Vercel Edge Functions），用于测试在边缘环境中运行的代码。

## 4. 安装

```bash
pnpm add -D @edge-runtime/vm
```

## 5. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'edge-runtime',
  },
})
```

## 6. 使用场景

测试在边缘运行时中执行的代码：

- Cloudflare Workers
- Vercel Edge Functions
- Deno Deploy
- 其他边缘计算平台

## 7. API 限制

边缘运行时只提供部分 Web API：

- `fetch`、`Request`、`Response`
- `URL`、`URLSearchParams`
- `TextEncoder`、`TextDecoder`
- `crypto`
- `setTimeout`、`setInterval`

不支持 Node.js 特有的 API（如 `fs`、`path`、`child_process`）。

## 8. 测试示例

```typescript
import { test, expect } from 'vitest'

test('edge fetch', async () => {
  const response = await fetch('https://api.example.com/data')
  expect(response.ok).toBe(true)
})
```

## 9. 与 Node 环境的区别

在 `edge-runtime` 环境中，如果使用了 Node.js 特有的 API，测试会失败：

```typescript
test('node api not available', () => {
  expect(() => {
    require('fs') // 会报错
  }).toThrow()
})
```

## 10. 注意事项

- `edge-runtime` 环境限制较多，只测试边缘兼容的代码
- 大部分测试仍应使用 `node` 或 `jsdom` 环境
- 确保代码在边缘运行时中能正常工作再使用此环境
