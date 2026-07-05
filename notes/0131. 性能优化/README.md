# [0131. 性能优化](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0131.%20%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 并发执行](#3-并发执行)
- [4. 测试拆分](#4-测试拆分)
- [5. 依赖优化](#5-依赖优化)
- [6. CI 缓存](#6-ci-缓存)
- [7. 增量测试](#7-增量测试)
- [8. 排除慢测试](#8-排除慢测试)
- [9. 并行与串行](#9-并行与串行)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 并发执行（pool: 'threads'）
- 测试拆分（多个小文件）
- 依赖优化（deps.inline）
- CI 缓存（node_modules）
- 增量测试（--changed）
- 排除慢测试
- 并行与串行选择

## 2. 评价

性能优化的核心是充分利用并发能力。配置线程池、拆分测试文件、缓存依赖都可以显著提高测试速度。

## 3. 并发执行

```typescript
// vitest.config.ts
test: {
  pool: 'threads',
  poolOptions: {
    threads: {
      maxThreads: 4,
    },
  },
}
```

## 4. 测试拆分

将大型测试文件拆分为多个小文件：

```
src/
├── utils/
│   ├── math.test.ts
│   ├── string.test.ts
│   └── array.test.ts
```

## 5. 依赖优化

```typescript
test: {
  deps: {
    inline: [/some-package/],
  },
}
```

## 6. CI 缓存

```yaml
- uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-pnpm-${{ hashFiles('pnpm-lock.yaml') }}
```

## 7. 增量测试

```bash
# 只运行变更的测试
vitest run --changed
```

## 8. 排除慢测试

```typescript
test: {
  exclude: ['**/*.slow.test.ts'],
}
```

## 9. 并行与串行

```typescript
// 并行执行
test.concurrent('test 1', () => {})
test.concurrent('test 2', () => {})

// 串行执行
test.sequential('test 3', () => {})
```

## 10. 注意事项

- 并行执行可能有竞态条件
- 使用 `isolate: true` 确保隔离
- 监控测试执行时间
