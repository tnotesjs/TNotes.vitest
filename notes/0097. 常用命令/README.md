# [0097. 常用命令](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0097.%20%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vitest](#3-vitest)
- [4. vitest run](#4-vitest-run)
- [5. vitest watch](#5-vitest-watch)
- [6. vitest --ui](#6-vitest---ui)
- [7. vitest --coverage](#7-vitest---coverage)
- [8. 指定文件](#8-指定文件)
- [9. 指定项目（Workspace）](#9-指定项目workspace)
- [10. 常用组合](#10-常用组合)

<!-- endregion:toc -->

## 1. 本节内容

- vitest 默认 Watch 模式
- vitest run 单次运行
- vitest watch
- vitest --ui
- vitest --coverage
- 指定文件
- 指定项目

## 2. 评价

常用命令覆盖了日常开发和 CI 的需求。本地开发使用 Watch 模式，CI 使用 run 模式。

## 3. vitest

默认命令，启动 Watch 模式：

```bash
vitest
```

- 监听文件变化
- 自动重跑受影响的测试
- 适合本地开发

## 4. vitest run

单次运行，运行后退出：

```bash
vitest run
```

- 不进入 Watch 模式
- 适合 CI 环境
- 适合手动触发

## 5. vitest watch

显式启动 Watch 模式：

```bash
vitest watch
```

等同于 `vitest`，但语义更明确。

## 6. vitest --ui

启动 Web UI：

```bash
vitest --ui
```

需要安装 `@vitest/ui`。

## 7. vitest --coverage

运行并生成覆盖率报告：

```bash
vitest run --coverage
```

需要安装覆盖率 provider。

## 8. 指定文件

```bash
# 运行单个文件
vitest run src/utils.test.ts

# 运行目录
vitest run src/components

# 使用 glob
vitest run "src/**/*.test.ts"
```

## 9. 指定项目（Workspace）

```bash
# 运行指定项目
vitest run --project core

# 运行多个项目
vitest run --project core --project ui
```

## 10. 常用组合

```bash
# 本地开发
vitest

# CI 运行
vitest run

# 带覆盖率
vitest run --coverage

# UI 模式
vitest --ui

# 更新快照
vitest run -u
```
