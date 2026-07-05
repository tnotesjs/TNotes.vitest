# [0119. Pinia 与 Redux 与 Zustand](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0119.%20Pinia%20%E4%B8%8E%20Redux%20%E4%B8%8E%20Zustand)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Pinia 测试](#3-pinia-测试)
- [4. Redux 测试](#4-redux-测试)
- [5. Zustand 测试](#5-zustand-测试)
- [6. 测试 Store Actions](#6-测试-store-actions)
- [7. 测试 Store Getters](#7-测试-store-getters)
- [8. Mock Store](#8-mock-store)
- [9. 组件中的 Store 测试](#9-组件中的-store-测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Pinia 测试（setActivePinia）
- Redux 测试（configureStore）
- Zustand 测试
- Store Actions 测试
- Store Getters 测试
- Mock Store
- 组件中的 Store 测试

## 2. 评价

- 状态管理库的测试需要在每个测试前重置 Store 状态。Pinia 使用 setActivePinia 创建新实例，Redux 使用 configureStore。

## 3. Pinia 测试

```typescript
import { setActivePinia, createPinia } from 'pinia'
import { useUserStore } from './user'

beforeEach(() => {
  setActivePinia(createPinia())
})

test('Pinia store', () => {
  const store = useUserStore()
  store.setUser({ name: 'Alice' })
  expect(store.user.name).toBe('Alice')
})
```

## 4. Redux 测试

```typescript
import { configureStore } from '@reduxjs/toolkit'
import userReducer, { setUser } from './userSlice'

test('Redux store', () => {
  const store = configureStore({
    reducer: { user: userReducer },
  })

  store.dispatch(setUser({ name: 'Alice' }))
  expect(store.getState().user.name).toBe('Alice')
})
```

## 5. Zustand 测试

```typescript
import { useUserStore } from './userStore'

beforeEach(() => {
  useUserStore.setState({ user: null })
})

test('Zustand store', () => {
  const store = useUserStore.getState()
  store.setUser({ name: 'Alice' })
  expect(useUserStore.getState().user.name).toBe('Alice')
})
```

## 6. 测试 Store Actions

```typescript
test('Pinia actions', () => {
  const store = useUserStore()
  store.login('alice', 'password')
  expect(store.isLoggedIn).toBe(true)
  expect(store.user.name).toBe('Alice')
})
```

## 7. 测试 Store Getters

```typescript
test('Pinia getters', () => {
  const store = useUserStore()
  store.setUser({ firstName: 'Alice', lastName: 'Smith' })
  expect(store.fullName).toBe('Alice Smith')
})
```

## 8. Mock Store

```typescript
vi.mock('./userStore', () => ({
  useUserStore: vi.fn(() => ({
    user: { name: 'Mock' },
    isLoggedIn: true,
  })),
}))
```

## 9. 组件中的 Store 测试

```typescript
test('component with store', () => {
  const store = useUserStore()
  store.setUser({ name: 'Alice' })

  render(<UserProfile />)

  expect(screen.getByText('Alice')).toBeInTheDocument()
})
```

## 10. 注意事项

- 每个测试前重置 Store 状态
- 使用 `setActivePinia` 创建新的 Pinia 实例
- Mock Store 时确保类型正确
