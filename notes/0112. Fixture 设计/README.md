# [0112. Fixture 设计](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0112.%20Fixture%20%E8%AE%BE%E8%AE%A1)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 静态夹具](#3-静态夹具)
- [4. 工厂函数](#4-工厂函数)
- [5. 数据构造器](#5-数据构造器)
- [6. 随机数据](#6-随机数据)
- [7. 序列数据](#7-序列数据)
- [8. 夹具组合](#8-夹具组合)
- [9. 测试数据管理](#9-测试数据管理)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 静态夹具（直接定义数据）
- 工厂函数（创建测试数据）
- 数据构造器（Builder 模式）
- 随机数据
- 序列数据
- 夹具组合

## 2. 评价

- 好的 Fixture 设计可以提高测试的可维护性。工厂函数是最常用的方式，支持覆盖默认值，确保每个测试有独立的数据。

## 3. 静态夹具

直接定义测试数据：

```typescript
const testUser = {
  id: 1,
  name: 'Alice',
  email: 'alice@example.com',
}

test('user', () => {
  expect(testUser.name).toBe('Alice')
})
```

## 4. 工厂函数

创建测试数据的函数：

```typescript
function createUser(overrides = {}) {
  return {
    id: 1,
    name: 'Alice',
    email: 'alice@example.com',
    ...overrides,
  }
}

test('user with custom name', () => {
  const user = createUser({ name: 'Bob' })
  expect(user.name).toBe('Bob')
})
```

## 5. 数据构造器

```typescript
class UserBuilder {
  private user = {
    id: 1,
    name: 'Alice',
    email: 'alice@example.com',
  }

  withName(name: string) {
    this.user.name = name
    return this
  }

  withEmail(email: string) {
    this.user.email = email
    return this
  }

  build() {
    return { ...this.user }
  }
}

const user = new UserBuilder()
  .withName('Bob')
  .withEmail('bob@example.com')
  .build()
```

## 6. 随机数据

```typescript
function randomUser() {
  return {
    id: Math.random(),
    name: `User-${Math.random().toString(36).slice(7)}`,
    email: `user-${Date.now()}@example.com`,
  }
}
```

## 7. 序列数据

```typescript
let counter = 0

function nextUser() {
  counter++
  return {
    id: counter,
    name: `User ${counter}`,
    email: `user${counter}@example.com`,
  }
}
```

## 8. 夹具组合

```typescript
function createTestScenario() {
  return {
    user: createUser(),
    posts: [createPost(), createPost()],
    comments: [createComment()],
  }
}
```

## 9. 测试数据管理

```typescript
// fixtures/users.ts
export const users = {
  admin: { id: 1, name: 'Admin', role: 'admin' },
  user: { id: 2, name: 'User', role: 'user' },
  guest: { id: 3, name: 'Guest', role: 'guest' },
}
```

## 10. 注意事项

- 夹具应该是可预测的
- 避免夹具之间的依赖
- 使用工厂函数创建独立的测试数据
