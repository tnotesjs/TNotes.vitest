# [0093. 配置 Workspace](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0093.%20%E9%85%8D%E7%BD%AE%20Workspace)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 创建配置文件](#3-创建配置文件)
- [4. 项目数组](#4-项目数组)
- [5. glob 模式](#5-glob-模式)
- [6. 项目配置](#6-项目配置)
- [7. 继承配置](#7-继承配置)
- [8. 命令行运行](#8-命令行运行)
- [9. 完整示例](#9-完整示例)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 创建 vitest.workspace.ts
- 项目数组
- glob 模式
- 项目配置
- 继承配置
- 命令行运行
- 完整示例

## 2. 评价

Workspace 配置使用 defineWorkspace 函数，支持项目数组和 glob 模式。每个项目可以有独立的配置。

## 3. 创建配置文件

在项目根目录创建 `vitest.workspace.ts`：

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  './packages/core',
  './packages/ui',
  './packages/utils',
])
```

## 4. 项目数组

使用对象数组定义项目：

```typescript
export default defineWorkspace([
  {
    name: 'core',
    root: './packages/core',
    test: {
      environment: 'node',
    },
  },
  {
    name: 'ui',
    root: './packages/ui',
    test: {
      environment: 'jsdom',
    },
  },
])
```

## 5. glob 模式

使用 glob 匹配项目目录：

```typescript
export default defineWorkspace(['./packages/*', './apps/*'])
```

## 6. 项目配置

每个项目可以有独立的配置：

```typescript
export default defineWorkspace([
  {
    name: 'core',
    root: './packages/core',
    test: {
      environment: 'node',
      include: ['**/*.test.ts'],
    },
  },
  {
    name: 'ui',
    root: './packages/ui',
    test: {
      environment: 'jsdom',
      include: ['**/*.test.tsx'],
    },
  },
])
```

## 7. 继承配置

项目可以继承根配置：

```typescript
export default defineWorkspace([
  {
    extends: true, // 继承根配置
    name: 'core',
    root: './packages/core',
  },
])
```

## 8. 命令行运行

```bash
# 运行所有项目
vitest run

# 运行指定项目
vitest run --project core

# 运行多个项目
vitest run --project core --project ui
```

## 9. 完整示例

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  {
    name: 'unit',
    root: './packages/core',
    test: {
      environment: 'node',
      include: ['src/**/*.test.ts'],
    },
  },
  {
    name: 'components',
    root: './packages/ui',
    test: {
      environment: 'jsdom',
      include: ['src/**/*.test.tsx'],
    },
  },
  {
    name: 'e2e',
    root: './e2e',
    test: {
      environment: 'browser',
    },
  },
])
```

## 10. 注意事项

- `vitest.workspace.ts` 必须在项目根目录
- 项目名称必须唯一
- 每个项目可以有独立的配置文件
