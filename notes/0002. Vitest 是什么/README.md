# [0002. Vitest 是什么](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0002.%20Vitest%20%E6%98%AF%E4%BB%80%E4%B9%88)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Vitest 的定位](#3-vitest-的定位)
  - [3.1. 核心特点](#31-核心特点)
  - [3.2. 设计理念](#32-设计理念)
- [4. 和 Vite 的关系](#4-和-vite-的关系)
  - [4.1. 共享基础设施](#41-共享基础设施)
  - [4.2. 配置复用](#42-配置复用)
- [5. 适用场景](#5-适用场景)
  - [5.1. 最佳适用场景](#51-最佳适用场景)
  - [5.2. 也可以使用](#52-也可以使用)
  - [5.3. 不太适合的场景](#53-不太适合的场景)
- [6. 和 Jest 的区别](#6-和-jest-的区别)
  - [6.1. 相同点](#61-相同点)
  - [6.2. 核心差异](#62-核心差异)
  - [6.3. 语法差异示例](#63-语法差异示例)
  - [6.4. 选择建议](#64-选择建议)
- [7. 总结](#7-总结)

<!-- endregion:toc -->

## 1. 本节内容

- Vitest 的定位：基于 Vite 的单元测试框架
- 和 Vite 的关系：共享基础设施、配置复用
- 适用场景：Vite 项目、Vue/React/Svelte、TypeScript、ESM、Monorepo
- 和 Jest 的区别：API 兼容但性能更优、原生 ESM 支持

## 2. 评价

Vitest 是 Vite 生态的官方测试框架，解决了传统测试工具在 Vite 项目中配置复杂、启动缓慢的问题。它与 Vite 深度集成，复用相同的模块解析、插件系统和 TypeScript 处理逻辑，使得测试配置极其简洁。同时保持与 Jest API 的高度兼容，降低了迁移成本。

## 3. Vitest 的定位

Vitest 是一个基于 Vite 的单元测试框架，专为现代前端项目设计。

### 3.1. 核心特点

- 极速启动：利用 Vite 的按需编译能力，测试启动速度极快
- 原生 ESM 支持：无需额外配置，直接支持 ES Modules
- TypeScript 优先：开箱即用的 TypeScript 支持
- 兼容 Jest API：学习成本低，迁移简单
- 热模块替换：Watch 模式下修改代码立即重新测试

### 3.2. 设计理念

Vitest 的设计目标是成为 Vite 生态的官方测试解决方案，填补 Vite 项目在测试领域的空白。它解决了传统测试工具（如 Jest）在 Vite 项目中配置复杂、启动缓慢的问题。

## 4. 和 Vite 的关系

### 4.1. 共享基础设施

Vitest 和 Vite 共享相同的底层架构：

| 特性       | 说明                           |
| ---------- | ------------------------------ |
| 依赖解析   | 使用相同的模块解析算法         |
| 插件系统   | 兼容 Vite 插件生态             |
| 配置文件   | 可直接复用 `vite.config.ts`    |
| TypeScript | 共享相同的 TypeScript 处理逻辑 |
| 别名解析   | `resolve.alias` 配置完全一致   |

### 4.2. 配置复用

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': '/src',
    },
  },
})
```

Vitest 会自动读取 `vite.config.ts`，无需重复配置。如果需要测试专用配置，可以在 `vitest.config.ts` 中扩展：

```typescript
// vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import viteConfig from './vite.config'

export default mergeConfig(
  viteConfig,
  defineConfig({
    test: {
      environment: 'jsdom',
      globals: true,
    },
  }),
)
```

## 5. 适用场景

### 5.1. 最佳适用场景

| 场景             | 说明                       | 推荐度     |
| ---------------- | -------------------------- | ---------- |
| Vite 项目        | 零配置集成，性能最优       | ⭐⭐⭐⭐⭐ |
| Vue/React/Svelte | 组件测试支持完善           | ⭐⭐⭐⭐⭐ |
| TypeScript 项目  | 原生支持，无需额外配置     | ⭐⭐⭐⭐⭐ |
| ESM 项目         | 完美的 ES Modules 支持     | ⭐⭐⭐⭐⭐ |
| Monorepo         | Workspace 模式支持多包测试 | ⭐⭐⭐⭐   |
| 库开发           | 覆盖率、类型测试、快照测试 | ⭐⭐⭐⭐   |

### 5.2. 也可以使用

| 场景                 | 说明                     | 推荐度   |
| -------------------- | ------------------------ | -------- |
| 纯 Node.js 项目      | 可用，但 Jest 可能更成熟 | ⭐⭐⭐   |
| 遗留 CommonJS 项目   | 支持，但可能需要额外配置 | ⭐⭐⭐   |
| 已有 Jest 项目的迁移 | API 兼容，迁移成本低     | ⭐⭐⭐⭐ |

### 5.3. 不太适合的场景

| 场景               | 说明                       | 替代方案            |
| ------------------ | -------------------------- | ------------------- |
| 端到端测试         | Vitest 专注于单元/集成测试 | Cypress, Playwright |
| 非 JavaScript 项目 | 仅支持 JS/TS 生态          | 对应语言的测试框架  |
| 极老的项目         | 可能需要大量 polyfill      | Jest, Mocha         |

## 6. 和 Jest 的区别

### 6.1. 相同点

- API 高度兼容（`describe`, `test`, `expect`, `vi.fn` 等）
- 支持快照测试
- 支持 Mock 和 Spy
- 支持覆盖率报告
- 支持并行测试

### 6.2. 核心差异

| 特性       | Vitest               | Jest                      |
| ---------- | -------------------- | ------------------------- |
| 构建工具   | 基于 Vite            | 基于自定义转换层          |
| ESM 支持   | 原生支持，零配置     | 需要实验性标志或转换      |
| TypeScript | 原生支持，利用 Vite  | 需要 `ts-jest` 或 `babel` |
| 启动速度   | 极快（秒级）         | 较慢（需要预编译）        |
| HMR        | 支持热模块替换       | 不支持                    |
| 配置复杂度 | 低（复用 Vite 配置） | 中等（需要单独配置）      |
| 浏览器模式 | 支持真实浏览器测试   | 不支持                    |
| 依赖处理   | 按需编译             | 全量转换                  |

### 6.3. 语法差异示例

虽然 API 兼容，但有一些细微差异：

```typescript
// Jest
jest.fn()
jest.mock('./module')
jest.useFakeTimers()

// Vitest
vi.fn()
vi.mock('./module')
vi.useFakeTimers()
```

```typescript
// Jest - 手动 mock
jest.mock('./api', () => ({
  fetchUser: jest.fn(),
}))

// Vitest - 类似，但支持自动 mock
vi.mock('./api', () => ({
  fetchUser: vi.fn(),
}))
```

### 6.4. 选择建议

| 情况               | 建议                        |
| ------------------ | --------------------------- |
| 新项目使用 Vite    | 选择 Vitest                 |
| 新项目使用 Webpack | 可以选择 Vitest 或 Jest     |
| 已有 Jest 项目     | 考虑迁移到 Vitest（成本低） |
| 需要浏览器测试     | 选择 Vitest（Browser Mode） |

## 7. 总结

Vitest 是 Vite 生态的官方测试框架，具有以下核心优势：

1. 性能卓越：利用 Vite 的按需编译，启动和执行速度极快
2. 零配置：与 Vite 项目无缝集成，TypeScript、ESM 开箱即用
3. 生态兼容：兼容 Jest API，学习成本低，迁移简单
4. 功能完整：覆盖单元测试、集成测试、组件测试、快照测试等
5. 现代化：原生支持 ESM、TypeScript、浏览器模式等现代特性

如果你正在使用 Vite 构建项目，Vitest 是最佳的测试框架选择。如果你的项目使用其他构建工具，Vitest 仍然是一个值得考虑的优秀选择。
