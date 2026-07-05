# [0101. 配置覆盖](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0101.%20%E9%85%8D%E7%BD%AE%E8%A6%86%E7%9B%96)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. --config](#3---config)
- [4. --environment](#4---environment)
- [5. --globals](#5---globals)
- [6. --reporter](#6---reporter)
- [7. --outputFile](#7---outputfile)
- [8. --passWithNoTests](#8---passwithnotests)
- [9. --bail](#9---bail)
- [10. 配置优先级](#10-配置优先级)

<!-- endregion:toc -->

## 1. 本节内容

- --config 指定配置文件
- --environment 覆盖测试环境
- --globals 启用全局 API
- --reporter 指定 reporter
- --outputFile 输出文件
- --passWithNoTests 无测试不报错
- --bail 遇失败停止

## 2. 评价

命令行参数可以覆盖配置文件的设置，优先级最高。适合在 CI 或特定场景下临时调整配置。

## 3. --config

使用 `--config` 指定配置文件：

```bash
vitest run --config ./my-vitest.config.ts
```

可以使用不同的配置文件运行测试。

## 4. --environment

命令行覆盖测试环境：

```bash
vitest run --environment jsdom
vitest run --environment happy-dom
```

## 5. --globals

启用全局测试 API：

```bash
vitest run --globals
```

## 6. --reporter

指定 reporter：

```bash
vitest run --reporter=verbose
vitest run --reporter=json
vitest run --reporter=junit
```

## 7. --outputFile

指定输出文件：

```bash
vitest run --reporter=json --outputFile=results.json
vitest run --reporter=junit --outputFile=junit.xml
```

## 8. --passWithNoTests

没有测试文件时不报错：

```bash
vitest run --passWithNoTests
```

## 9. --bail

遇到第一个失败时停止：

```bash
vitest run --bail=1
```

## 10. 配置优先级

命令行参数 > 配置文件 > 默认值

```bash
# 命令行参数覆盖配置文件中的 environment
vitest run --environment jsdom
```
