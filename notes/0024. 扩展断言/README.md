# [0024. 扩展断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0024.%20%E6%89%A9%E5%B1%95%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. expect.extend 基础](#3-expectextend-基础)
- [4. matcher 函数签名](#4-matcher-函数签名)
- [5. 返回值说明](#5-返回值说明)
- [6. 异步 matcher](#6-异步-matcher)
- [7. TypeScript 类型扩展](#7-typescript-类型扩展)
- [8. 在 setup 文件中注册](#8-在-setup-文件中注册)

<!-- endregion:toc -->

## 1. 本节内容

- expect.extend 创建自定义 matcher
- matcher 函数签名
- 返回值说明
- 异步 matcher
- TypeScript 类型扩展
- setup 文件中注册

## 2. 评价

expect.extend 允许创建项目特定的断言方法，提高测试代码的可读性和复用性。TypeScript 项目需要额外声明类型以获得编辑器支持。

## 3. expect.extend 基础

`expect.extend` 用于创建自定义 matcher：

```typescript
import { expect } from 'vitest'

expect.extend({
  toBeWithinRange(received: number, floor: number, ceiling: number) {
    const pass = received >= floor && received <= ceiling
    return {
      pass,
      message: () =>
        pass
          ? `expected ${received} not to be within range ${floor} - ${ceiling}`
          : `expected ${received} to be within range ${floor} - ${ceiling}`,
    }
  },
})
```

使用自定义 matcher：

```typescript
test('custom matcher', () => {
  expect(10).toBeWithinRange(1, 20)
  expect(10).not.toBeWithinRange(11, 20)
})
```

## 4. matcher 函数签名

matcher 函数接收的第一个参数是实际值（`received`），后续参数是 matcher 的参数：

```typescript
expect.extend({
  toHaveLength(received: unknown[], expected: number) {
    const actualLength = received.length
    return {
      pass: actualLength === expected,
      message: () => `expected array length ${actualLength} to be ${expected}`,
    }
  },
})

test('array length', () => {
  expect([1, 2, 3]).toHaveLength(3)
})
```

## 5. 返回值说明

matcher 返回一个对象：

| 字段      | 说明                       |
| --------- | -------------------------- |
| `pass`    | 布尔值，表示断言是否通过   |
| `message` | 函数，返回失败时的提示信息 |

当 `pass` 为 `true` 时，配合 `not` 使用会反转结果。

## 6. 异步 matcher

自定义 matcher 也支持异步：

```typescript
expect.extend({
  async toBeValidUrl(received: string) {
    try {
      const response = await fetch(received)
      return {
        pass: response.ok,
        message: () => `expected ${received} to be a valid URL`,
      }
    } catch {
      return {
        pass: false,
        message: () => `expected ${received} to be a valid URL`,
      }
    }
  },
})

test('async matcher', async () => {
  await expect('https://example.com').resolves.toBeValidUrl()
})
```

## 7. TypeScript 类型扩展

为了让 TypeScript 识别自定义 matcher，需要声明类型：

```typescript
// vitest.d.ts 或 custom-matchers.d.ts
import 'vitest'

declare module 'vitest' {
  interface Assertion<T = any> {
    toBeWithinRange(floor: number, ceiling: number): T
    toHaveLength(expected: number): T
  }
}
```

将该文件添加到 `tsconfig.json` 的 `include` 中即可获得类型提示。

## 8. 在 setup 文件中注册

推荐在 setup 文件中统一注册自定义 matcher：

```typescript
// setup.ts
import { expect } from 'vitest'

expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    return {
      pass: received >= floor && received <= ceiling,
      message: () => `expected ${received} to be within ${floor}-${ceiling}`,
    }
  },
})
```

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    setupFiles: ['./setup.ts'],
  },
})
```
