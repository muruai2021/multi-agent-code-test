# Python 测试生成指南

## 测试框架

推荐使用以下测试框架：
- **pytest** - 强大、简洁、广泛使用（推荐）
- **unittest** - 标准库，无需安装
- **pytest-asyncio** - 异步测试支持

## 配置

```ini
# pytest.ini 或 pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = "-v --tb=short --cov=src --cov-report=html"
asyncio_mode = "auto"
```

## 测试模板

### 基础测试

```python
import pytest
from module import function_name, ValidationError


class TestModuleFunction:
    """测试模块函数"""

    def test_function_with_valid_input(self):
        """有效输入应返回预期结果"""
        input_data = {"field": "value"}
        result = function_name(input_data)

        assert result.success is True
        assert result.data == "expected"

    def test_function_with_invalid_input(self):
        """无效输入应抛出 ValidationError"""
        input_data = {"field": ""}

        with pytest.raises(ValidationError):
            function_name(input_data)
```

### Fixture 使用

```python
import pytest
from datetime import datetime


@pytest.fixture
def valid_user():
    """有效的用户数据"""
    return {
        "id": "usr_123",
        "email": "test@example.com",
        "name": "Test User",
        "created_at": datetime(2024, 1, 1),
    }


@pytest.fixture
def invalid_user_missing_email():
    """缺少邮箱的用户数据"""
    return {
        "id": "usr_123",
        "name": "Test User",
    }


def test_create_user_success(valid_user):
    """成功创建用户"""
    result = create_user(valid_user)
    assert result.id == "usr_123"


def test_create_user_missing_email(invalid_user_missing_email):
    """缺少邮箱应失败"""
    with pytest.raises(ValidationError, match="email is required"):
        create_user(invalid_user_missing_email)
```

### 异步测试

```python
import pytest


@pytest.mark.asyncio
async def test_async_fetch_user():
    """异步获取用户"""
    user = await fetch_user("usr_123")
    assert user.id == "usr_123"


@pytest.mark.asyncio
async def test_async_fetch_not_found():
    """用户不存在应返回 None"""
    user = await fetch_user("nonexistent")
    assert user is None
```

### Mock 使用

```python
from unittest.mock import patch, Mock
import pytest


def test_external_api_call():
    """测试调用外部 API"""
    with patch('module.requests.get') as mock_get:
        mock_response = Mock()
        mock_response.json.return_value = {"data": "test"}
        mock_get.return_value = mock_response

        result = call_external_api()

        mock_get.assert_called_once()
        assert result == {"data": "test"}


def test_with_mock_fixture():
    """使用 mock fixture"""
    @pytest.fixture
    def mock_db():
        with patch('module.Database') as mock:
            yield mock.return_value

    def test_db_query(mock_db):
        mock_db.query.return_value = ["item1", "item2"]
        result = query_database()
        assert len(result) == 2
```

---

## Django 测试

### Model 测试

```python
from django.test import TestCase
from django.db import IntegrityError


class UserModelTest(TestCase):
    def test_create_user(self):
        """创建用户"""
        user = User.objects.create_user(
            email="test@example.com",
            password="securepass123",
            name="Test User"
        )
        self.assertEqual(user.email, "test@example.com")

    def test_duplicate_email(self):
        """重复邮箱应抛出异常"""
        User.objects.create_user(
            email="test@example.com",
            password="pass123"
        )
        with self.assertRaises(IntegrityError):
            User.objects.create_user(
                email="test@example.com",
                password="pass456"
            )
```

### View 测试

```python
from django.test import TestCase, Client
import json


class UserViewTest(TestCase):
    def setUp(self):
        self.client = Client()

    def test_create_user_api(self):
        """POST /api/users 创建用户"""
        response = self.client.post(
            '/api/users/',
            data=json.dumps({
                'email': 'new@example.com',
                'name': 'New User',
                'password': 'pass123'
            }),
            content_type='application/json'
        )
        self.assertEqual(response.status_code, 201)
        self.assertEqual(response.json()['email'], 'new@example.com')

    def test_invalid_email(self):
        """无效邮箱应返回 400"""
        response = self.client.post(
            '/api/users/',
            data=json.dumps({'email': 'invalid'}),
            content_type='application/json'
        )
        self.assertEqual(response.status_code, 400)
```

---

## FastAPI 测试

### 使用 TestClient

```python
from fastapi.test import TestClient
from main import app

client = TestClient(app)


def test_read_user():
    """GET /users/{user_id}"""
    response = client.get("/users/1")
    assert response.status_code == 200
    assert response.json()["id"] == 1


def test_create_user():
    """POST /users/"""
    response = client.post(
        "/users/",
        json={"email": "test@example.com", "name": "Test"}
    )
    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "test@example.com"
    assert "id" in data


def test_create_user_invalid():
    """无效数据应返回 422"""
    response = client.post(
        "/users/",
        json={"email": "invalid"}
    )
    assert response.status_code == 422
```

### 异步测试

```python
import pytest
from httpx import AsyncClient, ASGITransport
from main import app


@pytest.mark.asyncio
async def test_async_create_user():
    """异步创建用户"""
    transport = ASGITransport(app=app)
    async with AsyncClient(transport=transport, base_url="http://test") as client:
        response = await client.post(
            "/users/",
            json={"email": "test@example.com", "name": "Test"}
        )
        assert response.status_code == 201
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

### 参数化测试

```python
import pytest


@pytest.mark.parametrize("email,expected", [
    ("test@example.com", True),
    ("invalid", False),
    ("@example.com", False),
    ("test@", False),
])
def test_validate_email(email, expected):
    """邮箱验证参数化测试"""
    assert validate_email(email) == expected
```

### 上下文管理器测试

```python
def test_database_transaction():
    """测试数据库事务"""
    with db.transaction() as tx:
        user = User.create(name="Test")
        assert user.id is not None
        tx.rollback()  # 测试回滚


def test_resource_cleanup():
    """测试资源清理"""
    with open("file.txt", "w") as f:
        f.write("test")
    # 文件自动关闭
```

### Mock 属性

```python
from unittest.mock import PropertyMock


def test_cache_hit(mocker):
    """缓存命中测试"""
    mocker.patch.object(
        Cache,
        'get',
        return_value={'data': 'cached'}
    )
    result = get_data(use_cache=True)
    assert result == {'data': 'cached'}
```

---

## 边界条件测试

```python
def test_empty_string():
    """空字符串"""
    assert validate_input("") is False


def test_whitespace_string():
    """空白字符串"""
    assert validate_input("   ") is False


def test_none_value():
    """None 值"""
    with pytest.raises(ValidationError):
        validate_input(None)


def test_negative_number():
    """负数"""
    assert calculate(-1) == 0  # 或抛出异常


def test_very_large_number():
    """超大数"""
    assert calculate(10**10) is not None  # 不崩溃


def test_special_characters():
    """特殊字符"""
    assert sanitize("<script>") == "&lt;script&gt;"
```