# 测试生成阶段

## 阶段总览

| 阶段 | 名称 | 重点 |
|------|------|------|
| Phase 1 | Context Analysis | 理解代码上下文 |
| Phase 2 | Test Planning | 设计测试策略 |
| Phase 3 | Test Generation | 生成测试代码 |
| Phase 4 | Quality Check | 测试质量验证 |

---

## Phase 1: Context Analysis (上下文分析)

### 目标
理解待测试代码的结构和特性

### 任务清单
- [ ] 识别代码语言和框架
- [ ] 分析文件结构和模块关系
- [ ] 理解业务逻辑和函数签名
- [ ] 识别外部依赖（数据库、API、缓存）
- [ ] 检查是否有现有测试
- [ ] 确定测试边界

### 输出
```markdown
### Context Summary
- **Language**: TypeScript
- **Framework**: Express.js
- **Files**: 5 files to test
- **Dependencies**: PostgreSQL, Redis
- **Existing Tests**: 2 test files (40% coverage)
- **Test Target**: 80% coverage
```

---

## Phase 2: Test Planning (测试规划)

### 目标
设计全面的测试策略

### 任务清单
- [ ] 确定测试类型组合
- [ ] 规划测试数据
- [ ] 设计测试场景
- [ ] 确定 Mock 策略
- [ ] 规划覆盖率目标

### 测试类型选择

#### 单元测试场景
| 函数类型 | 测试重点 |
|----------|----------|
| 纯函数 | 输入-输出映射 |
| 异步函数 | 成功/失败路径 |
| 验证函数 | 正则边界 |
| 转换函数 | 数据格式转换 |

#### 集成测试场景
| 组件类型 | 测试重点 |
|----------|----------|
| API 端点 | 请求-响应 |
| 数据库操作 | CRUD 操作 |
| 缓存交互 | 命中/未命中 |
| 消息队列 | 生产-消费 |

---

## Phase 3: Test Generation (测试生成)

### 目标
生成完整可运行的测试代码

### 任务清单
- [ ] 生成单元测试
- [ ] 生成集成测试
- [ ] 生成 E2E 测试（如适用）
- [ ] 生成安全测试（如适用）
- [ ] 生成性能测试（如适用）

### 测试代码规范

#### TypeScript 测试模板

```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { functionName } from '../src/module';

describe('ModuleName', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  describe('functionName', () => {
    describe('when input is valid', () => {
      it('should return expected result', async () => {
        // Arrange
        const input = createValidInput();
        const expected = { success: true, data: 'result' };

        // Act
        const result = await functionName(input);

        // Assert
        expect(result).toEqual(expected);
      });
    });

    describe('when input is invalid', () => {
      it('should throw ValidationError', async () => {
        const input = createInvalidInput();
        await expect(functionName(input)).rejects.toThrow(ValidationError);
      });
    });

    describe('when external service fails', () => {
      it('should return cached result', async () => {
        // Arrange
        vi.spyOn(cache, 'get').mockResolvedValue({ cached: true });

        // Act
        const result = await functionName({ useCache: true });

        // Assert
        expect(result.cached).toBe(true);
      });
    });
  });
});
```

#### Python 测试模板

```python
import pytest
from module import function_name

class TestModule:
    @pytest.fixture
    def valid_input(self):
        return {"key": "value"}

    @pytest.fixture
    def invalid_input(self):
        return {"key": ""}

    def test_function_name_with_valid_input(self, valid_input):
        result = function_name(valid_input)
        assert result.success is True
        assert result.data == "result"

    def test_function_name_with_invalid_input(self, invalid_input):
        with pytest.raises(ValidationError):
            function_name(invalid_input)

    @pytest.mark.asyncio
    async def test_async_function(self):
        result = await async_function()
        assert result == expected
```

#### Go 测试模板

```go
package module_test

import (
    "testing"
    "github.com/stretchr/testify/assert"
    "module"
)

func TestFunctionName(t *testing.T) {
    t.Run("valid input", func(t *testing.T) {
        input := &Input{Field: "value"}
        result, err := module.FunctionName(input)

        assert.NoError(t, err)
        assert.Equal(t, "expected", result)
    })

    t.Run("invalid input", func(t *testing.T) {
        input := &Input{Field: ""}
        _, err := module.FunctionName(input)

        assert.Error(t, err)
        assert.Equal(t, module.ErrInvalidInput, err)
    })
}
```

---

## Phase 4: Quality Check (质量检查)

### 目标
验证生成的测试质量

### 检查清单

#### 测试覆盖率
- [ ] 核心函数 100% 覆盖
- [ ] 边界条件覆盖
- [ ] 错误路径覆盖

#### 测试质量
- [ ] 测试名称清晰
- [ ] AAA 结构一致
- [ ] Mock 使用恰当
- [ ] 无硬编码值
- [ ] 独立的测试用例

#### 代码规范
- [ ] 通过 linter
- [ ] 格式化一致
- [ ] 无 TODO/FIXME
- [ ] 适当的注释

---

## 测试数据设计

### Fixture 设计原则
1. **可复用** - 跨测试共享
2. **自包含** - 不依赖外部状态
3. **可读** - 清晰的命名
4. **最小化** - 只包含必要字段

### 测试数据示例

```typescript
// Valid User
const validUser = {
  id: 'usr_123',
  email: 'test@example.com',
  name: 'Test User',
  role: 'member',
  createdAt: new Date('2024-01-01'),
};

// Invalid User - Missing email
const invalidUserMissingEmail = {
  id: 'usr_123',
  name: 'Test User',
};

// Edge Case - Empty name
const userEmptyName = {
  id: 'usr_123',
  email: 'test@example.com',
  name: '',
};
```

---

## 边界条件测试

### 常见边界条件

| 类型 | 测试值 |
|------|--------|
| 空字符串 | `""`, `''`, `` |
| 空格 | `" "`, `"\t"`, `"\n"` |
| 零值 | `0`, `null`, `undefined` |
| 负数 | `-1`, `-999999` |
| 极大值 | `Number.MAX_VALUE` |
| 特殊字符 | `<script>`, `'; DROP TABLE` |
| 超长字符串 | `repeat('a', 10000)` |

---

## 错误消息测试

### 测试场景
1. 验证错误类型正确
2. 验证错误消息清晰
3. 验证错误码一致
4. 验证日志记录

```typescript
it('should provide clear error message', async () => {
  try {
    await riskyOperation();
    fail('Should have thrown');
  } catch (error) {
    expect(error).toBeInstanceOf(CustomError);
    expect(error.message).toBe('Operation failed: invalid input');
    expect(error.code).toBe('ERR_INVALID_INPUT');
  }
});
```