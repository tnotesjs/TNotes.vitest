# [0053. Node 环境](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0053.%20Node%20%E7%8E%AF%E5%A2%83)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 默认环境](#3-默认环境)
- [4. Node API 测试](#4-node-api-测试)
- [5. 网络请求](#5-网络请求)
- [6. 子进程](#6-子进程)
- [7. 流操作](#7-流操作)
- [8. 适合场景](#8-适合场景)
- [9. 不适合场景](#9-不适合场景)
- [10. 切换环境](#10-切换环境)

<!-- endregion:toc -->

## 1. 本节内容

- 默认环境无需配置
- Node API 测试（fs/path/os）
- 网络请求
- 子进程
- 流操作
- 适合纯函数和服务端代码

## 2. 评价

Node 环境是 Vitest 的默认环境，适合测试纯函数、工具库和服务端代码。不提供 DOM API，需要 DOM 测试应使用 jsdom 或 Browser Mode。

## 3. 默认环境

Vitest 默认使用 `node` 环境，不需要额外配置：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'node', // 默认值
  },
})
```

在 `node` 环境中，可以使用所有 Node.js API。

## 4. Node API 测试

```typescript
import { test, expect } from 'vitest'
import fs from 'node:fs'
import path from 'node:path'
import os from 'node:os'

test('fs operations', () => {
  const content = fs.readFileSync('package.json', 'utf-8')
  expect(content).toContain('vitest')
})

test('path operations', () => {
  const result = path.join('/home', 'user', 'file.txt')
  expect(result).toBe('/home/user/file.txt')
})

test('os info', () => {
  expect(os.platform()).toBeDefined()
  expect(os.arch()).toBeDefined()
})
```

## 5. 网络请求

```typescript
test('fetch', async () => {
  const response = await fetch('https://api.example.com/data')
  expect(response.ok).toBe(true)
  const data = await response.json()
  expect(data).toBeDefined()
})
```

Node.js 18+ 内置 `fetch`，不需要额外 polyfill。

## 6. 子进程

```typescript
import { execSync } from 'node:child_process'

test('child process', () => {
  const result = execSync('echo hello', { encoding: 'utf-8' })
  expect(result.trim()).toBe('hello')
})
```

## 7. 流操作

```typescript
import { Readable } from 'node:stream'

test('stream', async () => {
  const stream = Readable.from(['hello', 'world'])
  const chunks: string[] = []

  for await (const chunk of stream) {
    chunks.push(chunk)
  }

  expect(chunks).toEqual(['hello', 'world'])
})
```

## 8. 适合场景

`node` 环境适合测试：

- 纯函数和工具库
- Node.js API 操作（文件系统、网络、进程）
- 服务端逻辑
- CLI 工具
- 数据处理

## 9. 不适合场景

`node` 环境不提供 DOM API，以下场景需要使用 `jsdom` 或 `happy-dom`：

- 浏览器组件渲染
- DOM 操作
- `document`、`window` 对象
- 浏览器事件

## 10. 切换环境

如果大部分测试使用 `node` 环境，只有少数需要 DOM，可以通过文件级注释切换：

```typescript
// @vitest-environment jsdom
import { test, expect } from 'vitest'

test('DOM test', () => {
  document.body.innerHTML = '<div id="app"></div>'
  expect(document.getElementById('app')).toBeDefined()
})
```
