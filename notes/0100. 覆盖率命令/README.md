# [0100. 覆盖率命令](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0100.%20%E8%A6%86%E7%9B%96%E7%8E%87%E5%91%BD%E4%BB%A4)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. --coverage](#3---coverage)
- [4. 指定 provider](#4-指定-provider)
- [5. 指定 reporter](#5-指定-reporter)
- [6. 指定输出目录](#6-指定输出目录)
- [7. include / exclude](#7-include--exclude)
- [8. 阈值](#8-阈值)
- [9. 完整示例](#9-完整示例)
- [10. CI 配置](#10-ci-配置)

<!-- endregion:toc -->

## 1. 本节内容

- --coverage 启用覆盖率
- 指定 provider
- 指定 reporter
- 指定输出目录
- include/exclude
- 阈值
- CI 配置

## 2. 评价

覆盖率命令支持丰富的参数，可以在命令行中覆盖配置文件的设置。适合 CI 中灵活调整覆盖率策略。

## 3. --coverage

启用覆盖率收集：

```bash
vitest run --coverage
```

需要安装覆盖率 provider：

```bash
pnpm add -D @vitest/coverage-v8
```

## 4. 指定 provider

```bash
vitest run --coverage.provider=v8
vitest run --coverage.provider=istanbul
```

## 5. 指定 reporter

```bash
vitest run --coverage.reporter=text
vitest run --coverage.reporter=html
vitest run --coverage.reporter=lcov

# 多个 reporter
vitest run --coverage.reporter=text --coverage.reporter=html
```

## 6. 指定输出目录

```bash
vitest run --coverage.reportsDirectory=./coverage
```

## 7. include / exclude

```bash
vitest run --coverage.include="src/**"
vitest run --coverage.exclude="src/**/*.test.ts"
```

## 8. 阈值

```bash
vitest run --coverage.thresholds.lines=80
vitest run --coverage.thresholds.functions=80
```

## 9. 完整示例

```bash
vitest run \
  --coverage \
  --coverage.provider=v8 \
  --coverage.reporter=text \
  --coverage.reporter=html \
  --coverage.include="src/**" \
  --coverage.thresholds.lines=80
```

## 10. CI 配置

```yaml
# GitHub Actions
- name: Run tests with coverage
  run: vitest run --coverage

- name: Upload coverage
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```
