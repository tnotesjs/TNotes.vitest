# [0055. happy-dom 环境](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0055.%20happy-dom%20%E7%8E%AF%E5%A2%83)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装 happy-dom](#3-安装-happy-dom)
- [4. 配置](#4-配置)
- [5. 使用方式](#5-使用方式)
- [6. 和 jsdom 的差异](#6-和-jsdom-的差异)
- [7. 选择建议](#7-选择建议)
- [8. 功能对比](#8-功能对比)
- [9. 文件级切换](#9-文件级切换)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 happy-dom
- 配置方式
- 与 jsdom 的差异（速度更快）
- 选择建议
- 功能对比
- 文件级切换

## 2. 评价

happy-dom 是 jsdom 的轻量替代，速度通常快 2-5 倍。大多数场景下两者可以互换，需要完整 DOM 实现时选择 jsdom，追求速度时选择 happy-dom。

## 3. 安装 happy-dom

```bash
pnpm add -D happy-dom
```

## 4. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'happy-dom',
  },
})
```

## 5. 使用方式

与 `jsdom` 类似，`happy-dom` 也提供浏览器环境：

```typescript
import { test, expect } from 'vitest'

test('DOM test', () => {
  document.body.innerHTML = '<div id="app">Hello</div>'
  expect(document.getElementById('app')?.textContent).toBe('Hello')
})
```

## 6. 和 jsdom 的差异

| 维度   | jsdom    | happy-dom |
| ------ | -------- | --------- |
| 速度   | 较慢     | 较快      |
| 完整性 | 更完整   | 基本完整  |
| 包大小 | 较大     | 较小      |
| 维护   | 社区维护 | 活跃维护  |

`happy-dom` 的主要优势是速度，通常比 `jsdom` 快 2-5 倍。

## 7. 选择建议

- 需要完整的 DOM 实现：选择 `jsdom`
- 追求测试速度：选择 `happy-dom`
- 大多数场景下两者可以互换

## 8. 功能对比

两者都支持：

- `document`、`window` 对象
- DOM 操作（创建、查询、修改元素）
- 事件处理
- `localStorage`、`sessionStorage`

`jsdom` 额外支持：

- 更完整的 Web API
- 更准确的 HTML 解析
- 某些边缘场景的兼容性

## 9. 文件级切换

```typescript
// @vitest-environment happy-dom
import { test, expect } from 'vitest'

test('happy-dom test', () => {
  document.body.innerHTML = '<p>Fast!</p>'
  expect(document.querySelector('p')?.textContent).toBe('Fast!')
})
```

## 10. 注意事项

- `happy-dom` 某些 API 可能与浏览器行为有差异
- 如果测试依赖特定的 DOM 行为，建议使用 `jsdom`
- 两者都不支持布局和渲染
