# [0009. TypeScript 支持](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0009.%20TypeScript%20%E6%94%AF%E6%8C%81)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 开箱即用](#3-开箱即用)
- [4. 类型声明](#4-类型声明)
- [5. vitest/globals 配置](#5-vitestglobals-配置)
- [6. tsconfig 配置](#6-tsconfig-配置)
  - [6.1. 路径别名](#61-路径别名)
- [7. JSX/TSX 支持](#7-jsxtsx-支持)
- [8. 注意事项](#8-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 开箱即用的 TypeScript 支持
- 类型声明
- vitest/globals 全局类型
- tsconfig 配置
- 路径别名
- JSX/TSX 支持

## 2. 评价

Vitest 内置 esbuild 转译 TypeScript，无需额外配置。配合 vitest/globals 类型声明，编辑器可以提供完整的类型提示和自动补全。

## 3. 开箱即用

Vitest 内置了基于 esbuild 的 TypeScript 转译能力，不需要安装 `ts-jest` 或配置 Babel。只要安装 Vitest 就可以直接写 `.ts` 格式的测试文件。

```typescript
// utils.test.ts - 直接写 TypeScript
import { describe, it, expect } from 'vitest'

interface User {
  name: string
  age: number
}

function greet(user: User): string {
  return `Hello, ${user.name}!`
}

describe('greet', () => {
  it('返回问候语', () => {
    expect(greet({ name: 'Alice', age: 25 })).toBe('Hello, Alice!')
  })
})
```

## 4. 类型声明

Vitest 提供了完整的类型声明文件。安装后会在 `node_modules/vitest` 下找到类型定义。

如果需要全局使用 `describe`、`it`、`expect` 等 API（不需要手动 import），需要在 `tsconfig.json` 中添加类型引用：

```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

添加后，TypeScript 编译器会识别全局测试 API，编辑器也能提供自动补全和类型检查。

## 5. vitest/globals 配置

除了在 `tsconfig.json` 中添加类型声明，还需要在 Vitest 配置中启用 globals：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    globals: true,
  },
})
```

启用后，以下 API 会成为全局变量：

- `describe`
- `it` / `test`
- `expect`
- `vi`
- `beforeAll` / `beforeEach` / `afterAll` / `afterEach`

这样在测试文件中不需要写 `import { describe, it, expect } from 'vitest'`。

## 6. tsconfig 配置

推荐为测试文件单独配置 `tsconfig.json`，避免测试代码污染生产代码的类型检查：

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "types": ["vitest/globals"]
  },
  "include": ["src/**/*.ts", "src/**/*.test.ts"]
}
```

### 6.1. 路径别名

如果 `tsconfig.json` 中配置了路径别名，Vitest 需要通过 `vite-tsconfig-paths` 插件来解析：

```bash
pnpm add -D vite-tsconfig-paths
```

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [tsconfigPaths()],
  test: {
    globals: true,
  },
})
```

## 7. JSX/TSX 支持

Vitest 通过 Vite 插件支持 JSX/TSX 语法。在 `tsconfig.json` 中配置：

```json
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "jsxImportSource": "react"
  }
}
```

或者对于 Vue：

```json
{
  "compilerOptions": {
    "jsx": "preserve"
  }
}
```

然后确保 Vite 配置中包含了对应的 JSX 插件（如 `@vitejs/plugin-react`），Vitest 会自动复用。

## 8. 注意事项

- Vitest 使用 esbuild 转译 TypeScript，不做类型检查。如果需要类型检查，请单独运行 `tsc --noEmit`
- `vitest/globals` 的类型声明只作用于测试文件，不会影响生产代码
- 如果项目中有 `strict: true`，测试文件也会受到严格类型检查
