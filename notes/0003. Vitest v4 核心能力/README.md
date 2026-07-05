# [0003. Vitest v4 核心能力](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0003.%20Vitest%20v4%20%E6%A0%B8%E5%BF%83%E8%83%BD%E5%8A%9B)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. ESM 原生支持](#3-esm-原生支持)
  - [3.1. 和 Jest 的 ESM 支持对比](#31-和-jest-的-esm-支持对比)
- [4. TypeScript 支持](#4-typescript-支持)
  - [4.1. 类型声明](#41-类型声明)
- [5. JSX 支持](#5-jsx-支持)
- [6. 快照测试](#6-快照测试)
  - [6.1. 内联快照](#61-内联快照)
- [7. Mock 与 Spy](#7-mock-与-spy)
  - [7.1. vi.fn - 创建 Mock 函数](#71-vifn---创建-mock-函数)
  - [7.2. vi.spyOn - 监听方法调用](#72-vispyon---监听方法调用)
  - [7.3. vi.mock - 模块级 Mock](#73-vimock---模块级-mock)
- [8. 覆盖率](#8-覆盖率)
- [9. 浏览器模式 (Browser Mode)](#9-浏览器模式-browser-mode)
- [10. Workspace](#10-workspace)

<!-- endregion:toc -->

## 1. 本节内容

- ESM 原生支持：无需额外配置，直接支持 ES Modules
- TypeScript 支持：开箱即用，利用 Vite 的 esbuild 转译
- JSX 支持：通过 Vite 插件系统支持 JSX/TSX
- 快照测试：捕获 UI 组件或复杂数据结构的输出
- Mock 与 Spy：完整的 Mock 系统，API 与 Jest 兼容
- 覆盖率：支持 v8 和 istanbul 两种 provider
- 浏览器模式：在真实浏览器环境中运行测试
- Workspace：支持 Monorepo 多项目测试

## 2. 评价

Vitest v4 提供了完整的测试能力，覆盖了现代前端项目的所有测试需求。它继承了 Vite 的高性能特性，同时提供了与 Jest 兼容的 API，使得开发者可以无缝迁移。浏览器模式和 Workspace 支持使其成为大型项目的理想选择。

## 3. ESM 原生支持

Vitest 内部使用 Vite 的模块解析引擎，天然支持 ESM。不需要额外的 Babel 转换或实验性标志，直接运行 `.mjs` 文件或 `package.json` 中 `"type": "module"` 的项目。

```typescript
// utils.ts
export function add(a: number, b: number): number {
  return a + b
}

// utils.test.ts - 直接 import，无需额外配置
import { expect, test } from 'vitest'
import { add } from './utils'

test('add', () => {
  expect(add(1, 2)).toBe(3)
})
```

Vitest 会通过 Vite 对源码做按需编译，测试文件中的 ESM import 语句不需要任何转换就能在 Node.js 中正确执行。

### 3.1. 和 Jest 的 ESM 支持对比

Jest 默认使用自己的 CommonJS 模块系统，要使用 ESM 需要通过 `--experimental-vm-modules` 标志，或者借助 Babel/SWC 做转换。Vitest 则直接复用 Vite 的模块系统，开箱即用。

## 4. TypeScript 支持

Vitest 继承了 Vite 的 esbuild 转译能力，TypeScript 文件可以在运行测试时即时转译，不需要预先编译。

```typescript
// 直接写 TypeScript，不需要 tsconfig 中额外配置
import { describe, it, expect } from 'vitest'

interface User {
  name: string
  age: number
}

function greet(user: User): string {
  return `Hello, ${user.name}!`
}

describe('greet', () => {
  it('should greet user', () => {
    expect(greet({ name: 'Alice', age: 25 })).toBe('Hello, Alice!')
  })
})
```

### 4.1. 类型声明

Vitest 提供了完整的类型声明，安装后在 `tsconfig.json` 中添加引用即可获得类型提示：

```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

启用 `vitest/globals` 后，`describe`、`it`、`expect` 等全局 API 不需要手动 import 就能获得类型补全。

## 5. JSX 支持

Vitest 通过 Vite 的插件系统支持 JSX/TSX。配合 `@vitejs/plugin-react` 或 `@vitejs/plugin-vue-jsx`，可以在测试中直接编写 JSX：

```tsx
// 配合 @vitejs/plugin-react
import { render, screen } from '@testing-library/react'
import { expect, test } from 'vitest'
import { Button } from './Button'

test('renders button', () => {
  render(<Button>Click me</Button>)
  expect(screen.getByText('Click me')).toBeDefined()
})
```

JSX 转换由 Vite 插件链完成，Vitest 本身不做 JSX 处理，而是复用项目已有的 Vite 配置。

## 6. 快照测试

快照测试用于捕获 UI 组件或复杂数据结构的输出，并在后续测试中对比是否发生变化。

```typescript
import { expect, test } from 'vitest'

test('snapshot', () => {
  const result = {
    name: 'vitest',
    version: 4,
    features: ['esm', 'typescript', 'coverage'],
  }
  expect(result).toMatchSnapshot()
})
```

首次运行时会在 `__snapshots__` 目录下生成 `.snap` 文件，后续运行时自动对比。如果输出发生了预期变化，可以用 `vitest -u` 更新快照。

### 6.1. 内联快照

除了外部快照文件，Vitest 还支持内联快照，将快照内容直接写入测试文件：

```typescript
test('inline snapshot', () => {
  expect(add(1, 2)).toMatchInlineSnapshot(`3`)
})
```

## 7. Mock 与 Spy

Vitest 内置了完整的 Mock 系统，API 与 Jest 高度兼容。

### 7.1. vi.fn - 创建 Mock 函数

```typescript
import { expect, test, vi } from 'vitest'

test('mock function', () => {
  const fn = vi.fn()
  fn('hello')

  expect(fn).toHaveBeenCalledOnce()
  expect(fn).toHaveBeenCalledWith('hello')
})
```

### 7.2. vi.spyOn - 监听方法调用

```typescript
import { expect, test, vi } from 'vitest'

test('spyOn', () => {
  const obj = { greet: (name: string) => `Hi, ${name}` }
  const spy = vi.spyOn(obj, 'greet')

  obj.greet('Alice')

  expect(spy).toHaveBeenCalledWith('Alice')
  spy.mockRestore()
})
```

### 7.3. vi.mock - 模块级 Mock

```typescript
import { expect, test, vi } from 'vitest'

vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ name: 'Mock User' })),
}))

import { fetchUser } from './api'

test('module mock', () => {
  expect(fetchUser(1)).toEqual({ name: 'Mock User' })
})
```

## 8. 覆盖率

Vitest 支持通过 v8 或 istanbul 两种 provider 收集代码覆盖率。

```bash
vitest --coverage
```

在配置文件中启用：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html', 'lcov'],
      include: ['src/**'],
      exclude: ['src/**/*.test.ts'],
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80,
      },
    },
  },
})
```

覆盖率报告支持多种格式：`text`（终端输出）、`html`（可视化报告）、`lcov`（CI 集成）等。

## 9. 浏览器模式 (Browser Mode)

Vitest v4 支持在真实浏览器环境中运行测试，而非 jsdom 或 happy-dom 的模拟环境。

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    browser: {
      enabled: true,
      provider: 'playwright',
      instances: [{ browser: 'chromium' }, { browser: 'firefox' }],
    },
  },
})
```

浏览器模式的优势：

- 使用真实的 DOM API，行为与生产环境一致
- 可以测试浏览器特有 API（Web Worker、WebGL 等）
- 组件渲染结果可在浏览器中直观查看

## 10. Workspace

Vitest Workspace 用于在 Monorepo 或多项目场景下统一运行测试。

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  {
    test: {
      name: 'unit',
      root: './packages/core',
      environment: 'node',
    },
  },
  {
    test: {
      name: 'components',
      root: './packages/ui',
      environment: 'jsdom',
    },
  },
])
```

每个子项目可以有独立的配置（环境、覆盖率、文件匹配规则等），根目录统一运行 `vitest` 即可执行所有项目的测试。
