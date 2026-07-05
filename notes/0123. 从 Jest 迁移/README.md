# [0123. 从 Jest 迁移](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0123.%20%E4%BB%8E%20Jest%20%E8%BF%81%E7%A7%BB)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. API 对照](#3-api-对照)
- [4. 配置迁移](#4-配置迁移)
- [5. Mock 差异](#5-mock-差异)
- [6. hoisting 行为](#6-hoisting-行为)
- [7. 断言迁移](#7-断言迁移)
- [8. 快照迁移](#8-快照迁移)
- [9. 迁移步骤](#9-迁移步骤)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- API 对照表（jest.fn -> vi.fn）
- 配置迁移（jest.config -> vitest.config）
- Mock 差异
- hoisting 行为
- 断言迁移
- 快照迁移
- 迁移步骤

## 2. 评价

- Jest 到 Vitest 的迁移成本很低，API 高度兼容
- 主要变化是 jest 命名空间改为 vi，配置文件格式不同

## 3. API 对照

| Jest                   | Vitest               |
| ---------------------- | -------------------- |
| `jest.fn()`            | `vi.fn()`            |
| `jest.mock()`          | `vi.mock()`          |
| `jest.spyOn()`         | `vi.spyOn()`         |
| `jest.useFakeTimers()` | `vi.useFakeTimers()` |
| `jest.requireActual()` | `vi.importActual()`  |

## 4. 配置迁移

```typescript
// jest.config.js -> vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['./setup.ts'],
  },
})
```

## 5. Mock 差异

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

## 6. hoisting 行为

```typescript
// 两者都会提升 mock 到文件顶部
vi.mock('./module') // 自动提升
```

## 7. 断言迁移

大部分断言 API 兼容：

```typescript
expect(value).toBe(1)
expect(value).toEqual({ a: 1 })
expect(fn).toHaveBeenCalled()
```

## 8. 快照迁移

```typescript
// 基本相同
expect(result).toMatchSnapshot()
expect(result).toMatchInlineSnapshot()
```

## 9. 迁移步骤

1. 安装 Vitest
2. 创建 `vitest.config.ts`
3. 替换 `jest` 为 `vi`
4. 运行测试验证

## 10. 注意事项

- Jest 的 `jest.requireActual` 变为 `vi.importActual`
- `vi.mock` 是 ESM 优先
- 使用 `@vitest/coverage-v8` 替代 `istanbul`
