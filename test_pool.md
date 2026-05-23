# 测试用例池

> 测试员Agent使用此文档执行完整测试。

---

## 语法验证

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-SYNTAX-001 | frontmatter格式 | SKILL.md内容 | YAML格式正确 | 以`---`开头和结尾 |
| TC-SYNTAX-002 | 必需字段 | frontmatter | 包含name/description/version/author/license/platforms/metadata | 8个字段都存在 |
| TC-SYNTAX-003 | description长度 | description内容 | ≤1024字符 | `len(description) <= 1024` |
| TC-SYNTAX-004 | description开头 | description内容 | 以"Use when"开头 | `description.startswith("Use when")` |
| TC-SYNTAX-005 | version字段 | version内容 | 符合语义化版本 | `x.y.z`格式 |

---

## 功能测试

### 测试生成触发词测试

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-FUNC-001 | 英文触发 | "write tests" | 正确识别 | 触发skill |
| TC-FUNC-002 | 中文触发 | "生成测试" | 正确识别 | 触发skill |
| TC-FUNC-003 | 单元测试 | "unit test" | 正确识别 | 触发skill |
| TC-FUNC-004 | 集成测试 | "integration test" | 正确识别 | 触发skill |
| TC-FUNC-005 | 覆盖率 | "test coverage" | 正确识别 | 触发skill |

### 多Agent调度测试

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-FUNC-010 | 主编解析 | Python函数测试请求 | 识别语言 | 主编正确分解任务 |
| TC-FUNC-011 | 单元测试生成 | validateEmail函数 | 生成vitest/pytest测试 | 格式正确 |
| TC-FUNC-012 | 集成测试生成 | API端点代码 | 生成supertest测试 | 格式正确 |
| TC-FUNC-013 | 并行调度 | 包含多类型的代码 | 多个Generator并行 | 无串行等待 |

### 测试标签测试

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-FUNC-020 | critical标签 | 核心业务逻辑测试 | 🔴 [critical] | 正确标记 |
| TC-FUNC-021 | important标签 | 重要功能测试 | 🟡 [important] | 正确标记 |
| TC-FUNC-022 | standard标签 | 辅助函数测试 | 🟢 [standard] | 正确标记 |
| TC-FUNC-023 | edge-case标签 | 边界条件测试 | 🎯 [edge-case] | 正确标记 |

---

## 边界测试

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-BOUND-001 | 空输入 | 空字符串函数 | 返回测试占位符 | 不崩溃 |
| TC-BOUND-002 | 超长函数 | >1000行 | 分段生成测试 | 不崩溃 |
| TC-BOUND-003 | 无效语法 | 语法错误代码 | 跳过或报错 | 不崩溃 |
| TC-BOUND-004 | 复杂泛型 | TypeScript泛型嵌套 | 正确生成 | 不崩溃 |

---

## 安全测试

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-SEC-001 | SQL注入测试 | 包含SQL的代码 | 生成安全测试用例 | 包含注入检测 |
| TC-SEC-002 | XSS测试 | 包含用户输入的代码 | 生成XSS测试用例 | 包含转义验证 |
| TC-SEC-003 | 认证测试 | 包含认证的代码 | 生成auth测试 | 包含权限验证 |
| TC-SEC-004 | 敏感数据测试 | 包含敏感数据的代码 | 不在测试中暴露 | 无泄露 |

---

## 语言专项测试

### TypeScript/Vitest

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-TS-001 | 异步测试 | async函数 | 生成async/await测试 | 格式正确 |
| TC-TS-002 | Mock使用 | 包含外部依赖的函数 | 生成vi.mock() | 格式正确 |
| TC-TS-003 | Spy使用 | 需要监控的函数 | 生成vi.spyOn() | 格式正确 |
| TC-TS-004 | React组件测试 | React组件 | 生成@testing-library测试 | 格式正确 |

### Python/pytest

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-PY-001 | 异步测试 | async def函数 | 生成pytest-asyncio测试 | 格式正确 |
| TC-PY-002 | Fixture | 需要共享数据的测试 | 生成@pytest.fixture | 格式正确 |
| TC-PY-003 | 参数化 | 多组输入的测试 | 生成@pytest.mark.parametrize | 格式正确 |
| TC-PY-004 | Mock | 包含外部依赖的函数 | 生成unittest.mock | 格式正确 |

### Go/testing

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-GO-001 | 表格驱动 | 标准Go函数 | 生成table-driven测试 | 格式正确 |
| TC-GO-002 | Mock接口 | 包含接口的代码 | 生成mock实现 | 格式正确 |
| TC-GO-003 | HTTP测试 | HTTP handler | 生成httptest测试 | 格式正确 |
| TC-GO-004 | 并发测试 | goroutine测试 | 生成并发安全测试 | 格式正确 |

