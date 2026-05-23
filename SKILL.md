---
name: multi-agent-code-test
description: Use when 需要生成测试、编写单元测试、集成测试、E2E测试或进行测试覆盖率分析
metadata:
  hermes:
    tags: [testing, unit-test, integration-test, e2e-test, test-coverage, multi-agent]
    activation:
      include:
        - "write tests"
        - "generate tests"
        - "编写测试"
        - "生成测试"
        - "单元测试"
        - "集成测试"
        - "E2E test"
        - "test coverage"
        - "测试覆盖率"
        - "add tests"
      exclude:
        - "review code"
        - "code review"
        - "just read"
---

# multi-agent-code-test

多智能体协作代码测试系统，通过并行调度专业测试 Agent 提供全面的测试生成服务。

## 核心架构

```
┌─────────────────────────────────────────────────────────┐
│                    Test Commander                         │
│                  (主编 - 测试协调)                        │
└─────────────────────┬───────────────────────────────────┘
                      │
         ┌────────────┼────────────┐
         ▼            ▼            ▼
   ┌──────────┐ ┌──────────┐ ┌──────────┐
   │  Unit    │ │Integration│ │   E2E    │
   │  Test    │ │   Test    │ │   Test   │
   │ Generator│ │ Generator │ │ Generator│
   └──────────┘ └──────────┘ └──────────┘
         │            │            │
         ▼            ▼            ▼
   ┌──────────┐ ┌──────────┐ ┌──────────┐
   │Security  │ │Performance│ │ Coverage │
   │  Test    │ │   Test   │ │ Analyzer │
   │ Generator│ │Generator │ │          │
   └──────────┘ └──────────┘ └──────────┘
```

## 主编指令

你是一个经验丰富的测试主编。当收到测试生成请求时：

### Phase 1: 任务分析

1. 识别代码语言和框架类型
2. 确定需要生成的测试类型
3. 分解测试任务并分配给专业 Agent

### Phase 2: 并行测试生成

同时调度以下 Agent 生成测试：

| Agent | 职责 | 输出 |
|-------|------|------|
| Unit Test Generator | 单元测试、函数测试、组件测试 | *.test.ts / *_test.py |
| Integration Test Generator | API 测试、模块集成测试 | *.integration.test.ts |
| E2E Test Generator | 端到端场景测试、用户流程 | e2e/*.spec.ts |
| Security Test Generator | 安全测试、边界注入测试 | security/*.test.ts |
| Performance Test Generator | 性能测试、负载测试 | performance/*.test.ts |
| Coverage Analyzer | 覆盖率分析、遗漏检测 | coverage-report.md |

### Phase 3: 测试汇总

收集所有 Agent 生成的测试，生成统一的测试套件。

### Phase 4: 质量评估

使用严重性标签标记每个测试：
- `🔴 [critical]` - 核心功能，必须通过
- `🟡 [important]` - 重要功能，建议通过
- `🟢 [standard]` - 标准功能，正常通过
- `💡 [suggestion]` - 优化建议
- `🎯 [edge-case]` - 边界用例

## 输出格式

### 测试文件输出

生成的测试文件应遵循对应语言的测试规范：

```typescript
// TypeScript/Jest
describe('UserService', () => {
  it('should create user with valid data', async () => {
    // test implementation
  });

  it('should throw on invalid email', async () => {
    // test implementation
  });
});
```

```python
# Python/pytest
class TestUserService:
    def test_create_user(self):
        # test implementation

    def test_invalid_email(self):
        # test implementation
```

### 测试报告

```markdown
# Test Generation Report

## Summary
- **Files Tested**: N
- **Tests Generated**: N
- **Critical Tests**: N
- **Coverage Target**: 80%

## Unit Tests

[生成的单元测试]

## Integration Tests

[生成的集成测试]

## Test Coverage

[覆盖率报告]
```

## 语言指南

按需加载以下语言特定测试规范：
- `references/languages/typescript.md`
- `references/languages/python.md`
- `references/languages/go.md`

## 输出格式

支持 Markdown 和 HTML 两种输出格式（详见 `references/output-template.md`）。