# [0049. setup 文件](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0049.%20setup%20%E6%96%87%E4%BB%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. setupFiles](#3-setupfiles)
- [4. setupFiles 的执行时机](#4-setupfiles-的执行时机)
- [5. globalSetup](#5-globalsetup)
- [6. 自定义 Matcher](#6-自定义-matcher)
- [7. Mock 全局对象](#7-mock-全局对象)
- [8. 多个 setup 文件](#8-多个-setup-文件)
- [9. 注意事项](#9-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- setupFiles 测试前执行
- 执行时机
- globalSetup 全局 setup
- 自定义 Matcher
- Mock 全局对象
- 多个 setup 文件

## 2. 评价

setupFiles 适合注册全局 Mock、自定义 Matcher 等。globalSetup 只执行一次，适合初始化数据库等重量级操作。多个 setup 文件按顺序执行。

## 3. setupFiles

`test.setupFiles` 指定在每个测试文件执行前运行的 setup 文件：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    setupFiles: ['./setup.ts'],
  },
})
```

```typescript
// setup.ts
import { vi } from 'vitest'

// 全局 Mock
vi.stubGlobal('fetch', vi.fn())

// 全局断言扩展
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    return {
      pass: received >= floor && received <= ceiling,
      message: () => `expected ${received} to be within ${floor}-${ceiling}`,
    }
  },
})
```

## 4. setupFiles 的执行时机

setup 文件在每个测试文件的 `beforeAll` 之前执行：

```
setup.ts 执行
  -> 测试文件的 beforeAll
    -> 测试文件的 beforeEach
      -> 测试用例
    -> 测试文件的 afterEach
  -> 测试文件的 afterAll
```

每个测试文件都会重新执行 setup 文件。

## 5. globalSetup

`test.globalSetup` 指定全局 setup 文件，只执行一次：

```typescript
test: {
  globalSetup: ['./global-setup.ts'],
}
```

```typescript
// global-setup.ts
import type { GlobalSetupContext } from 'vitest/node'

export function setup({ provide }: GlobalSetupContext) {
  // 只在所有测试开始前执行一次
  provide('dbUrl', 'mongodb://localhost:27017/test')
}

export function teardown() {
  // 所有测试结束后执行一次
}
```

## 6. 自定义 Matcher

在 setup 文件中注册自定义 matcher：

```typescript
// setup.ts
import { expect } from 'vitest'

expect.extend({
  toBeValidDate(received) {
    const pass = received instanceof Date && !isNaN(received.getTime())
    return {
      pass,
      message: () => `expected ${received} to be a valid Date`,
    }
  },
})
```

```typescript
// test file
test('custom matcher', () => {
  expect(new Date()).toBeValidDate()
})
```

## 7. Mock 全局对象

```typescript
// setup.ts
import { vi } from 'vitest'

// Mock window.matchMedia
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: vi.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: vi.fn(),
    removeListener: vi.fn(),
  })),
})
```

## 8. 多个 setup 文件

```typescript
test: {
  setupFiles: [
    './setup-mocks.ts',
    './setup-matchers.ts',
    './setup-globals.ts',
  ],
}
```

多个 setup 文件按数组顺序执行。

## 9. 注意事项

- setup 文件中的代码在每个测试文件执行前运行
- setup 文件中的 `vi.mock` 会影响所有测试
- `globalSetup` 只执行一次，适合初始化数据库等重量级操作
