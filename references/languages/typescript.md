# TypeScript 测试生成指南

## 测试框架

推荐使用以下测试框架：
- **Vitest** - 现代、快速、支持 TypeScript（推荐）
- **Jest** - 成熟、稳定
- **Mocha** - 灵活、可扩展

## 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: ['node_modules', 'dist'],
    },
    include: ['src/**/*.test.ts'],
  },
});
```

## 测试模板

### 基础测试

```typescript
import { describe, it, expect, vi, beforeEach, afterEach } from 'vitest';
import { functionName } from '../src/module';

describe('ModuleName', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  afterEach(() => {
    vi.restoreAllMocks();
  });

  describe('functionName', () => {
    it('should return expected result for valid input', async () => {
      // Arrange
      const input = { field: 'value' };
      const expected = { success: true, data: 'result' };

      // Act
      const result = await functionName(input);

      // Assert
      expect(result).toEqual(expected);
    });

    it('should throw ValidationError for invalid input', async () => {
      const input = { field: '' };
      await expect(functionName(input)).rejects.toThrow(ValidationError);
    });
  });
});
```

### 异步测试

```typescript
describe('asyncFunction', () => {
  it('should resolve with data', async () => {
    const result = await asyncFunction();
    expect(result).toBeDefined();
  });

  it('should reject on error', async () => {
    vi.spyOn(service, 'fetch').mockRejectedValue(new Error('Network error'));
    await expect(asyncFunction()).rejects.toThrow('Network error');
  });
});
```

### Mock 使用

```typescript
import { vi } from 'vitest';

// Mock 模块
vi.mock('../src/dependency', () => ({
  getData: vi.fn(),
  saveData: vi.fn(),
}));

// Mock 函数
const mockFetch = vi.fn();
vi.stubGlobal('fetch', mockFetch);

// 在测试中设置
mockFetch.mockResolvedValue({ json: () => ({ data: 'test' }) });
```

### Spies

```typescript
import { vi, it } from 'vitest';

it('should call validator before saving', async () => {
  const validatorSpy = vi.spyOn(validator, 'validate');

  await saveUser(validUser);

  expect(validatorSpy).toHaveBeenCalledWith(validUser);
  expect(validatorSpy).toHaveBeenCalledOnce();
});
```

---

## React 组件测试

### 使用 Testing Library

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { vi } from 'vitest';
import { UserForm } from './UserForm';

describe('UserForm', () => {
  it('should submit form with user data', async () => {
    const onSubmit = vi.fn();
    render(<UserForm onSubmit={onSubmit} />);

    fireEvent.change(screen.getByLabelText('Email'), {
      target: { value: 'test@example.com' },
    });
    fireEvent.click(screen.getByRole('button', { name: 'Submit' }));

    expect(onSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
    });
  });
});
```

---

## API 测试

### 使用 Supertest

```typescript
import request from 'supertest';
import { app } from '../src/app';

describe('POST /api/users', () => {
  it('should create user with valid data', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({
        email: 'test@example.com',
        name: 'Test User',
      })
      .expect(201);

    expect(response.body).toMatchObject({
      email: 'test@example.com',
      name: 'Test User',
    });
  });

  it('should return 400 for invalid email', async () => {
    await request(app)
      .post('/api/users')
      .send({ email: 'invalid' })
      .expect(400);
  });
});
```

---

## 测试覆盖率目标

| 类型 | 目标 | 说明 |
|------|------|------|
| Statements | 80% | 覆盖所有可执行语句 |
| Branches | 70% | 覆盖所有分支路径 |
| Functions | 90% | 覆盖所有函数 |
| Lines | 80% | 覆盖所有代码行 |

---

## 常见测试模式

### Table-Driven Tests

```typescript
describe('validateEmail', () => {
  const testCases = [
    { input: 'test@example.com', expected: true },
    { input: 'invalid', expected: false },
    { input: '@example.com', expected: false },
    { input: 'test@', expected: false },
  ];

  testCases.forEach(({ input, expected }) => {
    it(`should return ${expected} for ${input}`, () => {
      expect(validateEmail(input)).toBe(expected);
    });
  });
});
```

### Test Fixture Factory

```typescript
function createMockUser(overrides = {}) {
  return {
    id: 'usr_123',
    email: 'test@example.com',
    name: 'Test User',
    role: 'member',
    createdAt: new Date('2024-01-01'),
    ...overrides,
  };
}

// Usage
const user = createMockUser({ email: 'custom@example.com' });
const admin = createMockUser({ role: 'admin' });
```

---

## NestJS 测试

### 单元测试 Service

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { UserService } from './user.service';

describe('UserService', () => {
  let service: UserService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UserService,
        {
          provide: UserRepository,
          useValue: {
            findById: vi.fn(),
            save: vi.fn(),
          },
        },
      ],
    }).compile();

    service = module.get<UserService>(UserService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  it('should find user by id', async () => {
    const user = createMockUser();
    vi.spyOn(module.get(UserRepository), 'findById').mockResolvedValue(user);

    const result = await service.findById('usr_123');
    expect(result).toEqual(user);
  });
});
```

### Integration Test

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';

describe('AppController (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  afterEach(async () => {
    await app.close();
  });

  it('/GET health', () => {
    return request(app.getHttpServer())
      .get('/health')
      .expect(200)
      .expect('OK');
  });
});
```