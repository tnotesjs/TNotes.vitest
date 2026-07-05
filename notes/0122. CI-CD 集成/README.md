# [0122. CI-CD 集成](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0122.%20CI-CD%20%E9%9B%86%E6%88%90)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. GitHub Actions](#3-github-actions)
- [4. GitLab CI](#4-gitlab-ci)
- [5. 缓存配置](#5-缓存配置)
- [6. 报告产物](#6-报告产物)
- [7. 覆盖率集成](#7-覆盖率集成)
- [8. 多环境测试](#8-多环境测试)
- [9. npm scripts](#9-npm-scripts)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- GitHub Actions 配置
- GitLab CI 配置
- 缓存配置（node_modules）
- 报告产物上传
- 覆盖率集成（Codecov）
- 多环境测试
- npm scripts

## 2. 评价

- CI/CD 集成是自动化测试的关键环节
- 配置缓存可以显著提高 CI 速度，覆盖率集成帮助监控代码质量

## 3. GitHub Actions

```yaml
# .github/workflows/test.yml
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

## 4. GitLab CI

```yaml
# .gitlab-ci.yml
test:
  image: node:20
  cache:
    paths:
      - node_modules/
  script:
    - pnpm install
    - pnpm test:ci
  artifacts:
    reports:
      junit: junit.xml
```

## 5. 缓存配置

```yaml
# GitHub Actions 缓存
- uses: actions/cache@v3
  with:
    path: node_modules
    key: ${{ runner.os }}-pnpm-${{ hashFiles('pnpm-lock.yaml') }}
```

## 6. 报告产物

```yaml
# 上传测试报告
- name: Upload test results
  uses: actions/upload-artifact@v3
  if: always()
  with:
    name: test-results
    path: |
      junit.xml
      coverage/
```

## 7. 覆盖率集成

```yaml
# Codecov 集成
- name: Run tests with coverage
  run: pnpm vitest run --coverage

- name: Upload coverage
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```

## 8. 多环境测试

```yaml
strategy:
  matrix:
    node-version: [18, 20, 22]
steps:
  - uses: actions/setup-node@v4
    with:
      node-version: ${{ matrix.node-version }}
```

## 9. npm scripts

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:ci": "vitest run --coverage --reporter=default --reporter=junit --outputFile=junit.xml"
  }
}
```

## 10. 注意事项

- CI 中使用 `vitest run` 而非 `vitest`
- 配置缓存提高速度
- 上传测试报告和覆盖率
