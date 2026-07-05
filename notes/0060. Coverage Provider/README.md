# [0060. Coverage Provider](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0060.%20Coverage%20Provider)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. v8 provider](#3-v8-provider)
- [4. istanbul provider](#4-istanbul-provider)
- [5. 差异对比](#5-差异对比)
- [6. 选择建议](#6-选择建议)
- [7. v8 详细配置](#7-v8-详细配置)
- [8. istanbul 详细配置](#8-istanbul-详细配置)
- [9. 报告输出](#9-报告输出)
- [10. CI 集成](#10-ci-集成)

<!-- endregion:toc -->

## 1. 本节内容

- v8 provider 使用 V8 引擎内置能力
- istanbul provider 使用代码插桩
- 差异对比（速度/精度/开销）
- 选择建议
- CI 集成

## 2. 评价

大多数项目推荐使用 v8 provider，速度快且配置简单。istanbul 精度更高但需要插桩，适合对覆盖率精度有严格要求的场景。

## 3. v8 provider

`v8` provider 使用 V8 引擎内置的覆盖率收集能力：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
    },
  },
})
```

安装：

```bash
pnpm add -D @vitest/coverage-v8
```

特点：

- 速度快，开销小
- 不需要代码插桩
- 与 V8 引擎原生集成

## 4. istanbul provider

`istanbul` provider 使用 Istanbul 进行代码插桩：

```typescript
coverage: {
  provider: 'istanbul',
}
```

安装：

```bash
pnpm add -D @vitest/coverage-istanbul
```

特点：

- 覆盖率数据更精确
- 支持更多自定义选项
- 社区生态更成熟

## 5. 差异对比

| 维度 | v8       | istanbul       |
| ---- | -------- | -------------- |
| 速度 | 快       | 较慢           |
| 精度 | 基本准确 | 更精确         |
| 开销 | 低       | 中（需要插桩） |
| 配置 | 简单     | 丰富           |

## 6. 选择建议

- 大多数项目：推荐 `v8`，速度快、配置简单
- 需要精确覆盖率：选择 `istanbul`
- 兼容性问题：如果 `v8` 有问题，切换到 `istanbul`

## 7. v8 详细配置

```typescript
coverage: {
  provider: 'v8',
  // 收集覆盖率的文件
  include: ['src/**'],
  // 排除的文件
  exclude: ['src/**/*.test.ts'],
  // 报告格式
  reporter: ['text', 'html', 'lcov'],
  // 输出目录
  reportsDirectory: './coverage',
}
```

## 8. istanbul 详细配置

```typescript
coverage: {
  provider: 'istanbul',
  // 检测粒度
  100: true,
  // 忽略的行
  ignoreClassMethods: ['toString'],
}
```

## 9. 报告输出

```typescript
coverage: {
  reporter: ['text', 'html', 'json', 'lcov'],
  reportsDirectory: './coverage',
}
```

`html` 报告可以在浏览器中查看详细的覆盖率信息。

## 10. CI 集成

```yaml
# GitHub Actions
- name: Run tests with coverage
  run: vitest run --coverage

- name: Upload coverage
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```
