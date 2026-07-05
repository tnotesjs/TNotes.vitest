# [0136. 组合式函数与 Hook 测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0136.%20%E7%BB%84%E5%90%88%E5%BC%8F%E5%87%BD%E6%95%B0%E4%B8%8E%20Hook%20%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Vue Composable 测试](#3-vue-composable-测试)
- [4. React Hook 测试](#4-react-hook-测试)
- [5. 异步 Composable](#5-异步-composable)
- [6. 测试异步 Hook](#6-测试异步-hook)
- [7. 带依赖的 Hook](#7-带依赖的-hook)
- [8. 参数变化](#8-参数变化)
- [9. 清理](#9-清理)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Vue Composable 测试（直接调用）
- React Hook 测试（renderHook）
- 异步 Composable
- 测试异步 Hook（waitForNextUpdate）
- 带依赖的 Hook
- 参数变化
- 清理

## 2. 评价

Vue Composable 可以直接测试返回值，React Hook 需要使用 renderHook。异步操作需要等待更新后再断言。

## 3. Vue Composable 测试

```typescript
// useCounter.ts
import { ref } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)

  function increment() {
    count.value++
  }

  function decrement() {
    count.value--
  }

  return { count, increment, decrement }
}
```

```typescript
// useCounter.test.ts
import { useCounter } from './useCounter'

test('useCounter', () => {
  const { count, increment, decrement } = useCounter(0)

  expect(count.value).toBe(0)

  increment()
  expect(count.value).toBe(1)

  decrement()
  expect(count.value).toBe(0)
})
```

## 4. React Hook 测试

```typescript
// useCounter.ts
import { useState } from 'react'

export function useCounter(initial = 0) {
  const [count, setCount] = useState(initial)

  const increment = () => setCount((c) => c + 1)
  const decrement = () => setCount((c) => c - 1)

  return { count, increment, decrement }
}
```

```typescript
// useCounter.test.ts
import { renderHook, act } from '@testing-library/react'
import { useCounter } from './useCounter'

test('useCounter', () => {
  const { result } = renderHook(() => useCounter(0))

  expect(result.current.count).toBe(0)

  act(() => {
    result.current.increment()
  })

  expect(result.current.count).toBe(1)
})
```

## 5. 异步 Composable

```typescript
// useFetch.ts
export function useFetch(url: string) {
  const data = ref(null)
  const loading = ref(true)
  const error = ref(null)

  onMounted(async () => {
    try {
      const response = await fetch(url)
      data.value = await response.json()
    } catch (e) {
      error.value = e
    } finally {
      loading.value = false
    }
  })

  return { data, loading, error }
}
```

## 6. 测试异步 Hook

```typescript
test('useFetch', async () => {
  const { result, waitForNextUpdate } = renderHook(() => useFetch('/api/data'))

  expect(result.current.loading).toBe(true)

  await waitForNextUpdate()

  expect(result.current.loading).toBe(false)
  expect(result.current.data).toBeDefined()
})
```

## 7. 带依赖的 Hook

```typescript
test('useAuth with store', () => {
  const store = createMockStore({ user: { name: 'Alice' } })

  const { result } = renderHook(() => useAuth(), {
    wrapper: ({ children }) => (
      <StoreProvider store={store}>{children}</StoreProvider>
    ),
  })

  expect(result.current.user.name).toBe('Alice')
})
```

## 8. 参数变化

```typescript
test('responds to param changes', () => {
  const { result, rerender } = renderHook(
    ({ initial }) => useCounter(initial),
    { initialProps: { initial: 0 } },
  )

  expect(result.current.count).toBe(0)

  rerender({ initial: 10 })
  expect(result.current.count).toBe(10)
})
```

## 9. 清理

```typescript
test('cleans up', () => {
  const cleanup = vi.fn()

  renderHook(() => {
    useEffect(() => cleanup, [])
  })

  // cleanup 在 unmount 时调用
  expect(cleanup).toHaveBeenCalled()
})
```

## 10. 注意事项

- Vue composable 直接测试返回值
- React Hook 使用 renderHook
- 异步操作使用 waitForNextUpdate
