# [0073. DOM 交互测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0073.%20DOM%20%E4%BA%A4%E4%BA%92%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 查询元素](#3-查询元素)
- [4. 触发事件](#4-触发事件)
- [5. 用户事件](#5-用户事件)
- [6. 断言 UI 状态](#6-断言-ui-状态)
- [7. 异步断言](#7-异步断言)
- [8. 表单测试](#8-表单测试)
- [9. 列表测试](#9-列表测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 查询元素（getByText/getByRole 等）
- 触发事件（fireEvent）
- 用户事件（userEvent）
- 断言 UI 状态
- 异步断言
- 表单测试
- 列表测试

## 2. 评价

DOM 交互测试是前端组件测试的核心。推荐使用 userEvent 模拟真实用户操作，比 fireEvent 更贴近实际行为。优先使用语义化查询。

## 3. 查询元素

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

## 4. 触发事件

```typescript
import { fireEvent } from '@testing-library/react'

// 点击
await fireEvent.click(element)

// 输入
await fireEvent.change(input, { target: { value: 'hello' } })

// 键盘
await fireEvent.keyDown(element, { key: 'Enter' })

// 鼠标
await fireEvent.mouseEnter(element)
```

## 5. 用户事件

```typescript
import userEvent from '@testing-library/user-event'

const user = userEvent.setup()

// 模拟真实用户操作
await user.click(button)
await user.type(input, 'hello')
await user.keyboard('{Enter}')
await user.hover(element)
```

## 6. 断言 UI 状态

```typescript
import { screen } from '@testing-library/react'
import '@testing-library/jest-dom'

// 元素存在
expect(screen.getByText('Hello')).toBeInTheDocument()

// 元素不存在
expect(screen.queryByText('Hidden')).not.toBeInTheDocument()

// 元素可见
expect(screen.getByText('Visible')).toBeVisible()

// 元素禁用
expect(button).toBeDisabled()
```

## 7. 异步断言

```typescript
import { waitFor } from '@testing-library/react'

await waitFor(() => {
  expect(screen.getByText('Loaded')).toBeInTheDocument()
})
```

## 8. 表单测试

```typescript
test('form submission', async () => {
  const user = userEvent.setup()
  render(<LoginForm />)

  await user.type(screen.getByLabelText('Username'), 'admin')
  await user.type(screen.getByLabelText('Password'), '123456')
  await user.click(screen.getByRole('button', { name: 'Login' }))

  await waitFor(() => {
    expect(screen.getByText('Welcome')).toBeInTheDocument()
  })
})
```

## 9. 列表测试

```typescript
test('list rendering', () => {
  const items = ['Apple', 'Banana', 'Cherry']
  render(<List items={items} />)

  const listItems = screen.getAllByRole('listitem')
  expect(listItems).toHaveLength(3)
  expect(listItems[0]).toHaveTextContent('Apple')
})
```

## 10. 注意事项

- 优先使用语义化查询（getByRole、getByLabelText）
- 使用 userEvent 而非 fireEvent
- 异步操作使用 waitFor
