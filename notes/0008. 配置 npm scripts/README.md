# [0008. 配置 npm scripts](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0008.%20%E9%85%8D%E7%BD%AE%20npm%20scripts)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vitest - 默认模式](#3-vitest---默认模式)
- [4. vitest run - 单次运行](#4-vitest-run---单次运行)
- [5. vitest --ui - UI 界面](#5-vitest---ui---ui-界面)
- [6. 覆盖率命令](#6-覆盖率命令)
- [7. 指定文件运行](#7-指定文件运行)
- [8. CI 环境配置](#8-ci-环境配置)
- [9. 常用组合示例](#9-常用组合示例)

<!-- endregion:toc -->

## 1. 本节内容

- vitest 默认进入 Watch 模式
- vitest run 单次执行
- vitest --ui 启动 Web UI
- 覆盖率命令
- 指定文件运行
- CI 环境配置

## 2. 评价

合理配置 npm scripts 可以简化日常开发和 CI 流程。本地开发使用 Watch 模式，CI 环境使用单次运行模式，按需配置覆盖率和 reporter。

## 3. vitest - 默认模式

```json
{
  "scripts": {
    "test": "vitest"
  }
}
```

运行 `pnpm test` 会启动 Vitest 的 Watch 模式，监听文件变化并自动重新运行受影响的测试。适合本地开发时使用。

## 4. vitest run - 单次运行

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run"
  }
}
```

`vitest run` 执行一次测试后退出，不会进入 Watch 模式。适合 CI 环境或手动触发的场景。

## 5. vitest --ui - UI 界面

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui"
  }
}
```

启动 Vitest 的 Web UI 界面，可以在浏览器中查看测试结果、失败详情、覆盖率等。需要安装 `@vitest/ui`：

```bash
pnpm add -D @vitest/ui
```

## 6. 覆盖率命令

```json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```

运行覆盖率报告需要安装覆盖率 provider：

```bash
pnpm add -D @vitest/coverage-v8
```

## 7. 指定文件运行

```json
{
  "scripts": {
    "test": "vitest",
    "test:unit": "vitest run src/utils",
    "test:component": "vitest run src/components"
  }
}
```

可以在命令后指定目录或文件路径，只运行匹配的测试。

## 8. CI 环境配置

```json
{
  "scripts": {
    "test": "vitest",
    "test:ci": "vitest run --reporter=junit --outputFile=test-results.xml"
  }
}
```

CI 中常用的参数：

| 参数                        | 说明                              |
| --------------------------- | --------------------------------- |
| `--run`                     | 等同于 `vitest run`，单次执行     |
| `--reporter=default`        | 默认 reporter，带颜色输出         |
| `--reporter=junit`          | 输出 JUnit XML 格式，适配 CI 平台 |
| `--reporter=github-actions` | GitHub Actions 专用 reporter      |
| `--reporter=json`           | JSON 格式输出                     |
| `--passWithNoTests`         | 没有测试文件时不报错              |

典型的 CI 配置：

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage",
    "test:ci": "vitest run --coverage --reporter=default --reporter=junit --outputFile=junit.xml"
  }
}
```

## 9. 常用组合示例

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage",
    "test:watch": "vitest watch"
  }
}
```
