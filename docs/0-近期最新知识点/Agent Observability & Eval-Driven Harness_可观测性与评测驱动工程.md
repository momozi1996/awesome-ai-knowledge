---
title: Agent 可观测性与评测驱动工程（Eval-Driven Harness）：生产级 AI Agent 的质量与运维闭环
category: 大模型应用开发 / AI 工程化
tags: ["Agent Observability", "Eval-Driven Harness", "LLM-as-a-Judge", "Tracing", "OpenTelemetry", "AI 运维"]
related: ["graph-engineering-guide", "a2a-protocol-coordination-engineering", "llm-context-engineering"]
weight: 4
---


# Agent 可观测性与评测驱动工程（Eval-Driven Harness）：生产级 AI Agent 的质量与运维闭环

将大语言模型（LLM）从 Demo 推向生产环境的过程中，最大的挑战莫过于其**非确定性（Non-deterministic）**与**黑盒执行路径**。传统的 APM（应用性能监控，如 Datadog、Prometheus）主要针对确定性 API 的 HTTP 状态码、CPU/内存利用率进行监控，完全无法回答以下问题：

* *为什么 Agent 在第 4 步调用了错误的 API？*
* *模型版本的静默升级，是否导致了业务流程的隐蔽退化？*
* *如何在不耗费大量人工的前提下，实时拦截 Agent 的幻觉输出？*

为了解决上述难题，**Agent 可观测性（Agent Observability）** 与 **评测驱动工程（Eval-Driven Harness）** 应运而生。它们通过在 Agent 执行环境外围搭建全链路 Trace 追踪与自动化 Eval 断言，打造生产级 Agent 的质量与运维闭环。

---

## 一、 Agent 可观测性的三大工程支柱

标准的 Agent 可观测性架构由三大支柱构成：

```text
                           ┌─────────────────────────────────┐
                           │   Agent Trace (链路追踪树)      │
                           └────────────────┬────────────────┘
                                            │
               ┌────────────────────────────┴────────────────────────────┐
               ▼                                                         ▼
┌──────────────────────────────┐                         ┌──────────────────────────────┐
│  State & Context Mutation    │                         │  Real-time Eval Guardrails   │
│  (状态变迁与 Token 监控)     │                         │  (在线评测与断言护栏)        │
└──────────────────────────────┘                         └──────────────────────────────┘
```

### 1. 轨迹链路追踪（Agent Trace Tree）
不同于传统单次 HTTP 请求，一个 Agent 任务通常包含多轮思考（CoT）、工具调用、条件分支与重试。
* **Span 级粒度**：Trace 树必须精准记录：
  * **LLM Call Span**：输入 Prompt、System Message、模型返回结果、Token 消费（Input/Output/Cached）、首包延迟（TTFT）。
  * **Tool Call Span**：调用的工具名称、入参、工具执行返回值、报错信息。
  * **State Mutation Span**：当前节点对全局 State 做了哪些修改。

### 2. 上下文与状态变迁日志（Context Mutation Metrics）
监控 Agent 在长期运行中的上下文“健康度”：
* **Token 增长速率**：检测上下文是否存在爆炸式增长。
* **Context Rot（上下文腐烂度）**：检测工具返回的超长结果（如 100KB 的 HTML）是否占满了有限的上下文窗口。

### 3. 线上实时评测护栏（Online Eval Guardrails）
在数据落盘或返回给前端前，实时触发低延迟的 Evaluation 方针（断言），检测回答中是否存在：**安全合规越界、幻觉、格式失效或偏离原始意图**。

---

## 二、 Eval-Driven Harness（评测驱动支撑环境）的核心架构

**Eval-Driven Harness** 的核心理念是：**将评测（Evals）直接作为控制逻辑编入 Agent 的执行支撑环境中，实现“评测-检测-降级-自动重试”的实时闭环。**

```text
Agent 尝试输出 ──> [ Online Eval Harness ] ──> 判定合格 ──> 输出给用户
                          │
                   判定不合格 (如: 发现幻觉)
                          │
                          ▼
                 [ 自动注入反馈并强制重试 ]
```

### 1. 双轨评测机制（Online vs Offline Evals）

* **在线评测（Online Evals / Guardrails）**：
  * **运行节点**：部署在生产环境的响应路径上。
  * **要求**：超低延迟（<100ms），通常使用正则、确定性代码、小型轻量级分类模型（如 DeBERTa）或快速 LLM 实施。
  * **作用**：实时拦截阻断严重错误。
* **离线基准评测（Offline Evals / Regression Pipeline）**：
  * **运行节点**：CI/CD 流水线或定时后台任务。
  * **方法**：抽取生产 Trace 聚类生成的黄金数据集（Gold Dataset），使用强模型（如 GPT-4o / Claude 3.5 Sonnet）作为裁判（LLM-as-a-Judge）进行多维度深度评分。
  * **作用**：防止 Prompt 或代码修改引发系统退化。

### 2. LLM-as-a-Judge 的校准工程

在离线评测中，直接使用 LLM 进行打分容易产生**位置偏见（Position Bias）**、**自满偏见（Self-Enhancement Bias）**与**分数漂移**。工程实践中须进行如下校准：

1. **G-Eval 准则显式化**：必须向 Judge 模型提供极为具体的评分 Rubric（评分表），拆解为 1-5 分的具体判定标准，而非模糊地问“好不好”。
2. **Pairwise Comparison（成对双盲对比）**：同时向 Judge 投喂基线版本（A）与新版本（B）的输出，颠倒 A/B 位置打分两次取交集，消除位置偏见。

---

## 三、 代码实现：带 OpenTelemetry 追踪与在线评测护栏的 Harness

以下展示如何使用 Python 构建一个集成了 **Trace 收集** 与 **在线评测护栏（Eval Harness）** 的 Agent 执行环境：

