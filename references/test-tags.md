# 测试标签系统

## 标签定义

| 标签 | 符号 | 含义 | 优先级 |
|------|------|------|--------|
| `critical` | 🔴 | 核心功能，必须通过 | P0 |
| `important` | 🟡 | 重要功能，建议通过 | P1 |
| `standard` | 🟢 | 标准功能，正常通过 | P2 |
| `suggestion` | 💡 | 优化建议 | P3 |
| `edge-case` | 🎯 | 边界用例 | - |
| `security` | 🔒 | 安全测试 | P0 |
| `performance` | ⚡ | 性能测试 | P1 |

---

## 使用场景

### 🔴 [critical] - 核心功能测试

**场景**:
- 用户认证/注册
- 支付/交易逻辑
- 数据删除/修改
- 安全关键功能

**示例**:
```markdown
🔴 [critical] 用户注册 - 有效邮箱应创建账户
- 测试: POST /api/users 注册新用户
- 预期: 返回 201 和用户信息
- 优先级: 必须通过才能发布
```

---

### 🟡 [important] - 重要功能测试

**场景**:
- 核心业务逻辑
- 数据验证
- 错误处理
- API 响应格式

**示例**:
```markdown
🟡 [important] 邮箱格式验证
- 测试: 无效邮箱应返回 400
- 预期: 返回验证错误信息
- 覆盖率: 需覆盖 5+ 边界情况
```

---

### 🟢 [standard] - 标准功能测试

**场景**:
- 辅助函数
- 数据转换
- 格式处理
- 工具方法

**示例**:
```markdown
🟢 [standard] 日期格式化
- 测试: 不同格式的日期转换
- 预期: 统一返回 ISO 格式
```

---

### 🎯 [edge-case] - 边界用例测试

**场景**:
- 空值处理
- 超大/超小值
- 特殊字符
- 并发操作

**示例**:
```markdown
🎯 [edge-case] 空字符串处理
- 输入: ""
- 预期: 返回默认值或抛出 ValidationError

🎯 [edge-case] 超长输入
- 输入: string.repeat('a', 10000)
- 预期: 返回错误或截断
```

---

### 🔒 [security] - 安全测试

**场景**:
- SQL/NoSQL 注入
- XSS 防护
- CSRF 令牌
- 认证绕过

**示例**:
```markdown
🔒 [security] SQL 注入防护
- 输入: "'; DROP TABLE users; --"
- 预期: 返回 400 或转义处理
- 必须: 所有注入尝试都应被拦截
```

---

### ⚡ [performance] - 性能测试

**场景**:
- 响应时间
- 并发处理
- 资源使用
- 负载能力

**示例**:
```markdown
⚡ [performance] API 响应时间
- 并发: 100 用户
- 目标: p95 < 500ms
- 阈值: 错误率 < 1%
```

---

### 💡 [suggestion] - 优化建议

**场景**:
- 测试代码优化
- 更好的 Mock 策略
- 覆盖率改进
- 测试数据管理

**示例**:
```markdown
💡 [suggestion] 使用共享 Fixture
- 当前: 每个测试创建自己的数据
- 建议: 使用 beforeAll 创建共享数据
- 好处: 减少测试运行时间
```

---

## 测试分类矩阵

| 功能类型 | 必须标签 | 建议标签 |
|----------|----------|----------|
| 认证/授权 | 🔴 critical, 🔒 security | 🟡 important |
| 支付/交易 | 🔴 critical, 🔒 security | ⚡ performance |
| 数据验证 | 🔴 critical | 🟡 important, 🎯 edge-case |
| API 端点 | 🟡 important | 🎯 edge-case |
| 业务逻辑 | 🟡 important | 🟢 standard |
| 工具函数 | 🟢 standard | 💡 suggestion |
| UI 组件 | 🟡 important | 🎯 edge-case |

---

## 输出格式示例

```markdown
## Test Generation Report

| Priority | Tag | Count | Pass Rate |
|----------|-----|-------|-----------|
| P0 | 🔴 critical | 5 | 100% |
| P1 | 🟡 important | 12 | 92% |
| P2 | 🟢 standard | 8 | 100% |
| - | 🎯 edge-case | 15 | 87% |
| - | 🔒 security | 3 | 100% |
| - | ⚡ performance | 2 | 100% |

### Critical Tests (P0)
1. [🔴] 用户注册 - 有效邮箱创建账户
2. [🔴] 用户登录 - 正确凭据返回 Token
3. [🔴] 支付处理 - 余额不足时拒绝
4. [🔒] SQL 注入防护 - 所有注入尝试被拦截
5. [🔴] 数据删除 - 确认删除操作

### Important Tests (P1)
1. [🟡] 邮箱验证 - 格式错误返回 400
2. [🟡] 密码强度 - 弱密码被拒绝
...
```