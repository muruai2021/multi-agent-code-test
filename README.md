# multi-agent-code-test

[English](#english) | [中文](#中文)

---

## English

### Overview

**multi-agent-code-test** is an intelligent multi-agent collaborative code testing system designed for AI coding assistants and development teams. It leverages a parallel agent architecture where specialized test generators work simultaneously to provide comprehensive test coverage across unit tests, integration tests, E2E tests, security tests, and performance tests.

### Key Features

#### Multi-Agent Parallel Test Generation
- **Test Commander**: Task coordination and report aggregation
- **Unit Test Generator**: Function/method unit tests
- **Integration Test Generator**: API and module integration tests
- **E2E Test Generator**: End-to-end user journey tests
- **Security Test Generator**: Vulnerability and injection tests
- **Performance Test Generator**: Load and stress tests
- **Coverage Analyzer**: Coverage analysis and gap identification

#### 4-Phase Test Generation Process
1. **Context Analysis** - Understand code structure and dependencies
2. **Test Planning** - Design test strategy and prioritization
3. **Test Generation** - Generate test code in parallel
4. **Quality Check** - Validate test quality and coverage

#### Dual Output Format
- **Markdown**: Documentation, easy to share
- **HTML**: Embedded reports, email-friendly, print-ready

#### Priority Tagging System
| Tag | Meaning | Priority |
|-----|---------|----------|
| 🔴 [critical] | Core functionality, must pass | P0 |
| 🟡 [important] | Important functionality | P1 |
| 🟢 [standard] | Standard functionality | P2 |
| 🎯 [edge-case] | Edge case test | - |
| 🔒 [security] | Security test | P0 |
| ⚡ [performance] | Performance test | P1 |

### Supported Languages

| Language | Testing Framework |
|----------|-------------------|
| TypeScript | Vitest, Jest, Mocha |
| Python | pytest, unittest |
| Go | testing, testify, ginkgo |

### Usage

**Activate via command:**
```
/generate-tests <files or module>
```

**Activate via description:**
```
"帮我生成单元测试"
"generate tests for this module"
"为这个函数编写测试"
```

**Output format options:**
```
generate-tests src/**/*.ts --format markdown    # Default
generate-tests src/**/*.ts --format html        # HTML report
```

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                       Test Commander                         │
│                  (Task Coordination & Reporting)              │
└───────────────────────────┬─────────────────────────────────┘
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
  ┌────────────┐     ┌────────────┐     ┌────────────┐
  │    Unit    │     │Integration │     │    E2E     │
  │    Test    │     │    Test    │     │    Test    │
  │  Generator │     │  Generator │     │  Generator │
  └────────────┘     └────────────┘     └────────────┘
         │                  │                  │
         ▼                  ▼                  ▼
  ┌────────────┐     ┌────────────┐     ┌────────────┐
  │  Security  │     │Performance │     │ Coverage   │
  │    Test    │     │    Test    │     │  Analyzer  │
  │  Generator │     │  Generator │     │            │
  └────────────┘     └────────────┘     └────────────┘
```

### Project Structure

```
multi-agent-code-test/
├── SKILL.md                      # Main entry point
├── README.md                     # This file
├── test_pool.md                  # Test cases
└── references/
    ├── agents.md                 # Agent definitions
    ├── pipeline.md               # Collaboration workflow
    ├── test-phases.md           # 4-phase test process
    ├── test-tags.md             # Tagging system
    ├── output-template.md       # Markdown & HTML templates
    └── languages/
        ├── typescript.md         # TypeScript testing guide
        ├── python.md             # Python testing guide
        └── go.md                 # Go testing guide
```

---

## 中文

### 概述

**multi-agent-code-test** 是一款智能多智能体协作代码测试系统，专为 AI 编程助手和开发团队设计。它采用并行智能体架构，让专业测试生成器同时工作，从单元测试、集成测试、E2E 测试、安全测试、性能测试等多个维度提供全面的测试覆盖。

### 核心功能

#### 多智能体并行测试生成
- **主编 (Test Commander)**: 任务协调与报告汇总
- **单元测试生成员 (Unit Test Generator)**: 函数/方法的单元测试
- **集成测试生成员 (Integration Test Generator)**: API 和模块集成测试
- **E2E 测试生成员 (E2E Test Generator)**: 端到端用户旅程测试
- **安全测试生成员 (Security Test Generator)**: 漏洞和注入测试
- **性能测试生成员 (Performance Test Generator)**: 负载和压力测试
- **覆盖率分析员 (Coverage Analyzer)**: 覆盖率分析与缺口识别

#### 四阶段测试生成流程
1. **上下文分析** - 理解代码结构和依赖
2. **测试规划** - 设计测试策略和优先级
3. **测试生成** - 并行生成测试代码
4. **质量检查** - 验证测试质量和覆盖率

#### 双输出格式
- **Markdown**: 文档、易于分享
- **HTML**: 内嵌报告、邮件友好、打印就绪

#### 优先级标签系统
| 标签 | 含义 | 优先级 |
|------|------|--------|
| 🔴 [critical] | 核心功能，必须通过 | P0 |
| 🟡 [important] | 重要功能 | P1 |
| 🟢 [standard] | 标准功能 | P2 |
| 🎯 [edge-case] | 边界用例 | - |
| 🔒 [security] | 安全测试 | P0 |
| ⚡ [performance] | 性能测试 | P1 |

### 支持的语言和测试框架

| 语言 | 测试框架 |
|------|----------|
| TypeScript | Vitest, Jest, Mocha |
| Python | pytest, unittest |
| Go | testing, testify, ginkgo |

### 使用方式

**通过命令激活:**
```
/generate-tests <文件或模块>
```

**通过描述激活:**
```
"帮我生成单元测试"
"generate tests for this module"
"为这个函数编写测试"
```

**输出格式选项:**
```
generate-tests src/**/*.ts --format markdown    # 默认 Markdown
generate-tests src/**/*.ts --format html        # HTML 报告
```

### 系统架构

```
┌─────────────────────────────────────────────────────────────┐
│                       测试主编 (Test Commander)              │
│                    (任务协调与报告汇总)                         │
└───────────────────────────┬─────────────────────────────────┘
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
  ┌────────────┐     ┌────────────┐     ┌────────────┐
  │   单元     │     │   集成     │     │   E2E      │
  │   测试     │     │   测试     │     │   测试     │
  │   生成员   │     │   生成员   │     │   生成员   │
  └────────────┘     └────────────┘     └────────────┘
         │                  │                  │
         ▼                  ▼                  ▼
  ┌────────────┐     ┌────────────┐     ┌────────────┐
  │   安全     │     │   性能     │     │   覆盖率   │
  │   测试     │     │   测试     │     │   分析员   │
  │   生成员   │     │   生成员   │     │            │
  └────────────┘     └────────────┘     └────────────┘
```

### 项目结构

```
multi-agent-code-test/
├── SKILL.md                      # 主入口
├── README.md                     # 项目文档
├── test_pool.md                  # 测试用例池
└── references/
    ├── agents.md                 # 智能体定义
    ├── pipeline.md               # 协作流程
    ├── test-phases.md           # 四阶段测试流程
    ├── test-tags.md             # 标签系统
    ├── output-template.md       # 输出模板
    └── languages/
        ├── typescript.md         # TypeScript 测试指南
        ├── python.md             # Python 测试指南
        └── go.md                 # Go 测试指南
```

---

## License

MIT