---

## 输出格式测试

| 用例ID | 测试项 | 输入 | 预期结果 | 通过条件 |
|--------|--------|------|----------|----------|
| TC-OUT-001 | 测试文件格式 | 生成的测试代码 | 符合语言规范 | 语法正确 |
| TC-OUT-002 | Markdown报告 | 审查结果 | 包含统计和覆盖率 | 格式正确 |
| TC-OUT-003 | HTML报告 | `--format html` | 完整CSS和HTML | 可渲染 |
| TC-OUT-004 | 覆盖率报告 | 测试执行结果 | 包含行/分支/函数覆盖率 | 数据准确 |

---

## 测试报告模板

```markdown
# 测试报告

**技能名称**：multi-agent-code-test
**版本**：1.0.0
**测试日期**：2026-05-23
**测试员**：测试员Agent

## 测试结果汇总

| 测试类型 | 用例数 | 通过 | 失败 | 通过率 |
|----------|--------|------|------|--------|
| 语法验证 | 5 | 5 | 0 | 100% |
| 功能测试 | 13 | 13 | 0 | 100% |
| 边界测试 | 4 | 4 | 0 | 100% |
| 安全测试 | 4 | 4 | 0 | 100% |
| 语言专项 | 12 | 12 | 0 | 100% |
| **总计** | **38** | **38** | **0** | **100%** |

## 失败用例详情

无

## 测试结论

- [x] 语法验证通过
- [x] 功能测试通过
- [x] 边界测试通过
- [x] 安全测试通过
- [x] 语言专项通过
- [x] **可进入评审阶段**
```

## 单元测试用例

### TC-001: TypeScript 函数测试

**输入代码**:
```typescript
export function validateEmail(email: string): boolean {
  return email.includes('@') && email.indexOf('@') > 0;
}

export function calculateTax(amount: number, rate: number): number {
  if (amount < 0) throw new Error('Amount must be positive');
  return amount * rate;
}
```

**预期生成测试**:
- test_validateEmail_validEmail → expect(true)
- test_validateEmail_invalidNoAt → expect(false)
- test_validateEmail_invalidNoDomain → expect(false)
- test_validateEmail_empty → expect(false)
- test_calculateTax_positiveAmount → expect(100)
- test_calculateTax_negativeAmount → expect(Error)

---

### TC-002: Python 类测试

**输入代码**:
```python
class User:
    def __init__(self, email: str, name: str):
        if '@' not in email:
            raise ValueError("Invalid email")
        self.email = email
        self.name = name

    def greet(self) -> str:
        return f"Hello, {self.name}!"

    @staticmethod
    def is_valid_email(email: str) -> bool:
        return '@' in email and len(email) > 3
```

**预期生成测试**:
- test_user_creation_valid_email → creates successfully
- test_user_creation_invalid_email → raises ValueError
- test_user_greet → returns greeting string
- test_user_is_valid_email_valid → returns True
- test_user_is_valid_email_invalid → returns False

---

### TC-003: Go 函数测试

**输入代码**:
```go
package calculator

func Add(a, b int) int {
    return a + b
}

func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, ErrDivideByZero
    }
    return a / b, nil
}
```

**预期生成测试**:
- TestAdd_positiveNumbers
- TestAdd_negativeNumbers
- TestAdd_mixedNumbers
- TestDivide_normal → no error
- TestDivide_byZero → returns ErrDivideByZero

---

### TC-004: 异步函数测试

**输入代码**:
```typescript
export async function fetchUser(id: string): Promise<User | null> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) return null;
  return response.json();
}
```

**预期生成测试**:
- test_fetchUser_success → returns user
- test_fetchUser_notFound → returns null
- test_fetchUser_error → returns null

---

### TC-005: 错误处理测试

**输入代码**:
```typescript
export function parseJSON(str: string): unknown {
  try {
    return JSON.parse(str);
  } catch (e) {
    throw new ParseError('Invalid JSON');
  }
}
```

**预期生成测试**:
- test_parseJSON_valid → returns parsed object
- test_parseJSON_invalid → throws ParseError
- test_parseJSON_empty → throws ParseError

---

## 集成测试用例

### TC-101: API 端点集成测试

**输入代码**:
```typescript
// POST /api/users
app.post('/api/users', async (req, res) => {
  const { email, name } = req.body;
  const user = await userService.create({ email, name });
  res.status(201).json(user);
});
```

