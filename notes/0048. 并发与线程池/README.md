# [0048. 并发与线程池](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0048.%20%E5%B9%B6%E5%8F%91%E4%B8%8E%E7%BA%BF%E7%A8%8B%E6%B1%A0)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. pool 选项](#3-pool-选项)
- [4. threads 模式](#4-threads-模式)
- [5. forks 模式](#5-forks-模式)
- [6. vmThreads 模式](#6-vmthreads-模式)
- [7. 并发控制](#7-并发控制)
- [8. 隔离策略](#8-隔离策略)
- [9. 推荐配置](#9-推荐配置)

<!-- endregion:toc -->

## 1. 本节内容

- pool 选项（threads/forks/vmThreads）
- threads 模式
- forks 模式
- vmThreads 模式
- 并发控制
- 隔离策略

## 2. 评价

forks 模式是默认选择，使用 child_process 实现进程级隔离。threads 模式启动更快且共享进程内存，但隔离性较弱。vmThreads 提供最强隔离但某些 API 可能不可用。

## 3. pool 选项

`test.pool` 指定测试运行的线程池类型：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    pool: 'forks', // 默认值，也可选择 'threads'、'vmThreads'、'vmForks'
  },
})
```

可选值：

| pool        | 说明                          |
| ----------- | ----------------------------- |
| `forks`     | 使用 child_process（默认）    |
| `threads`   | 使用 worker_threads           |
| `vmThreads` | 使用 worker_threads + VM 隔离 |
| `vmForks`   | 使用 child_process + VM 隔离  |

## 4. threads 模式

`threads` 使用 Node.js 的 `worker_threads`：

```typescript
test: {
  pool: 'threads',
  poolOptions: {
    threads: {
      minThreads: 1,
      maxThreads: 4,
      singleThread: false,
    },
  },
}
```

特点：

- 共享进程内存
- 启动速度快
- 隔离性较弱

## 5. forks 模式

`forks` 使用 `child_process.fork`，每个测试文件在独立进程中运行：

```typescript
test: {
  pool: 'forks',
  poolOptions: {
    forks: {
      minForks: 1,
      maxForks: 4,
      singleFork: false,
    },
  },
}
```

特点：

- 进程级隔离
- 内存隔离更彻底
- 启动速度较慢

## 6. vmThreads 模式

`vmThreads` 在 worker_threads 中使用 VM 隔离：

```typescript
test: {
  pool: 'vmThreads',
  poolOptions: {
    vmThreads: {
      minThreads: 1,
      maxThreads: 4,
    },
  },
}
```

特点：

- 每个测试文件有独立的全局上下文
- 兼顾性能和隔离性
- 某些 Node.js API 可能不可用

## 7. 并发控制

```typescript
test: {
  // 文件级并行
  fileParallelism: true,
  // 最大并发数
  maxConcurrency: 5,
}
```

`fileParallelism` 控制测试文件是否并行执行。`maxConcurrency` 限制同时运行的测试数量。

## 8. 隔离策略

```typescript
test: {
  isolate: true, // 默认值
}
```

`isolate: true` 确保每个测试文件在独立的上下文中运行，避免全局状态污染。

设为 `false` 可以提高性能，但可能导致测试间相互影响。

## 9. 推荐配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    pool: 'threads',
    poolOptions: {
      threads: {
        minThreads: 1,
        maxThreads: 4,
      },
    },
    fileParallelism: true,
    maxConcurrency: 5,
    isolate: true,
  },
})
```
