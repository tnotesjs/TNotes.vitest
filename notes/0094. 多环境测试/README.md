# [0094. 多环境测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0094.%20%E5%A4%9A%E7%8E%AF%E5%A2%83%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Node 环境项目](#3-node-环境项目)
- [4. jsdom 环境项目](#4-jsdom-环境项目)
- [5. Browser Mode 项目](#5-browser-mode-项目)
- [6. 混合环境配置](#6-混合环境配置)
- [7. 运行指定环境](#7-运行指定环境)
- [8. 环境隔离](#8-环境隔离)
- [9. 性能考虑](#9-性能考虑)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Node 环境项目
- jsdom 环境项目
- Browser Mode 项目
- 混合环境配置
- 运行指定环境
- 环境隔离
- 性能考虑

## 2. 评价

Workspace 支持为不同项目配置不同测试环境。Node 适合工具库，jsdom 适合前端组件，Browser Mode 需要真实浏览器。

## 3. Node 环境项目

```typescript
// vitest.workspace.ts
export default defineWorkspace([
  {
    name: 'server',
    root: './packages/server',
    test: {
      environment: 'node',
    },
  },
])
```

适合：服务端代码、CLI 工具、工具函数库

## 4. jsdom 环境项目

```typescript
export default defineWorkspace([
  {
    name: 'frontend',
    root: './packages/frontend',
    test: {
      environment: 'jsdom',
    },
  },
])
```

适合：前端组件、DOM 操作、浏览器 API

## 5. Browser Mode 项目

```typescript
export default defineWorkspace([
  {
    name: 'e2e',
    root: './packages/e2e',
    test: {
      browser: {
        enabled: true,
        provider: 'playwright',
        instances: [{ browser: 'chromium' }],
      },
    },
  },
])
```

适合：需要真实浏览器环境的测试

## 6. 混合环境配置

```typescript
export default defineWorkspace([
  {
    name: 'core',
    root: './packages/core',
    test: { environment: 'node' },
  },
  {
    name: 'ui',
    root: './packages/ui',
    test: { environment: 'jsdom' },
  },
  {
    name: 'e2e',
    root: './packages/e2e',
    test: {
      browser: {
        enabled: true,
        provider: 'playwright',
      },
    },
  },
])
```

## 7. 运行指定环境

```bash
# 只运行 Node 环境的测试
vitest run --project core

# 只运行 jsdom 环境的测试
vitest run --project ui
```

## 8. 环境隔离

每个项目在独立的环境中运行，不会相互影响：

- Node 项目无法访问 DOM API
- jsdom 项目有模拟的 DOM
- Browser 项目有真实的浏览器

## 9. 性能考虑

不同环境的性能特征：

| 环境    | 启动速度 | 运行速度 | 资源消耗 |
| ------- | -------- | -------- | -------- |
| Node    | 快       | 快       | 低       |
| jsdom   | 中       | 中       | 中       |
| Browser | 慢       | 慢       | 高       |

## 10. 注意事项

- 环境选择应根据测试需求
- 大部分测试使用 Node 环境
- 需要 DOM 的测试使用 jsdom
- 需要真实浏览器的测试使用 Browser Mode
