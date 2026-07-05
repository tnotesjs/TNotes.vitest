# [0135. Vue 与 React 组件测试实战](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0135.%20Vue%20%E4%B8%8E%20React%20%E7%BB%84%E4%BB%B6%E6%B5%8B%E8%AF%95%E5%AE%9E%E6%88%98)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Vue 表单测试](#3-vue-表单测试)
- [4. React 表单测试](#4-react-表单测试)
- [5. 列表渲染](#5-列表渲染)
- [6. 异步加载](#6-异步加载)
- [7. 用户事件](#7-用户事件)
- [8. 条件渲染](#8-条件渲染)
- [9. 快照测试](#9-快照测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Vue 表单测试（@vue/test-utils）
- React 表单测试（@testing-library/react）
- 列表渲染
- 异步加载
- 用户事件（userEvent）
- 条件渲染
- 快照测试

## 2. 评价

组件测试应关注用户可见行为，使用语义化查询元素。userEvent 比 fireEvent 更贴近真实用户操作。

## 3. Vue 表单测试

```typescript
import { mount } from '@vue/test-utils'
import { test, expect } from 'vitest'
import LoginForm from './LoginForm.vue'

test('submits form', async () => {
  const wrapper = mount(LoginForm)

  await wrapper.find('input[name="username"]').setValue('admin')
  await wrapper.find('input[name="password"]').setValue('123456')
  await wrapper.find('form').trigger('submit')

  expect(wrapper.emitted('submit')).toBeTruthy()
  expect(wrapper.emitted('submit')![0]).toEqual([
    { username: 'admin', password: '123456' },
  ])
})
```

## 4. React 表单测试

```typescript
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import LoginForm from './LoginForm'

test('submits form', async () => {
  const user = userEvent.setup()
  const onSubmit = vi.fn()

  render(<LoginForm onSubmit={onSubmit} />)

  await user.type(screen.getByLabelText('Username'), 'admin')
  await user.type(screen.getByLabelText('Password'), '123456')
  await user.click(screen.getByRole('button', { name: 'Login' }))

  expect(onSubmit).toHaveBeenCalledWith({
    username: 'admin',
    password: '123456',
  })
})
```

## 5. 列表渲染

```typescript
test('renders list', () => {
  const items = ['Apple', 'Banana', 'Cherry']
  render(<List items={items} />)

  const listItems = screen.getAllByRole('listitem')
  expect(listItems).toHaveLength(3)
  expect(listItems[0]).toHaveTextContent('Apple')
})
```

## 6. 异步加载

```typescript
test('loads data', async () => {
  render(<UserProfile userId={1} />)

  expect(screen.getByText('Loading...')).toBeInTheDocument()

  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument()
  })
})
```

## 7. 用户事件

```typescript
test('handles click', async () => {
  const user = userEvent.setup()
  const onClick = vi.fn()

  render(<Button onClick={onClick}>Click me</Button>)

  await user.click(screen.getByRole('button'))
  expect(onClick).toHaveBeenCalled()
})
```

## 8. 条件渲染

```typescript
test('conditionally renders', async () => {
  render(<Toggle />)

  expect(screen.queryByText('Visible')).not.toBeInTheDocument()

  await user.click(screen.getByRole('button'))

  expect(screen.getByText('Visible')).toBeInTheDocument()
})
```

## 9. 快照测试

```typescript
test('matches snapshot', () => {
  const { container } = render(<Card title="Hello" />)
  expect(container).toMatchSnapshot()
})
```

## 10. 注意事项

- 测试用户可见行为
- 使用语义化查询
- 异步操作使用 waitFor
