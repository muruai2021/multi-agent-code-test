# 测试用例池

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