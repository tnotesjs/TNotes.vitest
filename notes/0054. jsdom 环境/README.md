# [0054. jsdom 环境](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0054.%20jsdom%20%E7%8E%AF%E5%A2%83)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装 jsdom](#3-安装-jsdom)
- [4. 配置](#4-配置)
- [5. DOM API 测试](#5-dom-api-测试)
- [6. 浏览器对象](#6-浏览器对象)
- [7. 事件处理](#7-事件处理)
- [8. localStorage / sessionStorage](#8-localstorage--sessionstorage)
- [9. 适用场景](#9-适用场景)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 jsdom
- 配置 environment: 'jsdom'
- DOM API 测试
- 浏览器对象（window/document/navigator）
- 事件处理
- localStorage/sessionStorage

## 2. 评价

jsdom 提供模拟的 DOM 环境，适合前端组件测试。它是 DOM 的模拟实现，某些浏览器 API 可能不完整，需要真实浏览器环境时应使用 Browser Mode。

## 3. 安装 jsdom

```bash
pnpm add -D jsdom
```

## 4. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'jsdom',
  },
})
```

## 5. DOM API 测试

```typescript
import { test, expect } from 'vitest'

test('DOM manipulation', () => {
  document.body.innerHTML = '<div id="app"><p>Hello</p></div>'

  const app = document.getElementById('app')
  expect(app).toBeDefined()
  expect(app?.querySelector('p')?.textContent).toBe('Hello')
})
```

## 6. 浏览器对象

`jsdom` 提供了 `window`、`document`、`navigator` 等浏览器全局对象：

```typescript
test('browser globals', () => {
  expect(window).toBeDefined()
  expect(document).toBeDefined()
  expect(navigator).toBeDefined()
  expect(location).toBeDefined()
})
```

## 7. 事件处理

```typescript
test('events', () => {
  const button = document.createElement('button')
  let clicked = false

  button.addEventListener('click', () => {
    clicked = true
  })

  button.click()
  expect(clicked).toBe(true)
})
```

## 8. localStorage / sessionStorage

```typescript
test('storage', () => {
  localStorage.setItem('key', 'value')
  expect(localStorage.getItem('key')).toBe('value')

  sessionStorage.setItem('session', 'data')
  expect(sessionStorage.getItem('session')).toBe('data')
})
```

## 9. 适用场景

- Vue/React/Svelte 组件测试
- DOM 操作测试
- 浏览器事件测试
- 表单处理测试
- localStorage/sessionStorage 测试

## 10. 注意事项

- `jsdom` 是 DOM 的模拟实现，某些浏览器 API 可能不完整
- 不支持布局和渲染（如 `offsetWidth`、`getBoundingClientRect`）
- 不支持图片和 CSS 加载
- 需要真实浏览器环境的测试应使用 Browser Mode
