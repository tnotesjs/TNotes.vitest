# [0059. 覆盖率基础](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0059.%20%E8%A6%86%E7%9B%96%E7%8E%87%E5%9F%BA%E7%A1%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 覆盖率概念](#3-覆盖率概念)
- [4. 运行覆盖率](#4-运行覆盖率)
- [5. 安装 provider](#5-安装-provider)
- [6. 配置](#6-配置)
- [7. 覆盖率报告](#7-覆盖率报告)
- [8. 阈值控制](#8-阈值控制)
- [9. 忽略覆盖率](#9-忽略覆盖率)
- [10. include / exclude](#10-include--exclude)

<!-- endregion:toc -->

## 1. 本节内容

- 四个覆盖率维度（行/函数/分支/语句）
- 运行覆盖率命令
- 安装 provider（v8/istanbul）
- 配置
- 报告格式
- 阈值控制
- 忽略覆盖率

## 2. 评价

覆盖率衡量测试对源代码的覆盖程度，是代码质量的重要指标。v8 provider 速度快、开销小，是推荐选择。

## 3. 覆盖率概念

代码覆盖率衡量测试对源代码的覆盖程度，有四个维度：

- 行覆盖率（Lines）：被执行的代码行数比例
- 函数覆盖率（Functions）：被调用的函数比例
- 分支覆盖率（Branches）：被执行的分支（if/else 等）比例
- 语句覆盖率（Statements）：被执行的语句比例

## 4. 运行覆盖率

```bash
vitest run --coverage
```

## 5. 安装 provider

需要安装覆盖率 provider：

```bash
# v8 provider（推荐）
pnpm add -D @vitest/coverage-v8

# 或 istanbul provider
pnpm add -D @vitest/coverage-istanbul
```

## 6. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      enabled: true,
    },
  },
})
```

## 7. 覆盖率报告

```typescript
coverage: {
  provider: 'v8',
  reporter: ['text', 'html', 'lcov'],
}
```

常用 reporter：

| reporter | 说明                         |
| -------- | ---------------------------- |
| `text`   | 终端输出                     |
| `html`   | 生成 HTML 报告               |
| `lcov`   | 生成 lcov 格式，用于 CI 集成 |
| `json`   | JSON 格式                    |

## 8. 阈值控制

设置覆盖率最低要求：

```typescript
coverage: {
  thresholds: {
    lines: 80,
    functions: 80,
    branches: 80,
    statements: 80,
  },
}
```

低于阈值时测试会失败。

## 9. 忽略覆盖率

```typescript
// 忽略单行
const value = someFunction() // v8 ignore next line

// 忽略代码块
/* v8 ignore start */
if (condition) {
  // 这段代码不计入覆盖率
}
/* v8 ignore stop */
```

## 10. include / exclude

```typescript
coverage: {
  include: ['src/**'],
  exclude: [
    'src/**/*.test.ts',
    'src/**/*.spec.ts',
    'src/**/*.d.ts',
  ],
}
```
