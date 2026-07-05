# [0083. 覆盖率查看](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0083.%20%E8%A6%86%E7%9B%96%E7%8E%87%E6%9F%A5%E7%9C%8B)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 启用覆盖率](#3-启用覆盖率)
- [4. HTML 报告](#4-html-报告)
- [5. UI 中查看](#5-ui-中查看)
- [6. 逐行查看](#6-逐行查看)
- [7. 分支覆盖](#7-分支覆盖)
- [8. 过滤和排序](#8-过滤和排序)
- [9. CI 集成](#9-ci-集成)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 启用覆盖率
- HTML 报告
- UI 中查看
- 逐行查看
- 分支覆盖
- 过滤排序
- CI 集成

## 2. 评价

覆盖率可视化帮助识别未覆盖的代码，HTML 报告支持逐行查看。

## 3. 启用覆盖率

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
    },
  },
})
```

## 4. HTML 报告

运行测试后生成 HTML 覆盖率报告：

```bash
vitest run --coverage
```

报告输出到 `coverage/` 目录，打开 `coverage/index.html` 查看。

## 5. UI 中查看

Vitest UI 可以直接查看覆盖率：

```bash
vitest --ui --coverage
```

UI 中会显示：

- 整体覆盖率概览
- 每个文件的覆盖率
- 未覆盖的代码行

## 6. 逐行查看

HTML 报告支持逐行查看覆盖率：

- 绿色：已覆盖
- 红色：未覆盖
- 黄色：部分覆盖

## 7. 分支覆盖

报告显示每个分支的覆盖情况：

- if/else 分支
- 三元表达式
- 逻辑运算符

## 8. 过滤和排序

覆盖率报告支持：

- 按文件名过滤
- 按覆盖率排序
- 按目录分组

## 9. CI 集成

```yaml
- name: Generate coverage
  run: vitest run --coverage

- name: Upload coverage
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```

## 10. 注意事项

- 覆盖率报告需要安装 provider（`@vitest/coverage-v8` 或 `@vitest/coverage-istanbul`）
- HTML 报告适合本地查看
- lcov 报告适合 CI 集成
