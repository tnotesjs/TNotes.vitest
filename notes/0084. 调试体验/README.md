# [0084. 调试体验](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0084.%20%E8%B0%83%E8%AF%95%E4%BD%93%E9%AA%8C)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 定位失败](#3-定位失败)
- [4. 查看日志](#4-查看日志)
- [5. Watch 配合](#5-watch-配合)
- [6. 源码跳转](#6-源码跳转)
- [7. 快照对比](#7-快照对比)
- [8. 控制台输出](#8-控制台输出)
- [9. 断点调试](#9-断点调试)
- [10. 调试技巧](#10-调试技巧)

<!-- endregion:toc -->

## 1. 本节内容

- 定位失败
- 查看日志
- Watch 配合
- 源码跳转
- 快照对比
- 断点调试

## 2. 评价

UI 提供丰富的调试信息，配合 Watch 模式可以快速迭代修复问题。

## 3. 定位失败

UI 中点击失败的测试可以查看：

- 错误消息
- 期望值 vs 实际值的 diff
- 堆栈跟踪
- 源代码位置

## 4. 查看日志

测试中的 `console.log` 输出会显示在 UI 中：

```typescript
test('debug', () => {
  console.log('Debug info:', someVariable)
  expect(result).toBe(expected)
})
```

## 5. Watch 配合

Watch 模式下修改代码后自动重跑：

```bash
vitest --ui --watch
```

- 保存文件后自动重跑相关测试
- 错误信息实时更新
- 可以快速迭代修复

## 6. 源码跳转

UI 中可以点击源代码位置直接跳转到编辑器：

- 需要配置编辑器协议
- 支持 VS Code 等编辑器

## 7. 快照对比

快照测试失败时，UI 显示详细的对比：

- 预期快照
- 实际输出
- 差异高亮

## 8. 控制台输出

```typescript
test('logging', () => {
  console.log('Step 1')
  console.warn('Warning')
  console.error('Error')
  // 所有输出都会在 UI 中显示
})
```

## 9. 断点调试

可以在测试代码中设置断点：

1. 在编辑器中设置断点
2. 使用 `--inspect` 启动 Vitest
3. 在浏览器开发者工具中调试

## 10. 调试技巧

- 使用 `console.log` 快速定位问题
- 使用 UI 的 diff 功能对比期望值
- 使用 Watch 模式快速迭代
- 使用覆盖率查看未测试的代码
