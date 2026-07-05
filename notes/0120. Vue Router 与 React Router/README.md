# [0120. Vue Router 与 React Router](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0120.%20Vue%20Router%20%E4%B8%8E%20React%20Router)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Vue Router 测试](#3-vue-router-测试)
- [4. Vue Router 导航测试](#4-vue-router-导航测试)
- [5. React Router 测试](#5-react-router-测试)
- [6. React Router 导航测试](#6-react-router-导航测试)
- [7. 路由参数](#7-路由参数)
- [8. 路由守卫](#8-路由守卫)
- [9. Mock 路由](#9-mock-路由)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Vue Router 测试（createMemoryHistory）
- Vue Router 导航测试
- React Router 测试（MemoryRouter）
- React Router 导航测试
- 路由参数
- 路由守卫
- Mock 路由

## 2. 评价

- 路由测试使用 MemoryHistory 避免影响浏览器地址栏。每个测试创建独立的 Router 实例，确保测试隔离。

## 3. Vue Router 测试

```bash
pnpm add -D @vue/test-utils
```

```typescript
import { mount } from '@vue/test-utils'
import { createRouter, createMemoryHistory } from 'vue-router'
import App from './App.vue'

const router = createRouter({
  history: createMemoryHistory(),
  routes: [
    { path: '/', component: { template: '<div>Home</div>' } },
    { path: '/about', component: { template: '<div>About</div>' } },
  ],
})

test('Vue Router', async () => {
  const wrapper = mount(App, {
    global: {
      plugins: [router],
    },
  })

  await router.push('/')
  await router.isReady()

  expect(wrapper.text()).toContain('Home')
})
```

## 4. Vue Router 导航测试

```typescript
test('navigation', async () => {
  await router.push('/about')
  await router.isReady()

  expect(router.currentRoute.value.path).toBe('/about')
})
```

## 5. React Router 测试

```bash
pnpm add -D @testing-library/react
```

```typescript
import { render, screen } from '@testing-library/react'
import { MemoryRouter } from 'react-router-dom'
import App from './App'

test('React Router', () => {
  render(
    <MemoryRouter initialEntries={['/']}>
      <App />
    </MemoryRouter>,
  )

  expect(screen.getByText('Home')).toBeInTheDocument()
})
```

## 6. React Router 导航测试

```typescript
import userEvent from '@testing-library/user-event'

test('navigation', async () => {
  const user = userEvent.setup()

  render(
    <MemoryRouter initialEntries={['/']}>
      <App />
    </MemoryRouter>,
  )

  await user.click(screen.getByText('About'))
  expect(screen.getByText('About Page')).toBeInTheDocument()
})
```

## 7. 路由参数

```typescript
test('route params', () => {
  render(
    <MemoryRouter initialEntries={['/user/1']}>
      <App />
    </MemoryRouter>,
  )

  expect(screen.getByText('User 1')).toBeInTheDocument()
})
```

## 8. 路由守卫

```typescript
test('route guard', async () => {
  const router = createRouter({
    history: createMemoryHistory(),
    routes: [
      {
        path: '/protected',
        component: Protected,
        beforeEnter: () => false,
      },
    ],
  })
})
```

## 9. Mock 路由

```typescript
vi.mock('vue-router', () => ({
  useRouter: () => ({
    push: vi.fn(),
    replace: vi.fn(),
  }),
  useRoute: () => ({
    params: { id: '1' },
  }),
}))
```

## 10. 注意事项

- 使用 `createMemoryHistory` 而非 `createWebHistory`
- 每个测试创建独立的 Router 实例
- 等待路由就绪后再断言
