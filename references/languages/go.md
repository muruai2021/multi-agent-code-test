# Go 测试生成指南

## 测试框架

Go 标准库自带 `testing` 包，推荐配合以下工具：
- **testify** - 断言和 mock 库（推荐）
- **ginkgo/gomega** - BDD 风格
- **gomock** - Mock 生成

## 配置

```bash
# 安装测试依赖
go get github.com/stretchr/testify
go install github.com/stretchr/testify/mock@latest
```

## 测试模板

### 基础测试

```go
package module_test

import (
    "testing"
    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
    "module"
)

func TestFunctionName(t *testing.T) {
    t.Run("valid input", func(t *testing.T) {
        input := &module.Input{Field: "value"}
        result, err := module.FunctionName(input)

        // 使用 require（失败时立即停止）
        require.NoError(t, err)
        assert.Equal(t, "expected", result)
    })

    t.Run("invalid input", func(t *testing.T) {
        input := &module.Input{Field: ""}
        _, err := module.FunctionName(input)

        require.Error(t, err)
        assert.Equal(t, module.ErrInvalidInput, err)
    })
}
```

### Table-Driven Tests

```go
func TestValidateEmail(t *testing.T) {
    testCases := []struct {
        name     string
        email    string
        wantErr  bool
    }{
        {"valid email", "test@example.com", false},
        {"invalid - no @", "testexample.com", true},
        {"invalid - no domain", "test@", true},
        {"invalid - empty", "", true},
    }

    for _, tc := range testCases {
        t.Run(tc.name, func(t *testing.T) {
            err := ValidateEmail(tc.email)
            if tc.wantErr {
                assert.Error(t, err)
            } else {
                assert.NoError(t, err)
            }
        })
    }
}
```

### Mock 使用

```go
package module_test

import (
    "testing"
    "github.com/stretchr/testify/mock"
)

// MockDatabase 模拟数据库
type MockDatabase struct {
    mock.Mock
}

func (m *MockDatabase) GetUser(id string) (*User, error) {
    args := m.Called(id)
    if args.Get(0) == nil {
        return nil, args.Error(1)
    }
    return args.Get(0).(*User), args.Error(1)
}

func TestGetUser(t *testing.T) {
    db := new(MockDatabase)

    // 设置期望
    expectedUser := &User{ID: "123", Name: "Test"}
    db.On("GetUser", "123").Return(expectedUser, nil)

    // 执行测试
    user, err := GetUser(db, "123")

    // 验证
    assert.NoError(t, err)
    assert.Equal(t, expectedUser, user)
    db.AssertExpectations(t)
}
```

---

## HTTP Handler 测试

### 使用 net/http/httptest

```go
package handler_test

import (
    "encoding/json"
    "net/http"
    "net/http/httptest"
    "strings"
    "testing"
    "github.com/stretchr/testify/assert"
    "handler"
)

func TestCreateUser(t *testing.T) {
    t.Run("valid request", func(t *testing.T) {
        body := `{"email":"test@example.com","name":"Test"}`
        req := httptest.NewRequest(
            http.MethodPost,
            "/api/users",
            strings.NewReader(body),
        )
        req.Header.Set("Content-Type", "application/json")

        rr := httptest.NewRecorder()
        handler.CreateUser(rr, req)

        assert.Equal(t, http.StatusCreated, rr.Code)

        var resp handler.UserResponse
        err := json.Unmarshal(rr.Body.Bytes(), &resp)
        assert.NoError(t, err)
        assert.Equal(t, "test@example.com", resp.Email)
    })

    t.Run("invalid email", func(t *testing.T) {
        body := `{"email":"invalid"}`
        req := httptest.NewRequest(
            http.MethodPost,
            "/api/users",
            strings.NewReader(body),
        )
        req.Header.Set("Content-Type", "application/json")

        rr := httptest.NewRecorder()
        handler.CreateUser(rr, req)

        assert.Equal(t, http.StatusBadRequest, rr.Code)
    })
}
```

---

## 数据库测试

### 使用 sqlmock

```go
package db_test

import (
    "database/sql"
    "testing"
    "github.com/DATA-DOG/go-sqlmock"
    "github.com/stretchr/testify/assert"
)

func TestGetUserByID(t *testing.T) {
    db, mock, _ := sqlmock.New()
    defer db.Close()

    rows := sqlmock.NewRows([]string{"id", "name", "email"}).
        AddRow("1", "Test User", "test@example.com")

    mock.ExpectQuery("SELECT id, name, email FROM users WHERE id = ?").
        WithArgs("1").
        WillReturnRows(rows)

    user, err := GetUserByID(db, "1")

    assert.NoError(t, err)
    assert.Equal(t, "Test User", user.Name)
    assert.NoError(t, mock.ExpectationsWereMet())
}
```

---

## 并发测试

```go
func TestConcurrentAccess(t *testing.T) {
    // Run parallel tests
    t.Run("parallel reads", func(t *testing.T) {
        t.Parallel()
        // test code
    })

    // Test for race conditions
    // Run with: go test -race
}
```

### 竞态检测

```bash
go test -race -count=1 ./...
```

---

## 性能测试

```go
func BenchmarkFibonacci(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Fibonacci(20)
    }
}

func BenchmarkParallel(b *testing.B) {
    b.RunParallel(func(pb *testing.PB) {
        for pb.Next() {
            // parallel work
        }
    })
}
```

---

## 覆盖率目标

| 类型 | 目标 | 说明 |
|------|------|------|
| Statements | 70% | 覆盖所有可执行语句 |
| Branches | 60% | 覆盖所有分支路径 |
| Functions | 80% | 覆盖所有函数 |
| Lines | 70% | 覆盖所有代码行 |

---

## 测试辅助函数

```go
// testdata 目录中的测试数据
import (
    "os"
    "path/filepath"
    "testing"
)

func loadTestData(t *testing.T, filename string) []byte {
    path := filepath.Join("testdata", filename)
    data, err := os.ReadFile(path)
    if err != nil {
        t.Fatalf("failed to read test data: %v", err)
    }
    return data
}

// 清理函数
func setup(t *testing.T) func() {
    // setup
    return func() {
        // teardown
    }
}

func TestWithSetup(t *testing.T) {
    cleanup := setup(t)
    defer cleanup()

    // test code
}
```

---

## 常见模式

### 错误类型测试

```go
func TestErrorTypes(t *testing.T) {
    tests := []struct {
        name     string
        input    string
        expected error
    }{
        {"empty", "", ErrEmptyInput},
        {"too long", strings.Repeat("a", 1000), ErrInputTooLong},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            _, err := Process(tt.input)
            assert.ErrorIs(t, err, tt.expected)
        })
    }
}
```

### Context 传播测试

```go
func TestWithContext(t *testing.T) {
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    defer cancel()

    result, err := DoWithContext(ctx, "task")

    if ctx.Err() == context.DeadlineExceeded {
        t.Skip("context deadline exceeded")
    }

    assert.NoError(t, err)
    assert.NotNil(t, result)
}
```