# [0038. 环境变量 Mock](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0038.%20%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%20Mock)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.stubEnv](#3-vistubenv)
- [4. import.meta.env](#4-importmetaenv)
- [5. vi.unstubAllEnvironments](#5-viunstuballenvironments)
- [6. 在 beforeEach 中使用](#6-在-beforeeach-中使用)
- [7. 检查环境变量](#7-检查环境变量)
- [8. 环境变量类型](#8-环境变量类型)
- [9. 配置中的环境变量](#9-配置中的环境变量)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- vi.stubEnv 设置环境变量
- import.meta.env 访问
- vi.unstubAllEnvironments 恢复
- 环境变量类型
- 配置中的环境变量

## 2. 评价

vi.stubEnv 用于在测试中临时修改环境变量，测试结束后自动恢复。适合测试不同环境下的代码行为。

## 3. vi.stubEnv

`vi.stubEnv` 设置环境变量，测试结束后自动恢复：

```typescript
import { vi, test, expect, afterEach } from 'vitest'

afterEach(() => {
  vi.unstubAllEnvironments()
})

test('stub env', () => {
  vi.stubEnv('NODE_ENV', 'production')

  expect(process.env.NODE_ENV).toBe('production')
})
```

## 4. import.meta.env

Vite 项目中使用 `import.meta.env` 读取环境变量：

```typescript
test('import.meta.env', () => {
  vi.stubEnv('MODE', 'development')
  vi.stubEnv('VITE_API_URL', 'http://localhost:3000')

  expect(import.meta.env.MODE).toBe('development')
  expect(import.meta.env.VITE_API_URL).toBe('http://localhost:3000')
})
```

## 5. vi.unstubAllEnvironments

恢复所有被 stub 的环境变量：

```typescript
test('unstub env', () => {
  vi.stubEnv('NODE_ENV', 'test')
  expect(process.env.NODE_ENV).toBe('test')

  vi.unstubAllEnvironments()
  // NODE_ENV 恢复到原始值
})
```

## 6. 在 beforeEach 中使用

```typescript
describe('env tests', () => {
  afterEach(() => {
    vi.unstubAllEnvironments()
  })

  test('test 1', () => {
    vi.stubEnv('NODE_ENV', 'development')
    expect(process.env.NODE_ENV).toBe('development')
  })

  test('test 2', () => {
    vi.stubEnv('NODE_ENV', 'production')
    expect(process.env.NODE_ENV).toBe('production')
  })
})
```

## 7. 检查环境变量

```typescript
test('check env', () => {
  vi.stubEnv('VITE_API_URL', 'https://api.example.com')

  const apiUrl = import.meta.env.VITE_API_URL
  expect(apiUrl).toBe('https://api.example.com')
})
```

## 8. 环境变量类型

在 TypeScript 中，`import.meta.env` 的类型由 `vite/client` 提供：

```typescript
// 常见的环境变量
import.meta.env.MODE // string
import.meta.env.BASE_URL // string
import.meta.env.PROD // boolean
import.meta.env.DEV // boolean
import.meta.env.SSR // boolean
import.meta.env.VITE_API_URL // string
```

## 9. 配置中的环境变量

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    env: {
      NODE_ENV: 'test',
      VITE_API_URL: 'http://localhost:3000',
    },
  },
})
```

配置中的 `env` 会为所有测试设置默认环境变量。

## 10. 注意事项

- `vi.stubEnv` 只影响当前测试，测试结束后自动恢复
- `vi.stubEnv` 影响 `process.env` 和 `import.meta.env`
- 如果测试中修改了 `process.env` 但没有使用 `vi.stubEnv`，需要手动恢复