**预期生成测试**:
- POST /api/users valid → 201 + user
- POST /api/users missing email → 400
- POST /api/users invalid email → 400
- POST /api/users duplicate → 409

---

### TC-102: 数据库集成测试

**输入代码**:
```python
class UserRepository:
    def __init__(self, db):
        self.db = db

    def find_by_email(self, email: str):
        cursor = self.db.execute(
            "SELECT * FROM users WHERE email = ?", (email,)
        )
        return cursor.fetchone()

    def create(self, email: str, name: str):
        self.db.execute(
            "INSERT INTO users (email, name) VALUES (?, ?)",
            (email, name)
        )
```

**预期生成测试**:
- test_find_by_email_exists → returns user
- test_find_by_email_not_found → returns None
- test_create_user → inserts successfully
- test_create_duplicate → raises IntegrityError

---

## 安全测试用例

### TC-201: SQL 注入防护测试

**输入代码**:
```python
def search_users(query: str):
    sql = f"SELECT * FROM users WHERE name LIKE '%{query}%'"
    cursor.execute(sql)
```

**预期生成测试**:
- test_search_normal_input → works
- test_search_sql_injection → parameter sanitized
- test_search_orm_equivalent → using parameterized query

---

### TC-202: XSS 防护测试

**输入代码**:
```typescript
function escapeHtml(input: string): string {
  return input
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;');
}
```

**预期生成测试**:
- test_escapeHtml_ampersand
- test_escapeHtml_tags
- test_escapeHtml_mixed
- test_escapeHtml_xss_attempt

---

## 性能测试用例

### TC-301: 大数据处理测试

**输入代码**:
```typescript
function processItems(items: string[]): Map<string, number> {
  const counts = new Map();
  for (const item of items) {
    counts.set(item, (counts.get(item) || 0) + 1);
  }
  return counts;
}
```

**预期生成测试**:
- test_processItems_small_dataset → completes < 10ms
- test_processItems_large_dataset → completes < 100ms
- test_processItems_empty → returns empty Map

---

### TC-302: 并发处理测试

**输入代码**:
```go
func ProcessConcurrently(tasks []Task) []Result {
    var wg sync.WaitGroup
    results := make(chan Result, len(tasks))

    for _, task := range tasks {
        wg.Add(1)
        go func(t Task) {
            defer wg.Done()
            results <- process(t)
        }(task)
    }

    wg.Wait()
    close(results)

    var output []Result
    for r := range results {
        output = append(output, r)
    }
    return output
}
```

**预期生成测试**:
- test_process_concurrently_correctness
- test_process_concurrently_order
- test_process_concurrently_empty

---

## 边界条件测试用例

### TC-401: 边界值测试

| 输入 | 预期 |
|------|------|
| 空字符串 `""` | 处理或拒绝 |
| 超长字符串 10KB | 处理或截断 |
| `null`/`nil` | 抛出错误 |
| 负数 | 视业务逻辑 |
| `0` | 视业务逻辑 |
| 最大整数 | 不溢出 |
| Unicode 字符 | 正确处理 |
| 特殊字符 `!@#$%` | 正确处理 |

---

### TC-402: 状态测试

**输入代码**:
```typescript
class Counter {
  private count = 0;

  increment(): number {
    return ++this.count;
  }

  decrement(): number {
    return --this.count;
  }

  getCount(): number {
    return this.count;
  }
}
```

**预期生成测试**:
- test_increment_once → count is 1
- test_increment_multiple → count increments
- test_decrement_from_zero → count is -1
- test_decrement_after_increment → returns to zero
- test_getCount_initial → returns 0

---

## 测试覆盖率验证用例

### TC-501: 分支覆盖验证

**输入代码**:
```typescript
function classify(n: number): string {
  if (n > 0) {
    if (n > 10) return 'big positive';
    return 'small positive';
  } else if (n < 0) {
    return 'negative';
  }
  return 'zero';
}
```

**要求覆盖**:
- [ ] n > 10
- [ ] 0 < n <= 10
- [ ] n === 0
- [ ] n < 0

**测试用例**:
- test_classify_big_positive (n=15)
- test_classify_small_positive (n=5)
- test_classify_zero (n=0)
- test_classify_negative (n=-5)

---

### TC-502: 错误路径覆盖

**输入代码**:
```python
def divide(a: int, b: int) -> float:
    try:
        return a / b
    except ZeroDivisionError:
        raise ValueError("Cannot divide by zero")
    except TypeError:
        raise ValueError("Invalid input types")
```

**要求覆盖**:
- [ ] 正常除法
- [ ] 除以零
- [ ] 类型错误
- [ ] 其他异常