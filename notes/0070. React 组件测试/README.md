# [0070. React 组件测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0070.%20React%20%E7%BB%84%E4%BB%B6%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装依赖](#3-安装依赖)
- [4. 配置](#4-配置)
- [5. 渲染组件](#5-渲染组件)
- [6. 事件处理](#6-事件处理)
- [7. 异步渲染](#7-异步渲染)
- [8. 查询元素](#8-查询元素)
- [9. 快照测试](#9-快照测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 @testing-library/react
- 配置 jsdom 环境
- 渲染组件
- 事件处理（fireEvent/userEvent）
- 异步渲染（waitFor）
- 查询元素
- 快照测试

## 2. 评价

React 组件测试推荐使用 Testing Library，遵循“测试用户可见行为”的原则。优先使用语义化查询（getByRole/getByLabelText），避免测试实现细节。

## 3. 安装依赖

```bash
pnpm add -D @testing-library/react @testing-library/jest-dom jsdom
```

## 4. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: ['./setup.ts'],
  },
})
```

```typescript
// setup.ts
import '@testing-library/jest-dom'
```

## 5. 渲染组件

```typescript
import { render, screen } from '@testing-library/react'
import { test, expect } from 'vitest'
import Button from './Button'

test('renders button', () => {
  render(<Button>Click me</Button>)
  expect(screen.getByText('Click me')).toBeInTheDocument()
})
```

## 6. 事件处理

```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { test, expect, vi } from 'vitest'
import Counter from './Counter'

test('increments counter', async () => {
  render(<Counter />)

  const button = screen.getByRole('button', { name: 'Increment' })
  await fireEvent.click(button)

  expect(screen.getByText('Count: 1')).toBeInTheDocument()
})
```

## 7. 异步渲染

```typescript
import { render, screen, waitFor } from '@testing-library/react'
import { test, expect } from 'vitest'
import UserProfile from './UserProfile'

test('loads user data', async () => {
  render(<UserProfile userId={1} />)

  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument()
  })
})
```

## 8. 查询元素

```typescript
// 按文本
screen.getByText('Hello')

// 按角色
screen.getByRole('button', { name: 'Submit' })

// 按 placeholder
screen.getByPlaceholderText('Enter name')

// 按 test id
screen.getByTestId('custom-element')

// 查询多个
screen.getAllByRole('listitem')
```

## 9. 快照测试

```typescript
import { render } from '@testing-library/react'
import { test, expect } from 'vitest'
import Button from './Button'

test('snapshot', () => {
  const { container } = render(<Button>Click</Button>)
  expect(container).toMatchSnapshot()
})
```

## 10. 注意事项

- 使用 `@testing-library/react` 的查询方法
- 优先使用 `getByRole` 等语义化查询
- 异步操作使用 `waitFor`
- 安装 `@testing-library/jest-dom` 获得额外的 matcher
