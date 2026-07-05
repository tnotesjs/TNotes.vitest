# [0061. 覆盖率配置](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0061.%20%E8%A6%86%E7%9B%96%E7%8E%87%E9%85%8D%E7%BD%AE)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. coverage.include](#3-coverageinclude)
- [4. coverage.exclude](#4-coverageexclude)
- [5. reports 配置](#5-reports-配置)
- [6. reportsDirectory](#6-reportsdirectory)
- [7. 允许未测试文件](#7-允许未测试文件)
- [8. 完整配置示例](#8-完整配置示例)
- [9. 命令行参数](#9-命令行参数)

<!-- endregion:toc -->

## 1. 本节内容

- coverage.include 指定收集文件
- coverage.exclude 排除文件
- reports 报告格式配置
- reportsDirectory 输出目录
- 允许未测试文件（all）
- 命令行参数

## 2. 评价

覆盖率配置控制收集范围和报告格式。include/exclude 使用 glob 模式，all 选项可以显示未测试文件的零覆盖率，帮助识别测试盲区。

## 3. coverage.include

`coverage.include` 指定需要收集覆盖率的文件：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      include: ['src/**'],
    },
  },
})
```

支持 glob 模式，只收集匹配文件的覆盖率。

## 4. coverage.exclude

`coverage.exclude` 排除不需要收集覆盖率的文件：

```typescript
coverage: {
  exclude: [
    'src/**/*.test.ts',
    'src/**/*.spec.ts',
    'src/**/*.d.ts',
    'src/**/index.ts',
    'src/**/types.ts',
  ],
}
```

默认排除测试文件和类型声明文件。

## 5. reports 配置

`coverage.reporter` 指定覆盖率报告的格式：

```typescript
coverage: {
  reporter: ['text', 'html', 'lcov', 'json'],
}
```

| reporter       | 说明                           |
| -------------- | ------------------------------ |
| `text`         | 终端输出摘要                   |
| `text-summary` | 终端输出简要信息               |
| `html`         | 生成 HTML 报告，可在浏览器查看 |
| `lcov`         | 生成 lcov 格式，用于 CI 集成   |
| `json`         | JSON 格式，程序化处理          |
| `json-summary` | JSON 摘要格式                  |
| `cobertura`    | Cobertura XML 格式             |
| `clover`       | Clover XML 格式                |

## 6. reportsDirectory

`coverage.reportsDirectory` 指定报告输出目录：

```typescript
coverage: {
  reportsDirectory: './coverage',
}
```

默认为 `./coverage`。

## 7. 允许未测试文件

`coverage.all` 控制是否为所有匹配文件生成覆盖率（包括没有测试的文件）：

```typescript
coverage: {
  all: true,
  include: ['src/**'],
}
```

设为 `true` 时，即使某些文件没有对应的测试，也会出现在覆盖率报告中（覆盖率为 0）。

## 8. 完整配置示例

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      enabled: true,
      include: ['src/**'],
      exclude: ['src/**/*.test.ts', 'src/**/*.d.ts'],
      reporter: ['text', 'html', 'lcov'],
      reportsDirectory: './coverage',
      all: true,
    },
  },
})
```

## 9. 命令行参数

```bash
# 启用覆盖率
vitest run --coverage

# 指定 provider
vitest run --coverage.provider=v8

# 指定 reporter
vitest run --coverage.reporter=html
```