```python
import time
from typing import Dict, Any, Callable
from pydantic import BaseModel

# 1. 定义 Trace 链路节点结构 (符合 OpenTelemetry 规范思想)
class TraceSpan(BaseModel):
    span_id: str
    parent_id: str | None = None
    name: str
    attributes: Dict[str, Any]
    latency_ms: float
    status: str = "OK"

# 2. 定义在线评测结果结构
class EvalResult(BaseModel):
    passed: bool
    score: float
    reason: str

# 3. 在线评测器：检查输出中是否存在硬编码幻觉或非法字符
def hallucination_evaluator(output_text: str, context: str) -> EvalResult:
    """确定性/轻量在线断言节点"""
    if "UNKNOWN_ERR" in output_text or len(output_text.strip()) == 0:
        return EvalResult(passed=False, score=0.0, reason="Empty or Error output detected")
    # 模拟简单的覆盖度检查
    return EvalResult(passed=True, score=1.0, reason="Passed sanity check")

# 4. Agent Eval-Driven Harness (支撑环境)
class AgentEvalHarness:
    def __init__(self, evaluators: list[Callable]):
        self.evaluators = evaluators
        self.trace_store: list[TraceSpan] = []

    def run_agent_step_with_eval(self, step_name: str, agent_fn: Callable, *args, **kwargs) -> str:
        start_time = time.time()
        span_id = f"span_{len(self.trace_store) + 1}"
        
        # 1. 执行 Agent 步骤逻辑
        raw_output = agent_fn(*args, **kwargs)
        duration = (time.time() - start_time) * 1000

        # 2. 运行在线 Eval 检查 (Guardrails)
        eval_passed = True
        eval_reasons = []
        for eval_fn in self.evaluators:
            res: EvalResult = eval_fn(raw_output, kwargs.get("context", ""))
            if not res.passed:
                eval_passed = False
                eval_reasons.append(res.reason)

        # 3. 记录全链路 Trace Span
        span = TraceSpan(
            span_id=span_id,
            name=step_name,
            attributes={
                "input": str(args),
                "output": raw_output,
                "eval_passed": eval_passed,
                "eval_reasons": eval_reasons
            },
            latency_ms=duration,
            status="OK" if eval_passed else "EVAL_FAILED"
        )
        self.trace_store.append(span)

        # 4. 根据 Eval 结果控制阻断或抛出
        if not eval_passed:
            print(f"[Harness Intercepted] Step '{step_name}' failed evals: {eval_reasons}")
            # 此处可触发自动重试逻辑 (Self-Correction Loop)
            return f"CORRECTION_REQUIRED: {', '.join(eval_reasons)}"
            
        return raw_output

# --- 使用示例 ---
def mock_agent_logic(user_query: str) -> str:
    # 模拟 Agent 生成的包含错误的文本
    return "UNKNOWN_ERR: Failed to query database."

harness = AgentEvalHarness(evaluators=[hallucination_evaluator])

# 执行带有 Eval 监控的步骤
output = harness.run_agent_step_with_eval(
    step_name="SQL_Generation",
    agent_fn=mock_agent_logic,
    user_query="Show total sales"
)

# 查看录制的 Trace 链路
print("\nRecorded Trace Spans:")
print(harness.trace_store[0].model_dump_json(indent=2))
```

---

## 四、 工程落地避坑指南（Engineering Pitfalls）

### 1. 警惕“双重幻觉”陷阱（Judge Hallucination）
* **现象**：使用未经校准的 LLM 去评测业务 LLM，结果裁判模型自己产生了幻觉，误判了大量的正确结果。
* **解法**：在线断言优先使用**确定性代码、正则表达式、JSON Schema 校验与轻量级分类器**；只有在无结构化语义理解场景下，才引入小参数量模型/强模型进行辅助评估。

### 2. Trace 数据爆炸与 PII 隐私泄露
* **现象**：记录完整的 Prompt 和 Tool 返回值导致日志存储成本暴涨，同时无意间落盘了用户的身份证、银行卡等敏感数据（PII）。
* **解法**：在 Trace 发送至后端（如 Langfuse/Phoenix）前，强制配置 **PII Redaction Exporter（隐私脱敏导出器）**；对超过 1KB 的 Tool Output 进行哈希摘要或截断存储。

### 3. Goodhart 定律与评估打分衰减
* **现象**：开发人员过分针对离线 Eval Dataset 进行 Prompt 调优，导致 Agent 出现“过拟合”，生产环境中面对未见过的 Prompt 表现骤降。
* **解法**：建立**动态评测集更新机制**，每周从生产环境的边缘 Trace（Edge Cases）中自动提取 5% 到 10% 的真实数据清洗后加入评估库，保持评测集的高新鲜度。

---

## 五、 主流生态与工具选型矩阵

| 工具名称 | 类型 | 技术特点与优势 | 适用场景 |
| :--- | :--- | :--- | :--- |
| **Langfuse** | 开源 Agent 可观测平台 | 轻量、支持自建部署，原生的 Trace 树展示与 Eval 打分面板 | 企业数据合规、自建 Agent 可观测体系 |
| **Arize Phoenix** | 开源 AI 性能评估 | 专注 Embedding 漂移、LLM Eval、原生集成 OpenTelemetry | 需要深度模型分析与数据集质检的团队 |
| **Braintrust** | 商业化 Eval Harness | 极其强大的 CI/CD 评测集成，支持极低延迟的离线/在线 Eval | 追求高迭代效率的企业生产级应用 |
| **OpenInference** | OpenTelemetry 标准扩展 | 联合 CNCF 倡导的语义标准（Semantic Conventions），标准化 Agent Trace 格式 | 需接入传统 Datadog / Dynatrace 运维体系 |
