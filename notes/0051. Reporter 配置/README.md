# [0051. Reporter 配置](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0051.%20Reporter%20%E9%85%8D%E7%BD%AE)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 默认 Reporter](#3-默认-reporter)
- [4. verbose](#4-verbose)
- [5. json](#5-json)
- [6. junit](#6-junit)
- [7. github-actions](#7-github-actions)
- [8. 组合使用](#8-组合使用)
- [9. 命令行指定](#9-命令行指定)
- [10. 自定义 Reporter](#10-自定义-reporter)

<!-- endregion:toc -->

## 1. 本节内容

- 默认 Reporter（default）
- verbose 详细输出
- json 格式
- junit 格式
- github-actions 专用
- 组合使用多个 Reporter
- 命令行指定
- 自定义 Reporter

## 2. 评价

Reporter 控制测试结果的输出格式。default 适合本地开发，junit/json 适合 CI 集成，github-actions 在 PR 中显示注释。可以组合使用多个 Reporter。

## 3. 默认 Reporter

Vitest 默认使用 `default` reporter，在终端中显示带颜色的测试结果：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    reporters: ['default'],
  },
})
```

## 4. verbose

`verbose` reporter 显示每个测试用例的详细信息：

```typescript
test: {
  reporters: ['verbose'],
}
```

输出示例：

```
✓ src/math.test.ts > add > 两数相加
✓ src/math.test.ts > add > 负数相加
✓ src/math.test.ts > divide > 正常除法
```

## 5. json

`json` reporter 输出 JSON 格式的测试结果：

```typescript
test: {
  reporters: ['json'],
  outputFile: './test-results.json',
}
```

适合程序化处理测试结果。

## 6. junit

`junit` reporter 输出 JUnit XML 格式，兼容大多数 CI 平台：

```typescript
test: {
  reporters: ['junit'],
  outputFile: './junit.xml',
}
```

## 7. github-actions

`github-actions` reporter 在 GitHub Actions 中输出带注释的测试结果：

```typescript
test: {
  reporters: ['github-actions'],
}
```

失败的测试会在 PR 中显示为注释。

## 8. 组合使用

可以同时使用多个 reporter：

```typescript
test: {
  reporters: ['default', 'junit'],
  outputFile: {
    junit: './junit.xml',
    json: './results.json',
  },
}
```

## 9. 命令行指定

```bash
# 使用指定 reporter
vitest --reporter=verbose

# 使用多个 reporter
vitest --reporter=default --reporter=junit
```

## 10. 自定义 Reporter

```typescript
// my-reporter.ts
import type { Reporter } from 'vitest/reporters'

export default class MyReporter implements Reporter {
  onFinished(files, errors) {
    console.log(`Tests finished: ${files.length} files`)
  }
}
```

```typescript
test: {
  reporters: ['./my-reporter.ts'],
}
```
