# [0092. Workspace 概念](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0092.%20Workspace%20%E6%A6%82%E5%BF%B5)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 什么是 Workspace](#3-什么是-workspace)
- [4. 适用场景](#4-适用场景)
- [5. 工作原理](#5-工作原理)
- [6. 与单项目配置的区别](#6-与单项目配置的区别)
- [7. 项目标识](#7-项目标识)
- [8. 测试结果](#8-测试结果)
- [9. 独立运行](#9-独立运行)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Workspace 多项目测试组织
- 适用场景（Monorepo/多环境）
- 工作原理
- 与单项目配置的区别
- 项目标识
- 独立运行

## 2. 评价

Workspace 用于在多个项目或目录中组织和运行测试，适合 Monorepo 和需要多环境测试的场景。

## 3. 什么是 Workspace

Vitest Workspace 用于在多个项目或目录中组织和运行测试：

```
my-project/
├── vitest.workspace.ts
├── packages/
│   ├── core/
│   ├── ui/
│   └── utils/
```

## 4. 适用场景

- Monorepo 多包项目
- 不同目录需要不同配置
- 多环境测试（Node、jsdom、browser）
- 共享配置，独立运行

## 5. 工作原理

Workspace 会：

1. 读取 `vitest.workspace.ts` 配置
2. 为每个项目创建独立的测试环境
3. 统一运行所有项目的测试
4. 合并测试结果

## 6. 与单项目配置的区别

| 维度     | 单项目             | Workspace             |
| -------- | ------------------ | --------------------- |
| 配置文件 | `vitest.config.ts` | `vitest.workspace.ts` |
| 项目数量 | 1 个               | 多个                  |
| 环境     | 统一               | 可独立配置            |
| 运行方式 | `vitest`           | `vitest`（根目录）    |

## 7. 项目标识

每个项目可以有独立的名称：

```typescript
// vitest.workspace.ts
export default [
  {
    name: 'core',
    root: './packages/core',
  },
  {
    name: 'ui',
    root: './packages/ui',
  },
]
```

## 8. 测试结果

Workspace 运行后，测试结果会按项目分组显示：

```
 ✓ core (5 tests) 120ms
 ✓ ui (8 tests) 200ms
 ✓ utils (3 tests) 50ms
```

## 9. 独立运行

可以只运行某个项目的测试：

```bash
vitest --project core
vitest --project ui
```

## 10. 注意事项

- Workspace 配置会覆盖项目内的配置
- 每个项目可以有独立的 `vitest.config.ts`
- 根目录的配置是默认配置
