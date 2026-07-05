# [0106. 常见失败排查](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0106.%20%E5%B8%B8%E8%A7%81%E5%A4%B1%E8%B4%A5%E6%8E%92%E6%9F%A5)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 超时问题](#3-超时问题)
- [4. 环境缺失](#4-环境缺失)
- [5. Mock 不生效](#5-mock-不生效)
- [6. 路径别名错误](#6-路径别名错误)
- [7. 模块导入错误](#7-模块导入错误)
- [8. 类型错误](#8-类型错误)
- [9. 内存泄漏](#9-内存泄漏)
- [10. 排查步骤](#10-排查步骤)

<!-- endregion:toc -->

## 1. 本节内容

- 超时问题（异步未 await）
- 环境缺失（未配置 jsdom）
- Mock 不生效（vi.mock 位置）
- 路径别名错误（vite-tsconfig-paths）
- 模块导入错误
- 类型错误
- 内存泄漏

## 2. 评价

常见失败通常由超时、环境配置、Mock 使用不当引起。掌握排查步骤可以快速定位问题。

## 3. 超时问题

测试超时通常是因为：

- 异步操作没有正确 await
- 测试中的 Promise 没有返回
- 操作本身耗时过长

解决方案：

```typescript
// 增加超时时间
test('slow test', async () => {
  const result = await slowOperation()
  expect(result).toBeDefined()
}, 15000) // 15 秒

// 或全局配置
// vitest.config.ts
test: {
  testTimeout: 15000,
}
```

## 4. 环境缺失

使用了特定环境的 API 但没有配置环境：

```typescript
// 错误：jsdom 环境未配置
test('DOM', () => {
  document.body.innerHTML = '<div></div>'
})
```

解决方案：

```typescript
// @vitest-environment jsdom
import { test, expect } from 'vitest'
```

## 5. Mock 不生效

Mock 没有生效通常是因为：

- `vi.mock` 位置不对（应在 import 之前）
- Mock 的路径不对
- 没有正确使用 `vi.mocked`

解决方案：

```typescript
// vi.mock 会被提升到文件顶部
vi.mock('./api', () => ({
  fetchUser: vi.fn(),
}))

import { fetchUser } from './api'

test('mock', () => {
  vi.mocked(fetchUser).mockReturnValue({ id: 1 })
  expect(fetchUser(1)).toEqual({ id: 1 })
})
```

## 6. 路径别名错误

路径别名不生效：

- 没有安装 `vite-tsconfig-paths`
- `tsconfig.json` 配置不对

解决方案：

```bash
pnpm add -D vite-tsconfig-paths
```

```typescript
// vitest.config.ts
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [tsconfigPaths()],
})
```

## 7. 模块导入错误

CommonJS/ESM 混合使用导致的问题：

```typescript
// 错误
const utils = require('./utils') // 在 ESM 中不能用 require

// 正确
import utils from './utils'
```

## 8. 类型错误

TypeScript 类型检查失败：

```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

## 9. 内存泄漏

测试中的资源没有正确清理：

```typescript
afterEach(() => {
  vi.restoreAllMocks()
})
```

## 10. 排查步骤

1. 检查错误消息
2. 检查堆栈跟踪
3. 使用 `console.log` 调试
4. 使用 `--reporter=verbose` 获取更多信息
5. 使用调试器断点
