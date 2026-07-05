# [0040. ESM 与 CommonJS](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0040.%20ESM%20%E4%B8%8E%20CommonJS)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Vitest 的 ESM 优先](#3-vitest-的-esm-优先)
- [4. CommonJS 支持](#4-commonjs-支持)
- [5. package.json 中的 type](#5-packagejson-中的-type)
- [6. 文件扩展名](#6-文件扩展名)
- [7. 模块解析差异](#7-模块解析差异)
- [8. 外部依赖处理](#8-外部依赖处理)
- [9. 与 Jest 的差异](#9-与-jest-的差异)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Vitest 的 ESM 优先模型
- CommonJS 支持
- package.json type 字段
- 文件扩展名
- 模块解析差异
- 外部依赖处理

## 2. 评价

Vitest 原生支持 ESM，不需要额外转换。同时兼容 CommonJS 模块，通过 Vite 自动处理模块格式差异。

## 3. Vitest 的 ESM 优先

Vitest 内部使用 Vite 的模块系统，原生支持 ESM。测试文件中的 `import`/`export` 语句不需要任何转换：

```typescript
// math.ts - ESM 模块
export function add(a: number, b: number): number {
  return a + b
}

// math.test.ts - 直接使用 ESM import
import { add } from './math'
import { test, expect } from 'vitest'

test('add', () => {
  expect(add(1, 2)).toBe(3)
})
```

## 4. CommonJS 支持

Vitest 也支持 CommonJS 模块，通过 Vite 自动转换：

```javascript
// utils.cjs - CommonJS 模块
function multiply(a, b) {
  return a * b
}

module.exports = { multiply }
```

```typescript
// 测试文件中可以直接 import CommonJS 模块
import { multiply } from './utils.cjs'

test('multiply', () => {
  expect(multiply(2, 3)).toBe(6)
})
```

## 5. package.json 中的 type

```json
{
  "type": "module"
}
```

设置 `"type": "module"` 后，`.js` 文件被视为 ESM 模块。不设置则视为 CommonJS。

Vitest 对两种模式都支持良好。

## 6. 文件扩展名

| 扩展名 | 模块类型                                 |
| ------ | ---------------------------------------- |
| `.mjs` | ESM（无论 package.json 配置）            |
| `.cjs` | CommonJS（无论 package.json 配置）       |
| `.js`  | 取决于 `package.json` 的 `type` 字段     |
| `.ts`  | TypeScript，由 esbuild 转译后按 ESM 处理 |

## 7. 模块解析差异

Vitest 使用 Vite 的模块解析，与 Node.js 的原生解析有一些差异：

- Vitest 支持 `resolve.alias` 路径别名
- Vitest 支持导入 CSS、图片等非 JS 资源
- Vitest 支持 `import.meta.glob` 等 Vite 特有 API

## 8. 外部依赖处理

Vitest 默认会对 `node_modules` 中的依赖进行转换（内联），以确保 ESM 兼容性：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    deps: {
      inline: [/some-package/], // 强制内联某些依赖
    },
  },
})
```

## 9. 与 Jest 的差异

Jest 默认使用 CommonJS 模块系统，要使用 ESM 需要：

- 添加 `--experimental-vm-modules` 标志
- 或使用 Babel/SWC 转换 ESM 为 CommonJS

Vitest 则直接支持 ESM，不需要额外配置。

## 10. 注意事项

- Vitest 使用 esbuild 转译 TypeScript，不做类型检查
- `vi.mock` 会自动提升到文件顶部，这与 ESM 的静态分析行为一致
- 某些 Node.js 原生模块在 Vitest 中可能需要特殊处理
