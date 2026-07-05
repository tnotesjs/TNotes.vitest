# [0080. 组件浏览器测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0080.%20%E7%BB%84%E4%BB%B6%E6%B5%8F%E8%A7%88%E5%99%A8%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 真实渲染](#3-真实渲染)
- [4. 交互验证](#4-交互验证)
- [5. 样式验证](#5-样式验证)
- [6. 截图测试](#6-截图测试)
- [7. 响应式测试](#7-响应式测试)
- [8. 动画测试](#8-动画测试)
- [9. 表单测试](#9-表单测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 真实渲染（布局计算）
- 交互验证
- 样式验证（getComputedStyle）
- 截图测试
- 响应式测试
- 动画测试
- 表单测试

## 2. 评价

浏览器端组件测试更接近真实使用场景，可以验证布局、样式、动画等 jsdom 无法测试的功能。性能开销较大，建议只对关键组件使用。

## 3. 真实渲染

Browser Mode 可以在真实浏览器中渲染组件：

```typescript
import { render } from '@testing-library/react'
import { test, expect } from 'vitest'
import Button from './Button'

test('real rendering', () => {
  render(<Button>Click</Button>)
  const button = screen.getByRole('button')

  // 真实的布局计算
  expect(button.offsetWidth).toBeGreaterThan(0)
  expect(button.offsetHeight).toBeGreaterThan(0)
})
```

## 4. 交互验证

```typescript
import userEvent from '@testing-library/user-event'

test('user interaction', async () => {
  const user = userEvent.setup()
  render(<Counter />)

  const button = screen.getByRole('button')
  await user.click(button)

  expect(screen.getByText('Count: 1')).toBeInTheDocument()
})
```

## 5. 样式验证

```typescript
test('styles', () => {
  render(<Button variant="primary" />)
  const button = screen.getByRole('button')

  const styles = window.getComputedStyle(button)
  expect(styles.backgroundColor).toBe('rgb(0, 123, 255)')
})
```

## 6. 截图测试

```typescript
test('screenshot', async () => {
  render(<Card title="Hello" />)

  const element = screen.getByTestId('card')
  // 截图需要额外配置
})
```

## 7. 响应式测试

```typescript
test('responsive', () => {
  // 模拟不同视口
  window.innerWidth = 375
  window.dispatchEvent(new Event('resize'))

  render(<ResponsiveLayout />)
  expect(screen.getByTestId('mobile-layout')).toBeInTheDocument()
})
```

## 8. 动画测试

```typescript
test('animation', async () => {
  render(<AnimatedComponent />)
  const element = screen.getByTestId('animated')

  // 等待动画完成
  await new Promise(resolve => setTimeout(resolve, 500))

  expect(element.classList.contains('animated')).toBe(true)
})
```

## 9. 表单测试

```typescript
test('form', async () => {
  const user = userEvent.setup()
  render(<ContactForm />)

  await user.type(screen.getByLabelText('Name'), 'Alice')
  await user.type(screen.getByLabelText('Email'), 'alice@example.com')
  await user.click(screen.getByRole('button', { name: 'Submit' }))

  expect(screen.getByText('Success')).toBeInTheDocument()
})
```

## 10. 注意事项

- Browser Mode 的组件测试更接近真实使用场景
- 性能开销较大，建议只对关键组件使用
- 可以与 jsdom 测试互补
