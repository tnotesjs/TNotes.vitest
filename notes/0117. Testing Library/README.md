# [0117. Testing Library](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0117.%20Testing%20Library)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装](#3-安装)
- [4. DOM 查询](#4-dom-查询)
- [5. 用户事件](#5-用户事件)
- [6. 可访问性查询](#6-可访问性查询)
- [7. 断言](#7-断言)
- [8. 异步测试](#8-异步测试)
- [9. 渲染组件](#9-渲染组件)
- [10. 设计原则](#10-设计原则)

<!-- endregion:toc -->

## 1. 本节内容

- 安装（@testing-library/react 等）
- DOM 查询（getByText/getByRole）
- 用户事件（userEvent）
- 可访问性查询
- 断言（jest-dom）
- 异步测试

## 2. 评价

- Testing Library 遵循"测试用户可见行为"的原则，鼓励使用语义化查询。是前端组件测试的推荐工具。

## 3. 安装

```bash
# React
pnpm add -D @testing-library/react @testing-library/jest-dom

# Vue
pnpm add -D @testing-library/vue @testing-library/jest-dom

# Svelte
pnpm add -D @testing-library/svelte @testing-library/jest-dom
```

## 4. DOM 查询

```typescript
import { screen } from '@testing-library/react'

// 按文本
screen.getByText('Hello')

// 按角色
screen.getByRole('button', { name: 'Submit' })

// 按 placeholder
screen.getByPlaceholderText('Enter name')

// 按 label
screen.getByLabelText('Username')

// 按 test id
screen.getByTestId('custom-element')
```

## 5. 用户事件

```typescript
import userEvent from '@testing-library/user-event'

const user = userEvent.setup()

await user.click(button)
await user.type(input, 'hello')
await user.keyboard('{Enter}')
await user.hover(element)
```

## 6. 可访问性查询

Testing Library 鼓励使用语义化查询：

```typescript
// 优先使用
screen.getByRole('button', { name: 'Submit' })

// 而不是
screen.getByTestId('submit-button')
```

## 7. 断言

```typescript
import '@testing-library/jest-dom'

expect(element).toBeInTheDocument()
expect(element).toBeVisible()
expect(element).toBeDisabled()
expect(element).toHaveTextContent('Hello')
expect(element).toHaveAttribute('href', '/home')
```

## 8. 异步测试

```typescript
import { waitFor } from '@testing-library/react'

await waitFor(() => {
  expect(screen.getByText('Loaded')).toBeInTheDocument()
})
```

## 9. 渲染组件

```typescript
import { render } from '@testing-library/react'

render(<Button>Click me</Button>)
```

## 10. 设计原则

Testing Library 的核心原则：

- 测试用户可见的行为
- 不测试实现细节
- 使用语义化查询
- 优先使用可访问性查询
