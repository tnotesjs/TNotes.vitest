# [0078. 浏览器端测试 API](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0078.%20%E6%B5%8F%E8%A7%88%E5%99%A8%E7%AB%AF%E6%B5%8B%E8%AF%95%20API)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. DOM 测试](#3-dom-测试)
- [4. 事件测试](#4-事件测试)
- [5. 用户交互](#5-用户交互)
- [6. 样式计算](#6-样式计算)
- [7. 网络请求](#7-网络请求)
- [8. Web API](#8-web-api)
- [9. Canvas 测试](#9-canvas-测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 真实 DOM API
- 事件测试
- 用户交互
- 样式计算（offsetWidth 等）
- 网络请求
- Web API（localStorage/IntersectionObserver）
- Canvas 测试

## 2. 评价

Browser Mode 中的 DOM API 是真实的浏览器 API，可以测试布局计算、Canvas 绘制等 jsdom 不支持的功能。

## 3. DOM 测试

在 Browser Mode 中，DOM API 是真实的浏览器 API：

```typescript
import { test, expect } from 'vitest'

test('real DOM', () => {
  const div = document.createElement('div')
  div.innerHTML = '<p>Hello</p>'
  document.body.appendChild(div)

  expect(document.querySelector('p')?.textContent).toBe('Hello')

  document.body.removeChild(div)
})
```

## 4. 事件测试

```typescript
test('events', async () => {
  const button = document.createElement('button')
  button.textContent = 'Click me'
  document.body.appendChild(button)

  let clicked = false
  button.addEventListener('click', () => {
    clicked = true
  })

  button.click()
  expect(clicked).toBe(true)

  document.body.removeChild(button)
})
```

## 5. 用户交互

```typescript
test('user interaction', async () => {
  const input = document.createElement('input')
  document.body.appendChild(input)

  input.focus()
  expect(document.activeElement).toBe(input)

  input.value = 'hello'
  expect(input.value).toBe('hello')

  document.body.removeChild(input)
})
```

## 6. 样式计算

```typescript
test('styles', () => {
  const div = document.createElement('div')
  div.style.width = '100px'
  div.style.height = '100px'
  document.body.appendChild(div)

  expect(div.offsetWidth).toBe(100)
  expect(div.offsetHeight).toBe(100)

  document.body.removeChild(div)
})
```

## 7. 网络请求

```typescript
test('fetch', async () => {
  const response = await fetch('/api/data')
  expect(response.ok).toBe(true)
  const data = await response.json()
  expect(data).toBeDefined()
})
```

## 8. Web API

Browser Mode 支持所有浏览器 API：

```typescript
test('Web APIs', () => {
  expect(window.localStorage).toBeDefined()
  expect(window.sessionStorage).toBeDefined()
  expect(window.matchMedia).toBeDefined()
  expect(window.IntersectionObserver).toBeDefined()
})
```

## 9. Canvas 测试

```typescript
test('canvas', () => {
  const canvas = document.createElement('canvas')
  const ctx = canvas.getContext('2d')

  expect(ctx).toBeDefined()
  expect(ctx?.fillRect).toBeInstanceOf(Function)
})
```

## 10. 注意事项

- Browser Mode 中的 API 是真实的，行为与生产环境一致
- 某些 API（如 alert）可能被拦截
- 测试隔离通过 iframe 实现
