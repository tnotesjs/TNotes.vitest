# [0071. Vue 组件测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0071.%20Vue%20%E7%BB%84%E4%BB%B6%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装依赖](#3-安装依赖)
- [4. 配置](#4-配置)
- [5. 渲染组件](#5-渲染组件)
- [6. props 测试](#6-props-测试)
- [7. emits 测试](#7-emits-测试)
- [8. slots 测试](#8-slots-测试)
- [9. 异步组件](#9-异步组件)
- [10. 快照测试](#10-快照测试)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 @vue/test-utils
- 配置 jsdom 环境
- 渲染组件（mount）
- props 测试
- emits 测试
- slots 测试
- 异步组件
- 快照测试

## 2. 评价

Vue 组件测试使用 @vue/test-utils 提供的 mount 方法渲染组件。支持测试 props、emits、slots 等组件接口，配合 Testing Library 可以测试用户交互。

## 3. 安装依赖

```bash
pnpm add -D @vue/test-utils jsdom
```

## 4. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom',
  },
})
```

## 5. 渲染组件

```typescript
import { mount } from '@vue/test-utils'
import { test, expect } from 'vitest'
import Button from './Button.vue'

test('renders button', () => {
  const wrapper = mount(Button, {
    props: { label: 'Click me' },
  })

  expect(wrapper.text()).toContain('Click me')
})
```

## 6. props 测试

```typescript
test('props', () => {
  const wrapper = mount(UserCard, {
    props: {
      name: 'Alice',
      age: 25,
    },
  })

  expect(wrapper.find('.name').text()).toBe('Alice')
  expect(wrapper.find('.age').text()).toBe('25')
})
```

## 7. emits 测试

```typescript
test('emits click event', async () => {
  const wrapper = mount(Button)

  await wrapper.find('button').trigger('click')

  expect(wrapper.emitted('click')).toHaveLength(1)
})
```

## 8. slots 测试

```typescript
test('renders slot content', () => {
  const wrapper = mount(Card, {
    slots: {
      default: '<p>Card content</p>',
      header: '<h2>Title</h2>',
    },
  })

  expect(wrapper.find('h2').text()).toBe('Title')
  expect(wrapper.find('p').text()).toBe('Card content')
})
```

## 9. 异步组件

```typescript
test('async component', async () => {
  const wrapper = mount(AsyncComponent)

  await wrapper.vm.$nextTick()

  expect(wrapper.find('.loaded').exists()).toBe(true)
})
```

## 10. 快照测试

```typescript
test('snapshot', () => {
  const wrapper = mount(Button, {
    props: { label: 'Click' },
  })

  expect(wrapper.html()).toMatchSnapshot()
})
```
