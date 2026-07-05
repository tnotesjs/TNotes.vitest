# [0114. 临时文件测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0114.%20%E4%B8%B4%E6%97%B6%E6%96%87%E4%BB%B6%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 创建临时目录](#3-创建临时目录)
- [4. 写入临时文件](#4-写入临时文件)
- [5. 临时文件清理](#5-临时文件清理)
- [6. 使用 tmp 库](#6-使用-tmp-库)
- [7. 文件系统测试](#7-文件系统测试)
- [8. 并行测试隔离](#8-并行测试隔离)
- [9. 超时清理](#9-超时清理)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 创建临时目录（mkdtemp）
- 写入临时文件
- 临时文件清理（afterEach）
- 使用 tmp 库
- 文件系统测试
- 并行测试隔离

## 2. 评价

- 临时文件测试需要确保每个测试使用独立目录，并在测试后清理。使用 tmp 库或 Node.js 原生 API 创建临时目录。

## 3. 创建临时目录

```typescript
import { mkdtemp, rm } from 'node:fs/promises'
import { tmpdir } from 'node:os'
import { join } from 'node:path'

describe('temp files', () => {
  let tempDir: string

  beforeEach(async () => {
    tempDir = await mkdtemp(join(tmpdir(), 'vitest-'))
  })

  afterEach(async () => {
    await rm(tempDir, { recursive: true, force: true })
  })
})
```

## 4. 写入临时文件

```typescript
import { writeFile, readFile } from 'node:fs/promises'

test('write and read', async () => {
  const filePath = join(tempDir, 'test.txt')
  await writeFile(filePath, 'hello')

  const content = await readFile(filePath, 'utf-8')
  expect(content).toBe('hello')
})
```

## 5. 临时文件清理

使用 `afterEach` 确保清理：

```typescript
afterEach(async () => {
  await rm(tempDir, { recursive: true, force: true })
})
```

## 6. 使用 tmp 库

```bash
pnpm add -D tmp
```

```typescript
import tmp from 'tmp'

test('tmp library', async () => {
  const { name, removeCallback } = tmp.dirSync()

  // 使用临时目录

  removeCallback()
})
```

## 7. 文件系统测试

```typescript
test('file operations', async () => {
  const filePath = join(tempDir, 'data.json')
  const data = { name: 'test' }

  await writeFile(filePath, JSON.stringify(data))
  const content = JSON.parse(await readFile(filePath, 'utf-8'))

  expect(content).toEqual(data)
})
```

## 8. 并行测试隔离

每个测试使用独立的临时目录：

```typescript
describe('parallel safe', () => {
  let tempDir: string

  beforeEach(async () => {
    tempDir = await mkdtemp(join(tmpdir(), 'test-'))
  })
})
```

## 9. 超时清理

确保清理不会超时：

```typescript
afterEach(async () => {
  try {
    await rm(tempDir, { recursive: true, force: true })
  } catch {
    // 忽略清理错误
  }
})
```

## 10. 注意事项

- 每个测试使用独立的临时目录
- 确保测试后清理临时文件
- 使用 try/catch 容忍清理失败
