# [0103. IDE 调试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0103.%20IDE%20%E8%B0%83%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. VS Code 调试配置](#3-vs-code-调试配置)
- [4. 使用方式](#4-使用方式)
- [5. 调试当前文件](#5-调试当前文件)
- [6. 调试所有测试](#6-调试所有测试)
- [7. 调试特定测试](#7-调试特定测试)
- [8. VS Code 扩展](#8-vs-code-扩展)
- [9. 断点调试](#9-断点调试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- VS Code launch.json 配置
- 使用方式（F5 启动）
- 调试当前文件
- 调试所有测试
- 调试特定测试
- VS Code 扩展
- 断点调试

## 2. 评价

VS Code 提供了完整的调试支持，通过 launch.json 配置可以一键启动调试。配合 Vitest 扩展获得更好的体验。

## 3. VS Code 调试配置

在 `.vscode/launch.json` 中添加调试配置：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Current Test",
      "autoAttachChildProcesses": true,
      "skipFiles": ["<node_internals>/**", "**/node_modules/**"],
      "program": "${workspaceRoot}/node_modules/vitest/vitest.mjs",
      "args": ["run", "${relativeFile}"],
      "console": "integratedTerminal"
    }
  ]
}
```

## 4. 使用方式

1. 在测试文件中设置断点
2. 打开测试文件
3. 按 F5 或点击调试按钮
4. 选择 "Debug Current Test"

## 5. 调试当前文件

```json
{
  "args": ["run", "${relativeFile}"]
}
```

## 6. 调试所有测试

```json
{
  "args": ["run"]
}
```

## 7. 调试特定测试

```json
{
  "args": ["run", "-t", "test name"]
}
```

## 8. VS Code 扩展

安装 Vitest 扩展获得更好的体验：

- Vitest Explorer：测试树视图
- 测试状态显示
- 一键运行/调试

## 9. 断点调试

```typescript
test('debug test', () => {
  const result = complexFunction() // 在这里设置断点
  expect(result).toBe(expected)
})
```

## 10. 注意事项

- 确保 `skipFiles` 配置正确
- 使用 `console` 为 `integratedTerminal` 查看输出
- 断点可能需要 source map 支持
