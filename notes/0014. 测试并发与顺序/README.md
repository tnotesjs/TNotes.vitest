# [0014. 测试并发与顺序](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0014.%20%E6%B5%8B%E8%AF%95%E5%B9%B6%E5%8F%91%E4%B8%8E%E9%A1%BA%E5%BA%8F)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 默认行为](#3-默认行为)
- [4. test.concurrent](#4-testconcurrent)
- [5. describe.concurrent](#5-describeconcurrent)
- [6. test.sequential](#6-testsequential)
- [7. 文件级并发控制](#7-文件级并发控制)
- [8. 线程池配置](#8-线程池配置)
- [9. 并发测试的注意事项](#9-并发测试的注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 默认行为（文件间并行、文件内串行）
- test.concurrent 并行测试
- describe.concurrent 分组并行
- test.sequential 强制串行
- 线程池配置

## 2. 评价

并发执行可以加速测试运行，但需要注意共享状态的竞态条件。对于有状态依赖的测试，使用 test.sequential 确保执行顺序。

## 3. 默认行为

默认情况下，Vitest 会并行运行不同的测试文件，但同一个文件内的测试用例是串行执行的。

这是因为同一文件内的测试可能共享状态（比如 `beforeEach` 中设置的变量），并行执行会导致竞态条件。

## 4. test.concurrent

使用 `test.concurrent` 可以让同一个 `describe` 内的测试用例并行执行：

```typescript
import { describe, test, expect } from 'vitest'

describe('concurrent tests', () => {
  test.concurrent('test 1', async () => {
    const result = await fetchData('api/a')
    expect(result).toBeDefined()
  })

  test.concurrent('test 2', async () => {
    const result = await fetchData('api/b')
    expect(result).toBeDefined()
  })

  test.concurrent('test 3', async () => {
    const result = await fetchData('api/c')
    expect(result).toBeDefined()
  })
})
```

三个测试会同时启动，并行执行，总耗时取决于最慢的那个。

注意：使用 `test.concurrent` 时，测试之间不能共享可变状态。

## 5. describe.concurrent

`describe.concurrent` 让整个 `describe` 内的所有测试都并行执行：

```typescript
describe.concurrent('parallel suite', () => {
  test('test 1', async () => {
    await delay(100)
    expect(true).toBe(true)
  })

  test('test 2', async () => {
    await delay(200)
    expect(true).toBe(true)
  })

  test('test 3', async () => {
    await delay(300)
    expect(true).toBe(true)
  })
})
```

等效于给每个 `test` 加上 `concurrent`。

## 6. test.sequential

`test.sequential` 强制测试串行执行，即使在并发模式下也不会并行：

```typescript
describe.concurrent('mixed suite', () => {
  test.concurrent('fast test 1', async () => {
    await delay(100)
  })

  test.sequential('must run after fast test 1', async () => {
    await delay(50)
  })

  test.concurrent('fast test 2', async () => {
    await delay(100)
  })
})
```

`sequential` 标记的测试会等待前面的测试完成后才执行，同时后续的测试也会等待它完成。

## 7. 文件级并发控制

在配置文件中可以控制文件级别的并发策略：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // 文件间串行执行
    fileParallelism: false,
  },
})
```

默认 `fileParallelism` 为 `true`，文件之间并行执行。设为 `false` 后，文件之间串行执行。

## 8. 线程池配置

Vitest 使用进程池管理并发。默认使用 `forks` 模式（基于 `child_process`），也可以切换为 `threads` 模式（基于 `worker_threads`）：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    pool: 'threads', // 默认为 'forks'
    poolOptions: {
      threads: {
        minThreads: 1,
        maxThreads: 4,
      },
    },
  },
})
```

可选的 pool 类型：

| pool        | 说明                          |
| ----------- | ----------------------------- |
| `threads`   | 使用 worker_threads（默认）   |
| `forks`     | 使用 child_process.fork       |
| `vmThreads` | 使用 worker_threads + VM 隔离 |

## 9. 并发测试的注意事项

使用 `test.concurrent` 时需要注意：

- 测试之间不能共享可变状态
- `beforeEach` 和 `afterEach` 仍然会为每个测试执行，但不保证执行顺序
- 如果测试依赖外部资源（如数据库），确保资源操作是线程安全的
- 并发测试的错误信息可能交错输出，增加调试难度
