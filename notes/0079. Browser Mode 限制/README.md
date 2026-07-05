# [0079. Browser Mode 限制](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0079.%20Browser%20Mode%20%E9%99%90%E5%88%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Mock 差异](#3-mock-差异)
- [4. 模块加载差异](#4-模块加载差异)
- [5. 性能成本](#5-性能成本)
- [6. CI 环境](#6-ci-环境)
- [7. 跨平台差异](#7-跨平台差异)
- [8. 调试难度](#8-调试难度)
- [9. 适用范围](#9-适用范围)
- [10. 选择建议](#10-选择建议)

<!-- endregion:toc -->

## 1. 本节内容

- Mock 差异（通过 iframe 隔离）
- 模块加载差异（不支持 Node.js API）
- 性能成本（启动慢）
- CI 环境配置
- 跨平台差异
- 调试难度

## 2. 评价

Browser Mode 有性能和兼容性限制。大部分测试应使用 jsdom/happy-dom，关键路径使用 Browser Mode 验证。

## 3. Mock 差异

Browser Mode 中的 Mock 行为与 Node 环境有所不同：

```typescript
// vi.mock 在 Browser Mode 中通过 iframe 隔离实现
vi.mock('./module', () => ({
  fn: vi.fn(),
}))
```

某些 Mock 功能可能受限，如：

- 模块级别的 Mock 机制不同
- 全局变量的 Mock 可能需要不同方式

## 4. 模块加载差异

Browser Mode 使用浏览器的模块加载机制：

- 不支持 Node.js 特有的模块解析
- 某些 Node.js API 不可用（fs、path 等）
- 需要通过 polyfill 或 mock 处理

## 5. 性能成本

Browser Mode 比 jsdom 慢：

- 需要启动真实浏览器
- 每个测试在独立 iframe 中运行
- 浏览器通信有开销

## 6. CI 环境

Browser Mode 在 CI 中需要额外配置：

```yaml
- name: Install browsers
  run: pnpm exec playwright install --with-deps
```

CI 环境可能没有 GPU，某些 API 可能不可用。

## 7. 跨平台差异

不同操作系统上的浏览器行为可能有细微差异：

- 字体渲染不同
- 某些 API 行为不同
- 性能特征不同

## 8. 调试难度

Browser Mode 的调试相对复杂：

- 需要在浏览器开发者工具中调试
- 错误堆栈可能跨 iframe
- 日志输出位置不同

## 9. 适用范围

Browser Mode 不适合：

- 纯 Node.js 代码测试
- 需要文件系统操作的测试
- 需要大量 Mock 的测试
- 追求极致速度的测试

## 10. 选择建议

| 场景         | 建议                                |
| ------------ | ----------------------------------- |
| 大部分测试   | 使用 jsdom/happy-dom                |
| 需要真实 DOM | 使用 Browser Mode                   |
| CI 测试      | 使用 jsdom，关键路径用 Browser Mode |
| 组件库发布前 | 使用 Browser Mode 验证              |
