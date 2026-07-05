# [0102. CI 参数](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0102.%20CI%20%E5%8F%82%E6%95%B0)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. --run](#3---run)
- [4. --reporter](#4---reporter)
- [5. --outputFile](#5---outputfile)
- [6. --passWithNoTests](#6---passwithnotests)
- [7. --bail](#7---bail)
- [8. --coverage](#8---coverage)
- [9. GitHub Actions 示例](#9-github-actions-示例)
- [10. GitLab CI 示例](#10-gitlab-ci-示例)

<!-- endregion:toc -->

## 1. 本节内容

- --run 单次运行
- --reporter 指定 reporter 格式
- --outputFile 输出文件
- --passWithNoTests 无测试不报错
- --bail 遇失败停止
- --coverage 覆盖率
- GitHub Actions 示例
- GitLab CI 示例

## 2. 评价

CI 环境必须使用 --run 避免进入 Watch 模式。配合 junit reporter 和覆盖率参数，满足 CI 门禁需求。

## 3. --run

单次运行，不进入 Watch 模式：

```bash
vitest run
```

CI 中必须使用 `vitest run`，否则会进入 Watch 模式挂起。

## 4. --reporter

指定 reporter 输出格式：

```bash
# JUnit 格式，适合 CI 平台
vitest run --reporter=junit

# GitHub Actions 专用
vitest run --reporter=github-actions

# 组合使用
vitest run --reporter=default --reporter=junit
```

## 5. --outputFile

输出文件：

```bash
vitest run --reporter=junit --outputFile=junit.xml
```

## 6. --passWithNoTests

没有测试文件时返回成功：

```bash
vitest run --passWithNoTests
```

适合 CI 中某些包可能没有测试的场景。

## 7. --bail

遇到失败时停止：

```bash
# 遇到第一个失败停止
vitest run --bail=1

# 遇到 3 个失败停止
vitest run --bail=3
```

## 8. --coverage

生成覆盖率：

```bash
vitest run --coverage
```

## 9. GitHub Actions 示例

```yaml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      - run: pnpm install
      - run: pnpm test:ci
```

```json
{
  "scripts": {
    "test:ci": "vitest run --coverage --reporter=default --reporter=junit --outputFile=junit.xml"
  }
}
```

## 10. GitLab CI 示例

```yaml
test:
  script:
    - pnpm install
    - pnpm vitest run --coverage
  coverage: '/Statements\s*:\s*(\d+\.?\d*)%/'
  artifacts:
    reports:
      junit: junit.xml
```
