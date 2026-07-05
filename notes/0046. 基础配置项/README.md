# [0046. 基础配置项](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0046.%20%E5%9F%BA%E7%A1%80%E9%85%8D%E7%BD%AE%E9%A1%B9)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. include](#3-include)
- [4. exclude](#4-exclude)
- [5. globals](#5-globals)
- [6. environment](#6-environment)
- [7. root](#7-root)
- [8. 完整示例](#8-完整示例)

<!-- endregion:toc -->

## 1. 本节内容

- include 指定测试文件
- exclude 排除文件
- globals 全局 API
- environment 测试环境
- root 根目录

## 2. 评价

基础配置项定义了测试运行的核心行为。include/exclude 控制测试文件范围，globals 和 environment 是最常调整的选项。

## 3. include

`test.include` 指定测试文件的匹配模式：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    include: ['src/**/*.{test,spec}.{js,ts}', 'tests/**/*.{test,spec}.{js,ts}'],
  },
})
```

默认值：

```typescript
;['**/__tests__/**/*.[jt]s?(x)', '**/?(*.){test,spec}.[jt]s?(x)']
```

## 4. exclude

`test.exclude` 排除不需要测试的文件：

```typescript
test: {
  exclude: [
    'node_modules',
    'dist',
    'build',
    '**/*.d.ts',
    '**/*.config.*',
  ],
}
```

`node_modules` 和 `dist` 是默认排除项。

## 5. globals

`test.globals` 让测试 API 成为全局变量：

```typescript
test: {
  globals: true,
}
```

启用后不需要在每个测试文件中 import：

```typescript
// 不需要 import { test, expect } from 'vitest'
test('global test', () => {
  expect(1 + 1).toBe(2)
})
```

启用 `globals` 时，建议在 `tsconfig.json` 中添加类型声明：

```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

## 6. environment

`test.environment` 设置测试环境：

```typescript
test: {
  environment: 'jsdom', // 默认为 'node'
}
```

可选值：

| 环境           | 说明                 |
| -------------- | -------------------- |
| `node`         | Node.js 环境（默认） |
| `jsdom`        | 模拟 DOM 环境        |
| `happy-dom`    | 轻量级 DOM 模拟      |
| `edge-runtime` | 边缘运行时           |

也可以通过注释在文件级别指定环境：

```typescript
// @vitest-environment jsdom
import { test, expect } from 'vitest'

test('DOM test', () => {
  document.body.innerHTML = '<div id="app"></div>'
  expect(document.getElementById('app')).toBeDefined()
})
```

## 7. root

`test.root` 指定测试的根目录：

```typescript
test: {
  root: './tests',
}
```

默认为项目根目录。

## 8. 完整示例

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    include: ['src/**/*.{test,spec}.{js,ts}'],
    exclude: ['node_modules', 'dist'],
    globals: true,
    environment: 'jsdom',
    root: '.',
  },
})
```
