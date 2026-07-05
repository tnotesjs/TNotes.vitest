# [0127. 测试命名](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0127.%20%E6%B5%8B%E8%AF%95%E5%91%BD%E5%90%8D)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 行为导向命名](#3-行为导向命名)
- [4. 避免实现细节](#4-避免实现细节)
- [5. 使用 should](#5-使用-should)
- [6. describe 命名](#6-describe-命名)
- [7. 一致性](#7-一致性)
- [8. 边界条件](#8-边界条件)
- [9. 错误场景](#9-错误场景)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 行为导向命名（描述行为而非实现）
- 避免实现细节
- 使用 should 开头
- describe 命名（功能模块）
- 一致性
- 边界条件
- 错误场景

## 2. 评价

- 好的测试命名应该描述预期行为，而不是实现细节
- 团队应统一命名风格，保持一致性

## 3. 行为导向命名

测试名称应该描述行为，而不是实现：

```typescript
// 好的命名
test('should return user name when given valid id', () => {})
test('should throw error when user not found', () => {})

// 不好的命名
test('getUser function', () => {})
test('test 1', () => {})
```

## 4. 避免实现细节

```typescript
// 好的命名
test('should display welcome message after login', () => {})

// 不好的命名
test('should call setState after API response', () => {})
```

## 5. 使用 should

```typescript
test('should validate email format', () => {})
test('should return empty array for invalid input', () => {})
```

## 6. describe 命名

```typescript
// 描述被测试的功能
describe('UserService', () => {
  describe('getUser', () => {
    test('should return user by id', () => {})
  })
})
```

## 7. 一致性

保持团队一致的命名风格：

```typescript
// 统一使用 "should" 开头
test('should do something', () => {})

// 或统一使用动词开头
test('returns user data', () => {})
```

## 8. 边界条件

```typescript
test('should handle empty input', () => {})
test('should handle null values', () => {})
test('should handle maximum length', () => {})
```

## 9. 错误场景

```typescript
test('should throw when input is invalid', () => {})
test('should return 404 when resource not found', () => {})
```

## 10. 注意事项

- 测试名称应该清晰描述预期行为
- 避免使用 "test" 或 "should" 作为名称的一部分（除非团队约定）
- 保持命名风格一致
