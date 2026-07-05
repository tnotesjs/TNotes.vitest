# [0104. Node Inspector](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0104.%20Node%20Inspector)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. --inspect](#3---inspect)
- [4. --inspect-brk](#4---inspect-brk)
- [5. 自定义端口](#5-自定义端口)
- [6. Chrome DevTools](#6-chrome-devtools)
- [7. 断点调试](#7-断点调试)
- [8. source map](#8-source-map)
- [9. 调试特定测试](#9-调试特定测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- --inspect 启动 Inspector
- --inspect-brk 等待连接
- 自定义端口
- Chrome DevTools 连接
- 断点调试
- source map 支持

## 2. 评价

Node Inspector 提供底层调试能力，适合需要深入调试的场景。--inspect-brk 确保调试器有时间连接。

## 3. --inspect

使用 `--inspect` 启动 Node Inspector：

```bash
vitest run --inspect
```

默认监听 `127.0.0.1:9229`。

## 4. --inspect-brk

启动时暂停，等待调试器连接：

```bash
vitest run --inspect-brk
```

## 5. 自定义端口

```bash
vitest run --inspect=9230
vitest run --inspect-brk=9230
```

## 6. Chrome DevTools

1. 运行 `vitest run --inspect-brk`
2. 打开 Chrome，访问 `chrome://inspect`
3. 点击 "Open dedicated DevTools for Node"
4. 设置断点，点击运行

## 7. 断点调试

在代码中设置断点：

```typescript
test('debug', () => {
  debugger // 断点
  const result = complexFunction()
  expect(result).toBe(expected)
})
```

## 8. source map

Vitest 自动生成 source map，可以在 TypeScript 源码上设置断点。

## 9. 调试特定测试

```bash
# 调试特定文件
vitest run --inspect src/utils.test.ts

# 调试特定测试
vitest run --inspect -t "test name"
```

## 10. 注意事项

- 调试时建议使用 `--inspect-brk` 等待连接
- 确保端口没有被占用
- 调试完成后关闭 Inspector
