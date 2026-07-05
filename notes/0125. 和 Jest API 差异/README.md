# [0125. 和 Jest API 差异](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0125.%20%E5%92%8C%20Jest%20API%20%E5%B7%AE%E5%BC%82)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. ESM 支持](#3-esm-支持)
- [4. hoisting 行为](#4-hoisting-行为)
- [5. fake timers](#5-fake-timers)
- [6. snapshot](#6-snapshot)
- [7. 模块 Mock](#7-模块-mock)
- [8. requireActual](#8-requireactual)
- [9. 配置差异](#9-配置差异)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- ESM 支持差异
- hoisting 行为差异
- fake timers 差异
- snapshot 差异
- 模块 Mock 差异
- requireActual（vi.importActual）
- 配置差异

## 2. 评价

- Vitest 与 Jest API 高度兼容，但有细微差异
- vi.importActual 是异步的，ESM 支持更原生，配置格式不同

## 3. ESM 支持

Vitest 原生支持 ESM，Jest 需要额外配置：

```typescript
// Vitest - 直接使用 import
import { add } from './math'

// Jest - 需要 Babel/SWC 转换
```

## 4. hoisting 行为

两者都会提升 `vi.mock`/`jest.mock`，但 Vitest 的 hoisting 更严格：

```typescript
// 两者都正确
vi.mock('./module')
import { fn } from './module'
```

## 5. fake timers

```typescript
// Jest
jest.useFakeTimers()
jest.advanceTimersByTime(1000)

// Vitest
vi.useFakeTimers()
vi.advanceTimersByTime(1000)
```

API 基本相同，但实现细节有差异。

## 6. snapshot

```typescript
// 基本相同
expect(result).toMatchSnapshot()
expect(result).toMatchInlineSnapshot()
```

## 7. 模块 Mock

```typescript
// Jest
jest.mock('./module', () => ({
  fn: jest.fn(),
}))

// Vitest
vi.mock('./module', () => ({
  fn: vi.fn(),
}))
```

## 8. requireActual

```typescript
// Jest
const actual = jest.requireActual('./module')

// Vitest
const actual = await vi.importActual('./module')
```

注意：`vi.importActual` 是异步的。

## 9. 配置差异

| 配置     | Jest            | Vitest           |
| -------- | --------------- | ---------------- |
| 配置文件 | jest.config.js  | vitest.config.ts |
| 环境     | testEnvironment | environment      |
| 全局     | globals         | globals          |
| Mock     | automock        | -                |

## 10. 注意事项

- `vi.importActual` 是异步的
- Vitest 使用 `vi` 而非 `jest`
- 配置文件格式不同
