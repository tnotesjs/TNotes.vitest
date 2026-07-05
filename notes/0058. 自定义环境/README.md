# [0058. 自定义环境](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0058.%20%E8%87%AA%E5%AE%9A%E4%B9%89%E7%8E%AF%E5%A2%83)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 自定义环境结构](#3-自定义环境结构)
- [4. setup 函数](#4-setup-函数)
- [5. teardown 函数](#5-teardown-函数)
- [6. 提供全局变量](#6-提供全局变量)
- [7. 使用自定义环境](#7-使用自定义环境)
- [8. 传递选项](#8-传递选项)
- [9. 基于现有环境扩展](#9-基于现有环境扩展)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Environment 接口结构
- setup 函数初始化
- teardown 函数清理
- 提供全局变量
- 使用自定义环境
- 传递选项
- 基于现有环境扩展

## 2. 评价

自定义环境允许创建特定的测试运行时，适合需要特殊环境配置的场景。可以基于 jsdom 等现有环境扩展，添加自定义全局变量。

## 3. 自定义环境结构

自定义环境需要导出一个符合 `Environment` 接口的对象：

```typescript
// my-environment.ts
import type { Environment } from 'vitest/environments'

export default <Environment>{
  name: 'my-environment',
  setup() {
    // 环境初始化
    return {
      teardown() {
        // 环境清理
      },
    }
  },
}
```

## 4. setup 函数

`setup` 函数在测试文件执行前调用，用于初始化环境：

```typescript
setup() {
  // 设置全局对象
  globalThis.myGlobal = 'value'

  // 返回 teardown 函数
  return {
    teardown() {
      delete globalThis.myGlobal
    },
  }
}
```

## 5. teardown 函数

`teardown` 函数在测试文件执行后调用，用于清理环境：

```typescript
setup() {
  const server = createMockServer()

  return {
    teardown() {
      server.close()
    },
  }
}
```

## 6. 提供全局变量

```typescript
// custom-dom-environment.ts
import type { Environment } from 'vitest/environments'

export default <Environment>{
  name: 'custom-dom',
  setup() {
    // 模拟 DOM 环境
    globalThis.document = {
      getElementById: (id) => ({ id, innerHTML: '' }),
    }
    globalThis.window = globalThis

    return {
      teardown() {
        delete globalThis.document
        delete globalThis.window
      },
    }
  },
}
```

## 7. 使用自定义环境

```typescript
// @vitest-environment custom-dom
import { test, expect } from 'vitest'

test('custom environment', () => {
  expect(document.getElementById('test')).toBeDefined()
})
```

或者在配置中使用：

```typescript
test: {
  environment: './my-environment.ts',
}
```

## 8. 传递选项

```typescript
// my-environment.ts
import type { Environment } from 'vitest/environments'

export default (options?: { debug?: boolean }): Environment => ({
  name: 'my-environment',
  setup() {
    if (options?.debug) {
      console.log('Environment setup')
    }
    return {
      teardown() {},
    }
  },
})
```

## 9. 基于现有环境扩展

```typescript
import { environments } from 'vitest/environments'

export default {
  name: 'extended-jsdom',
  async setup() {
    const jsdom = await environments.jsdom.setup()
    // 添加自定义全局变量
    globalThis.customApi = {}
    return {
      async teardown() {
        delete globalThis.customApi
        await jsdom.teardown()
      },
    }
  },
}
```

## 10. 注意事项

- 自定义环境需要正确清理资源，避免影响其他测试
- 全局变量的修改应在 teardown 中恢复
- 自定义环境适合需要特殊运行时配置的场景
