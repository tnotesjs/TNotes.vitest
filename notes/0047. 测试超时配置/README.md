# [0047. 测试超时配置](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0047.%20%E6%B5%8B%E8%AF%95%E8%B6%85%E6%97%B6%E9%85%8D%E7%BD%AE)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. testTimeout](#3-testtimeout)
- [4. 为单个测试设置超时](#4-为单个测试设置超时)
- [5. hookTimeout](#5-hooktimeout)
- [6. 慢测试警告](#6-慢测试警告)
- [7. 测试重试](#7-测试重试)
- [8. 完整超时配置](#8-完整超时配置)
- [9. 注意事项](#9-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- testTimeout 测试超时（默认 5 秒）
- 单个测试超时
- hookTimeout 钩子超时
- 慢测试警告（slowThreshold）
- 测试重试（retry）

## 2. 评价

超时配置防止测试无限挂起。异步测试容易超时，应设置合理的超时时间。retry 可以容忍偶发失败，但不应过度依赖。

## 3. testTimeout

`test.testTimeout` 设置单个测试用例的超时时间，默认 5000 毫秒：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    testTimeout: 10000, // 10 秒
  },
})
```

测试执行超过此时间会失败：

```typescript
test('slow test', async () => {
  await new Promise((resolve) => setTimeout(resolve, 6000))
  // 默认配置下会超时失败
})
```

## 4. 为单个测试设置超时

```typescript
test('specific timeout', async () => {
  const result = await slowOperation()
  expect(result).toBeDefined()
}, 15000) // 15 秒超时
```

## 5. hookTimeout

`test.hookTimeout` 设置生命周期钩子的超时时间，默认 10000 毫秒：

```typescript
test: {
  hookTimeout: 15000, // 15 秒
}
```

影响的钩子：

- `beforeAll`
- `afterAll`
- `beforeEach`
- `afterEach`

## 6. 慢测试警告

`test.slowThreshold` 设置慢测试的警告阈值，默认 300 毫秒：

```typescript
test: {
  slowThreshold: 500, // 超过 500ms 标记为慢测试
}
```

慢测试会在报告中标记出来，帮助识别需要优化的测试。

## 7. 测试重试

`test.retry` 设置失败测试的重试次数：

```typescript
test: {
  retry: 2, // 失败后重试 2 次
}
```

也可以在单个测试上设置：

```typescript
test('flaky test', { retry: 3 }, async () => {
  const result = await flakyOperation()
  expect(result).toBeDefined()
})
```

## 8. 完整超时配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    testTimeout: 10000,
    hookTimeout: 15000,
    slowThreshold: 500,
    retry: 2,
  },
})
```

## 9. 注意事项

- 异步测试容易超时，确保使用 `async/await` 正确等待
- `beforeAll` 中的数据库连接等操作可能需要更长的超时时间
- 慢测试可能是性能问题的信号，应考虑优化
