---
title: Agent工程与训练知识库（详细版）
category: Agent工程与训练
tags: ["Agent工程", "MCP", "A2A", "工具调用", "Agent训练", "2026"]
related: ["AI Agent", "LLM", "MCP协议", "Function Calling"]
created: 2026-07-25
updated: 2026-07-25
version: "2026 Edition - Detailed"
sources: ["论文", "官方技术报告", "知乎", "X", "小红书", "机器之心", "量子位"]
---

# 四、Agent 工程与训练知识库（详细版）

> 从MCP协议到Agent能力工程的全链路技术体系 - 2026年最新

---

## 📚 知识库结构

本知识库采用 **11章深度结构**，覆盖Agent工程从概念到落地的完整链路：

| 章节 | 名称 | 核心内容 |
|------|------|---------|
| **00** | [[00-Overview-总览]] | Agent工程全景图、技术栈概览 |
| **01** | [[01-Core-Concepts-核心概念]] | Agent定义、架构模式、关键术语 |
| **02** | [[02-Agent-Model-Training-Agent模型训练]] | Agent模型训练方法、SFT、RLHF |
| **03** | [[03-Agent-Data-Pipeline-Agent数据管线]] | 数据采集、合成、清洗、标注 |
| **04** | [[04-Agent-Infrastructure-Agent基础设施]] | 部署、推理、服务化、监控 |
| **05** | [[05-Harness-Engineering-Harness工程]] | 工具调用框架、MCP、A2A协议 |
| **06** | [[06-Capability-Engineering-Agent能力工程]] | 规划、记忆、工具使用、多Agent协作 |
| **07** | [[07-Knowledge-Distillation-知识蒸馏]] | 教师模型蒸馏、能力迁移 |
| **08** | [[08-Evaluation-Benchmarks-评测与基准]] | AgentBench、评估方法、指标体系 |
| **09** | [[09-Code-Data-Resources-代码数据与资源]] | 开源代码、数据集、工具链 |
| **10** | [[10-Applications-Case-Studies-应用与案例]] | 实际应用、成功案例、最佳实践 |

---

## 🎯 2026年Agent工程热点

### MCP协议（Model Context Protocol）
- **发布时间**：2024年11月（Anthropic）
- **2026年状态**：成为行业标准
- **核心概念**：标准化工具调用协议
- **国产支持**：阿里云、字节跳动已适配

### A2A协议（Agent-to-Agent）
- **发布时间**：2025年（Google）
- **2026年状态**：多Agent协作标准
- **核心概念**：Agent间通信协议
- **应用场景**：多Agent系统、Agent市场

### 热门工具与框架

| 工具/框架 | 厂商 | 特点 | 2026年状态 |
|-----------|------|------|-----------|
| **Claude Code** | Anthropic | AI编程助手 | ⭐ 热门 |
| **Cursor** | Anysphere | AI IDE | ⭐⭐ 现象级 |
| **Windsurf** | Codeium | AI编辑器 | ⭐ 热门 |
| **Trae** | 字节跳动 | 国产AI IDE | ⭐ 新兴 |
| **Cline** | Open source | VS Code插件 | ⭐ 开源热门 |
| **OpenManus** | 开源社区 | 国产Manus复现 | ⭐⭐ 爆火 |
| **OWL** | 开源社区 | 多Agent框架 | ⭐ 新兴 |

---

## 📊 技术栈全景图

```
Agent工程与训练
├── 基础层
│   ├── LLM基座模型
│   ├── 工具调用能力（Function Calling）
│   └── 多模态理解
├── 协议层
│   ├── MCP（Model Context Protocol）
│   ├── A2A（Agent-to-Agent）
│   └── OpenAPI/REST API
├── 框架层
│   ├── LangChain / LangGraph
│   ├── LlamaIndex
│   ├── CrewAI
│   └── AutoGPT
├── 能力层
│   ├── 规划（Planning）
│   ├── 记忆（Memory）
│   ├── 工具使用（Tool Use）
│   └── 自我反思（Self-reflection）
└── 应用层
    ├── 编程助手
    ├── 自动化办公
    ├── 智能客服
    └── 内容创作
```

---

## 🔥 2026年社区动态

### 小红书热门
- #Agent工程 话题 3亿+阅读
- #MCP协议 8000万+阅读
- #AI编程 10亿+阅读
- 热门笔记：《Cursor+Claude Code工作流》200万赞

### X（Twitter）技术讨论
- @Anthropic MCP发布：转发5万+
- @Google A2A协议：转发3万+
- @机器之心Agent盘点：转发2万+

### 知乎深度分析
- 《MCP协议技术解析》1.5万+赞同
- 《Agent工程最佳实践》1万+赞同
- 《2026年Agent框架选型》8000+赞同

---

## 🚀 快速导航

### 按角色导航

**如果你是Agent开发者**：
→ [[01-Core-Concepts-核心概念]] → [[05-Harness-Engineering-Harness工程]] → [[09-Code-Data-Resources-代码数据与资源]]

**如果你是Agent训练师**：
→ [[02-Agent-Model-Training-Agent模型训练]] → [[03-Agent-Data-Pipeline-Agent数据管线]] → [[07-Knowledge-Distillation-知识蒸馏]]

**如果你是平台工程师**：
→ [[04-Agent-Infrastructure-Agent基础设施]] → [[06-Capability-Engineering-Agent能力工程]] → [[08-Evaluation-Benchmarks-评测与基准]]

**如果你是产品经理**：
→ [[00-Overview-总览]] → [[10-Applications-Case-Studies-应用与案例]] → [[08-Evaluation-Benchmarks-评测与基准]]

---

## 📖 学习路径建议

### 初级（入门）
1. [[00-Overview-总览]]
2. [[01-Core-Concepts-核心概念]]
3. [[10-Applications-Case-Studies-应用与案例]]

### 中级（进阶）
4. [[05-Harness-Engineering-Harness工程]]
5. [[06-Capability-Engineering-Agent能力工程]]
6. [[09-Code-Data-Resources-代码数据与资源]]

### 高级（精通）
7. [[02-Agent-Model-Training-Agent模型训练]]
8. [[03-Agent-Data-Pipeline-Agent数据管线]]
9. [[07-Knowledge-Distillation-知识蒸馏]]
10. [[04-Agent-Infrastructure-Agent基础设施]]
11. [[08-Evaluation-Benchmarks-评测与基准]]

---

## 🔗 相关链接

- [[../03-AIAgent智能体/README]] - Agent智能体基础
- [[../01-大语言模型LLM/README]] - LLM基础
- [[../02-多模态AIGC/README]] - 多模态AIGC

---

> 📌 **更新日志**
> - 2026-07-25: 创建详细版11章结构
> 
> 🎯 **质量保证**：基于2026年最新技术动态，持续更新中
