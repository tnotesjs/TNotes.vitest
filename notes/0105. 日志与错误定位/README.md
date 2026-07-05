# [0105. 日志与错误定位](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0105.%20%E6%97%A5%E5%BF%97%E4%B8%8E%E9%94%99%E8%AF%AF%E5%AE%9A%E4%BD%8D)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. console 输出](#3-console-输出)
- [4. 错误堆栈](#4-错误堆栈)
- [5. source map](#5-source-map)
- [6. verbose 模式](#6-verbose-模式)
- [7. 错误消息](#7-错误消息)
- [8. 调试技巧](#8-调试技巧)
- [9. 截图（Browser Mode）](#9-截图browser-mode)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- console 输出显示在报告
- 错误堆栈定位
- source map 支持
- verbose 模式
- 错误消息
- 调试技巧
- 截图（Browser Mode）

## 2. 评价

错误定位依赖堆栈信息和 source map。console 输出会显示在测试报告中，verbose 模式提供更详细的信息。

## 3. console 输出

测试中的 `console.log` 输出会显示在测试报告中：

```typescript
test('logging', () => {
  console.log('Debug:', someVariable)
  console.warn('Warning:', someValue)
  expect(result).toBe(expected)
})
```

## 4. 错误堆栈

测试失败时会显示完整的错误堆栈：

```
AssertionError: expected 1 to be 2

  at src/math.test.ts:15:20
  at VitestRunner.runTest (node_modules/vitest/dist/runner.js)
```

## 5. source map

Vitest 自动生成 source map，错误堆栈指向源码位置。

## 6. verbose 模式

使用 verbose reporter 查看详细信息：

```bash
vitest run --reporter=verbose
```

## 7. 错误消息

编写清晰的错误消息帮助定位问题：

```typescript
expect(result).toBe(expected)
// 如果失败，显示期望值和实际值
```

## 8. 调试技巧

```typescript
test('debug', () => {
  console.log('Input:', input)
  const result = process(input)
  console.log('Output:', result)
  expect(result).toBe(expected)
})
```

## 9. 截图（Browser Mode）

Browser Mode 中可以截图辅助调试：

```typescript
test('screenshot', async ({ page }) => {
  await page.goto('http://localhost:3000')
  await page.screenshot({ path: 'debug.png' })
})
```

## 10. 注意事项

- 过多的 console.log 会影响测试输出可读性
- 调试完成后应移除调试代码
- 使用 `--reporter=verbose` 获取更多信息
