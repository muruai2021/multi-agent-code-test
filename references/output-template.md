# 测试报告模板

## Markdown 格式

```markdown
# Test Generation Report

## Summary
- **Files Analyzed**: N
- **Tests Generated**: N
- **Critical Tests**: N
- **Coverage Target**: 80%
- **Actual Coverage**: 75%

## Unit Tests

[生成的单元测试]

## Integration Tests

[生成的集成测试]

## E2E Tests

[生成的 E2E 测试]

## Security Tests

[生成的安全测试]

## Test Coverage

| Metric | Current | Target | Status |
|--------|---------|--------|--------|
| Statements | 75% | 80% | ⚠️ |
| Branches | 68% | 70% | ⚠️ |
| Functions | 85% | 90% | ✅ |
| Lines | 75% | 80% | ⚠️ |

## Recommendations

[下一步建议]
```

---

## HTML 格式

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Test Generation Report</title>
    <style>
        :root {
            --bg-primary: #ffffff;
            --bg-secondary: #f6f8fa;
            --bg-tertiary: #f0f0f0;
            --text-primary: #24292f;
            --text-secondary: #57606a;
            --border-color: #d0d7de;
            --critical: #d1242f;
            --important: #bf8700;
            --standard: #1a7f37;
            --suggestion: #8250df;
            --edge-case: #0550ae;
            --security: #cf222e;
            --performance: #bf8700;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
            line-height: 1.6;
            color: var(--text-primary);
            background: var(--bg-primary);
            padding: 2rem;
            max-width: 1200px;
            margin: 0 auto;
        }

        .header {
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 1rem;
            margin-bottom: 2rem;
        }

        .title {
            font-size: 1.75rem;
            font-weight: 600;
            margin-bottom: 0.5rem;
        }

        .meta {
            color: var(--text-secondary);
            font-size: 0.875rem;
        }

        .summary {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 1rem;
            margin-bottom: 2rem;
            padding: 1rem;
            background: var(--bg-secondary);
            border-radius: 6px;
        }

        .summary-item {
            display: flex;
            flex-direction: column;
        }

        .summary-label {
            font-size: 0.75rem;
            text-transform: uppercase;
            color: var(--text-secondary);
            margin-bottom: 0.25rem;
        }

        .summary-value {
            font-size: 1.25rem;
            font-weight: 600;
        }

        .summary-value.critical { color: var(--critical); }
        .summary-value.important { color: var(--important); }
        .summary-value.standard { color: var(--standard); }

        .section {
            margin-bottom: 2rem;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            overflow: hidden;
        }

        .section-header {
            background: var(--bg-secondary);
            padding: 0.75rem 1rem;
            font-weight: 600;
            border-bottom: 1px solid var(--border-color);
        }

        .section-body {
            padding: 1rem;
        }

        .test-tag {
            display: inline-flex;
            align-items: center;
            padding: 0.125rem 0.5rem;
            border-radius: 12px;
            font-size: 0.75rem;
            font-weight: 500;
            margin-right: 0.5rem;
        }

        .test-tag.critical {
            background: #ffeef0;
            color: var(--critical);
        }

        .test-tag.important {
            background: #fff8c5;
            color: var(--important);
        }

        .test-tag.standard {
            background: #dafbe1;
            color: var(--standard);
        }

        .test-tag.edge-case {
            background: #ddf4ff;
            color: var(--edge-case);
        }

        .test-tag.security {
            background: #ffebe9;
            color: var(--security);
        }

        .test-tag.performance {
            background: #fff8c5;
            color: var(--performance);
        }

        .test-item {
            padding: 1rem;
            border-bottom: 1px solid var(--border-color);
        }

        .test-item:last-child { border-bottom: none; }

        .test-header {
            display: flex;
            align-items: center;
            margin-bottom: 0.5rem;
        }

        .test-name {
            font-weight: 500;
            flex: 1;
        }

        .test-file {
            font-size: 0.875rem;
            color: var(--text-secondary);
        }

        .code-block {
            background: var(--bg-tertiary);
            padding: 1rem;
            border-radius: 4px;
            font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
            font-size: 0.875rem;
            overflow-x: auto;
            margin: 0.5rem 0;
        }

        .coverage-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1rem;
        }

        .coverage-item {
            background: var(--bg-secondary);
            padding: 1rem;
            border-radius: 6px;
        }

        .coverage-label {
            font-size: 0.875rem;
            color: var(--text-secondary);
            margin-bottom: 0.5rem;
        }

        .coverage-bar {
            height: 8px;
            background: var(--bg-tertiary);
            border-radius: 4px;
            overflow: hidden;
        }

        .coverage-fill {
            height: 100%;
            border-radius: 4px;
            transition: width 0.3s;
        }

        .coverage-fill.pass { background: var(--standard); }
        .coverage-fill.warning { background: var(--important); }
        .coverage-fill.fail { background: var(--critical); }

        .coverage-value {
            font-size: 1.5rem;
            font-weight: 600;
            margin-top: 0.5rem;
        }

        .status-badge {
            display: inline-flex;
            padding: 0.25rem 0.75rem;
            border-radius: 12px;
            font-size: 0.875rem;
            font-weight: 500;
        }

        .status-badge.pass {
            background: #dafbe1;
            color: var(--standard);
        }

        .status-badge.warning {
            background: #fff8c5;
            color: var(--important);
        }

        .status-badge.fail {
            background: #ffeef0;
            color: var(--critical);
        }

        @media (max-width: 768px) {
            body { padding: 1rem; }
            .summary { grid-template-columns: repeat(2, 1fr); }
        }
    </style>
