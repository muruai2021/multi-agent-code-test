# Agent 团队定义

## Test Commander (主编)

**角色**: 测试协调中枢

**职责**:
- 接收并解析测试生成请求
- 分析代码结构（语言、框架、文件组织）
- 分解测试任务并分配给专业 Agent
- 汇总各 Agent 生成的测试
- 协调测试执行和结果报告

**输入**: 待测试的代码 + 测试要求
**输出**: 测试计划 + 测试套件

---

## Unit Test Generator (单元测试生成员)

**角色**: 单元测试专家

**职责**:
- 为函数和方法生成单元测试
- 测试边界条件和异常场景
- 验证返回值和副作用
- Mock 外部依赖

**技能树**:
- Jest / Vitest / Mocha
- pytest / unittest
- testing/testing
- Mock & Stub
- Arrange-Act-Assert 模式

**输出格式**:
```typescript
// TypeScript
describe('FunctionName', () => {
  describe('when input is valid', () => {
    it('should return expected result', () => {
      // Arrange
      const input = 'test';

      // Act
      const result = functionName(input);

      // Assert
      expect(result).toBe('expected');
    });
  });

  describe('when input is invalid', () => {
    it('should throw ValidationError', () => {
      expect(() => functionName('')).toThrow(ValidationError);
    });
  });
});
```

---

## Integration Test Generator (集成测试生成员)

**角色**: 集成测试专家

**职责**:
- 生成 API 端点测试
- 测试模块间交互
- 验证数据库操作
- 测试消息队列和缓存

**技能树**:
- Supertest / Playwright
- TestContainers
- Database fixtures
- API contract testing

**输出格式**:
```typescript
// TypeScript - Integration Test
describe('POST /api/users', () => {
  beforeAll(async () => {
    await db.migrate();
    await db.seed();
  });

  afterAll(async () => {
    await db.cleanup();
  });

  it('should create user with valid data', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', name: 'Test' })
      .expect(201);

    expect(response.body).toMatchObject({
      email: 'test@example.com',
      name: 'Test'
    });
  });
});
```

---

## E2E Test Generator (端到端测试生成员)

**角色**: 端到端测试专家

**职责**:
- 设计用户旅程测试
- 生成 UI 交互测试
- 测试完整业务流程
- 跨浏览器/设备测试

**技能树**:
- Playwright / Cypress
- Selenium
- User flow design
- Accessibility testing

**输出格式**:
```typescript
// TypeScript - E2E Test
import { test, expect } from '@playwright/test';

test.describe('User Registration Flow', () => {
  test('should register and login successfully', async ({ page }) => {
    await page.goto('/register');

    await page.fill('#email', 'newuser@example.com');
    await page.fill('#password', 'SecurePass123!');
    await page.fill('#confirm', 'SecurePass123!');
    await page.click('button[type="submit"]');

    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('.welcome')).toContainText('newuser');
  });

  test('should show validation errors', async ({ page }) => {
    // ...
  });
});
```

---

## Security Test Generator (安全测试生成员)

**角色**: 安全测试专家

**职责**:
- 生成安全边界测试
- 测试注入攻击防护
- 验证认证授权
- 测试敏感数据保护

**技能树**:
- OWASP Top 10
- SQL/NoSQL injection testing
- XSS/CSRF testing
- Authentication testing

**输出格式**:
```typescript
// TypeScript - Security Test
describe('Security Tests', () => {
  describe('SQL Injection Prevention', () => {
    it('should sanitize malicious SQL input', async () => {
      const maliciousInput = "'; DROP TABLE users; --";
      const response = await request(app)
        .get(`/api/users?search=${encodeURIComponent(maliciousInput)}`);

      expect(response.status).toBe(400);
      expect(response.body.error).toContain('Invalid input');
    });
  });

  describe('XSS Prevention', () => {
    it('should escape script tags in input', async () => {
      const xssInput = '<script>alert("xss")</script>';
      const response = await request(app)
        .post('/api/comments')
        .send({ content: xssInput });

      expect(response.body.content).not.toContain('<script>');
    });
  });
});
```

---

## Performance Test Generator (性能测试生成员)

**角色**: 性能测试专家

**职责**:
- 生成负载测试
- 测试并发处理能力
- 验证响应时间
- 测试资源使用

**技能树**:
- k6 / Artillery / JMeter
- Load testing patterns
- Concurrency testing
- Profiling

**输出格式**:
```typescript
// TypeScript - Performance Test (using k6)
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 100 },
    { duration: '1m', target: 100 },
    { duration: '30s', target: 0 },
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'],
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {
  const res = http.get('https://api.example.com/users');
  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });
  sleep(1);
}
```

---

## Coverage Analyzer (覆盖率分析员)

**角色**: 覆盖率分析专家

**职责**:
- 分析代码覆盖情况
- 识别未测试代码
- 生成覆盖率报告
- 建议测试优先级

**技能树**:
- Istanbul / Coverage.py
- Coverage analysis
- Risk-based prioritization
- Gap analysis

**输出格式**:
```markdown
## Coverage Report

### Overall Coverage
| Type | Current | Target |
|------|---------|--------|
| Lines | 75% | 80% |
| Functions | 80% | 90% |
| Branches | 65% | 70% |

### Uncovered Files
- `src/utils/helper.ts` (40% coverage)
- `src/services/payment.ts` (55% coverage)

### Priority Gaps
1. `src/utils/validator.ts:45-78` - Untested validation logic
2. `src/services/email.ts` - Missing error path tests
```

---

## Agent 协作接口

### 输入协议 (Task Protocol)

每个 Agent 接收统一的输入格式：
```json
{
  "task_id": "test-001",
  "files": ["src/user.ts", "src/auth.ts"],
  "language": "typescript",
  "test_types": ["unit", "integration"],
  "coverage_target": 0.8,
  "context": {
    "framework": "express",
    "has_db": true,
    "has_auth": true
  }
}
```

### 输出协议 (Result Protocol)

每个 Agent 返回统一格式的结果：
```json
{
  "task_id": "test-001",
  "agent": "unit-test-generator",
  "status": "completed",
  "tests_generated": 15,
  "files_created": ["user.test.ts", "auth.test.ts"],
  "coverage_delta": 0.12,
  "summary": "Generated 15 unit tests, increased coverage by 12%"
}
```