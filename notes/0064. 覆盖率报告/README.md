# [0064. 覆盖率报告](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0064.%20%E8%A6%86%E7%9B%96%E7%8E%87%E6%8A%A5%E5%91%8A)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. text 报告](#3-text-报告)
- [4. html 报告](#4-html-报告)
- [5. lcov 报告](#5-lcov-报告)
- [6. json 报告](#6-json-报告)
- [7. 组合使用](#7-组合使用)
- [8. CI 集成](#8-ci-集成)
  - [8.1. GitHub Actions](#81-github-actions)
  - [8.2. GitLab CI](#82-gitlab-ci)
- [9. 查看 HTML 报告](#9-查看-html-报告)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- text 终端输出
- html 可视化报告
- lcov CI 集成格式
- json 格式
- 组合使用
- GitHub Actions 集成
- GitLab CI 集成

## 2. 评价

text 适合本地快速查看，html 适合详细分析，lcov 适合 CI 集成。可以同时输出多种格式，满足不同场景需求。

## 3. text 报告

在终端中输出覆盖率摘要：

```typescript
coverage: {
  reporter: ['text'],
}
```

输出示例：

```
 % Coverage report
 ---------------|---------|----------|---------|---------|
 File           | % Stmts | % Branch | % Funcs | % Lines |
 ---------------|---------|----------|---------|---------|
 src/math.ts    |   100   |   100    |   100   |   100   |
 src/utils.ts   |   85.7  |   50     |   100   |   85.7  |
 ---------------|---------|----------|---------|---------|
```

## 4. html 报告

生成 HTML 报告，可在浏览器中查看详细的覆盖率信息：

```typescript
coverage: {
  reporter: ['html'],
  reportsDirectory: './coverage',
}
```

HTML 报告会显示每个文件的逐行覆盖率，可以点击查看哪些行被覆盖、哪些没有。

## 5. lcov 报告

生成 lcov 格式，用于 CI 平台集成：

```typescript
coverage: {
  reporter: ['lcov'],
}
```

lcov 格式被大多数 CI 平台和代码托管平台支持（Codecov、Coveralls、SonarQube 等）。

## 6. json 报告

JSON 格式便于程序化处理：

```typescript
coverage: {
  reporter: ['json'],
}
```

## 7. 组合使用

可以同时生成多种格式的报告：

```typescript
coverage: {
  reporter: ['text', 'html', 'lcov', 'json'],
  reportsDirectory: './coverage',
}
```

## 8. CI 集成

### 8.1. GitHub Actions

```yaml
- name: Run tests with coverage
  run: vitest run --coverage

- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```

### 8.2. GitLab CI

```yaml
test:
  script:
    - vitest run --coverage
  coverage: '/Statements\s*:\s*(\d+\.?\d*)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        coverage.xml
```

## 9. 查看 HTML 报告

```bash
# 生成报告
vitest run --coverage

# 打开报告
open coverage/index.html
```

## 10. 注意事项

- HTML 报告适合本地调试
- lcov 报告适合 CI 集成
- text 报告适合快速查看
