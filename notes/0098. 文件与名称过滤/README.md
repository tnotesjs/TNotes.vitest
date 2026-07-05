# [0098. 文件与名称过滤](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0098.%20%E6%96%87%E4%BB%B6%E4%B8%8E%E5%90%8D%E7%A7%B0%E8%BF%87%E6%BB%A4)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 指定文件路径](#3-指定文件路径)
- [4. -t 名称过滤](#4--t-名称过滤)
- [5. --testNamePattern](#5---testnamepattern)
- [6. 文件名模式](#6-文件名模式)
- [7. 排除文件](#7-排除文件)
- [8. 正则表达式](#8-正则表达式)
- [9. 组合过滤](#9-组合过滤)
- [10. Watch 模式过滤](#10-watch-模式过滤)

<!-- endregion:toc -->

## 1. 本节内容

- 指定文件路径
- -t 名称过滤
- --testNamePattern
- 文件名模式
- 排除文件
- 正则表达式
- 组合过滤
- Watch 模式过滤

## 2. 评价

文件和名称过滤提供了灵活的测试控制。-t 参数按名称筛选，路径参数按文件筛选，可以组合使用。

## 3. 指定文件路径

```bash
# 运行单个文件
vitest run src/utils.test.ts

# 运行目录下所有测试
vitest run src/components

# 使用 glob 模式
vitest run "src/**/*.test.ts"
```

## 4. -t 名称过滤

`-t` 按测试名称过滤：

```bash
# 名称包含 "add" 的测试
vitest run -t "add"

# 正则表达式
vitest run -t "should.*user"

# 组合使用
vitest run src/utils.test.ts -t "format"
```

## 5. --testNamePattern

`--testNamePattern` 是 `-t` 的完整形式：

```bash
vitest run --testNamePattern="add"
```

## 6. 文件名模式

```bash
# 只运行 .test.ts 文件
vitest run "**/*.test.ts"

# 只运行 .spec.ts 文件
vitest run "**/*.spec.ts"

# 运行特定目录
vitest run "src/utils/**"
```

## 7. 排除文件

```bash
# 排除特定文件
vitest run --exclude="**/integration/**"
```

## 8. 正则表达式

`-t` 支持正则表达式：

```bash
# 匹配以 "test" 开头的测试
vitest run -t "^test"

# 匹配包含数字的测试
vitest run -t "\d+"
```

## 9. 组合过滤

```bash
# 文件 + 名称
vitest run src/math.test.ts -t "add"

# 目录 + 名称
vitest run src/components -t "Button"
```

## 10. Watch 模式过滤

在 Watch 模式中：

- 按 `t` 输入名称过滤
- 按 `p` 输入文件名过滤
- 按 `f` 只运行失败的测试
- 按 `a` 运行所有测试