</head>
<body>
    <div class="header">
        <h1 class="title">Test Generation Report</h1>
        <div class="meta">
            <span>Generated: </span><strong>2026-05-23 14:30</strong>
            <span> | </span>
            <span>Language: </span><strong>TypeScript</strong>
        </div>
    </div>

    <div class="summary">
        <div class="summary-item">
            <span class="summary-label">Files Analyzed</span>
            <span class="summary-value">8</span>
        </div>
        <div class="summary-item">
            <span class="summary-label">Tests Generated</span>
            <span class="summary-value">42</span>
        </div>
        <div class="summary-item">
            <span class="summary-label">
                <span class="test-tag critical">critical</span>
            </span>
            <span class="summary-value critical">5</span>
        </div>
        <div class="summary-item">
            <span class="summary-label">
                <span class="test-tag important">important</span>
            </span>
            <span class="summary-value important">12</span>
        </div>
        <div class="summary-item">
            <span class="summary-label">
                <span class="test-tag standard">standard</span>
            </span>
            <span class="summary-value standard">25</span>
        </div>
    </div>

    <div class="section">
        <div class="section-header">📊 Coverage Report</div>
        <div class="section-body">
            <div class="coverage-grid">
                <div class="coverage-item">
                    <div class="coverage-label">Statements</div>
                    <div class="coverage-bar">
                        <div class="coverage-fill pass" style="width: 75%"></div>
                    </div>
                    <div class="coverage-value">75%</div>
                    <span class="status-badge warning">Target: 80%</span>
                </div>
                <div class="coverage-item">
                    <div class="coverage-label">Branches</div>
                    <div class="coverage-bar">
                        <div class="coverage-fill warning" style="width: 68%"></div>
                    </div>
                    <div class="coverage-value">68%</div>
                    <span class="status-badge warning">Target: 70%</span>
                </div>
                <div class="coverage-item">
                    <div class="coverage-label">Functions</div>
                    <div class="coverage-bar">
                        <div class="coverage-fill pass" style="width: 85%"></div>
                    </div>
                    <div class="coverage-value">85%</div>
                    <span class="status-badge pass">Target: 90%</span>
                </div>
                <div class="coverage-item">
                    <div class="coverage-label">Lines</div>
                    <div class="coverage-bar">
                        <div class="coverage-fill pass" style="width: 75%"></div>
                    </div>
                    <div class="coverage-value">75%</div>
                    <span class="status-badge warning">Target: 80%</span>
                </div>
            </div>
        </div>
    </div>

    <div class="section">
        <div class="section-header">🔬 Unit Tests</div>
        <div class="section-body">
            <div class="test-item">
                <div class="test-header">
                    <span class="test-tag critical">🔴 critical</span>
                    <span class="test-name">should create user with valid data</span>
                    <span class="test-file">user.test.ts</span>
                </div>
                <div class="code-block">it('should create user with valid data', async () => {
  const user = await userService.create({
    email: 'test@example.com',
    name: 'Test'
  });
  expect(user.id).toBeDefined();
});</div>
            </div>
            <div class="test-item">
                <div class="test-header">
                    <span class="test-tag important">🟡 important</span>
                    <span class="test-name">should throw on invalid email</span>
                    <span class="test-file">user.test.ts</span>
                </div>
                <div class="code-block">it('should throw ValidationError for invalid email', async () => {
  await expect(userService.create({
    email: 'invalid'
  })).rejects.toThrow(ValidationError);
});</div>
            </div>
        </div>
    </div>

    <div class="section">
        <div class="section-header">🔒 Security Tests</div>
        <div class="section-body">
            <div class="test-item">
                <div class="test-header">
                    <span class="test-tag security">🔒 security</span>
                    <span class="test-name">should prevent SQL injection</span>
                    <span class="test-file">security.test.ts</span>
                </div>
                <div class="code-block">it('should sanitize SQL injection attempts', async () => {
  const result = await searchUsers("'; DROP TABLE users; --");
  expect(result).toEqual([]);
});</div>
            </div>
        </div>
    </div>

    <div class="section">
        <div class="section-header">💡 Recommendations</div>
        <div class="section-body">
            <ul>
                <li>Add edge case tests for <strong>empty string</strong> and <strong>null</strong> inputs</li>
                <li>Increase branch coverage to 70% by adding tests for error paths</li>
                <li>Consider adding property-based tests for validation functions</li>
            </ul>
        </div>
    </div>
</body>
</html>
```

---

## 输出格式选择

| 格式 | 适用场景 | 触发关键词 |
|------|----------|-----------|
| Markdown | 文档、GitHub 评论 | `--markdown` / `-m` |
| HTML | 报告展示、邮件发送 | `--html` / `-h` |

### 使用示例

```
generate-tests src/**/*.ts --format html > report.html
generate-tests src/**/*.ts --output html > test-report.html
```