# Agent工程与训练｜进阶知识
> 本目录：`docs/04‑Agent工程与训练/进阶知识`

本套文档面向**做私有微调Agent的工程师、TPM、AI产品**，聚焦从0到1落地可生产可用的LLM‑based Agent完整工程链路。
不只是概念科普，全部是可落地的实战流程、检查清单、决策树、避坑经验，覆盖数据集构建、蒸馏、训练脚手架、能力打磨、评测、线上故障排查、完整项目交付手册。

> ⚠️ 阅读前置：建议先阅读【Agent基础概念与架构】章节，掌握Agent基础范式、组件、MCP基本概念；
> 适合：已经了解Agent是什么，想要动手做、调优、上线生产级Agent的读者。

## 📄 文档列表
|序号|文档|主题简述|
|---|---|---|
|01|[01‑Core‑Concepts.md](./01‑Core‑Concepts.md)|Agent核心概念、术语定义、常见误区|
|02|[02‑Agent‑Architecture.md](./02‑Agent‑Architecture.md)|Agent分层架构、运行闭环、模块拆解|
|03|[03‑Agent‑Paradigm‑Evolve.md](./03‑Agent‑Paradigm‑Evolve.md)|Agent技术范式演进：ReAct → ToolLLM → SFT‑DPO微调Agent|
|04|[04‑Agent‑Infrastructure.md](./04‑Agent‑Infrastructure.md)|Agent基础设施：LangGraph / MCP / 状态管理、运行时组件|
|05|[05‑Harness‑Engineering.md](./05‑Harness‑Engineering.md)|训练脚手架Harness：配置、数据集预处理、Trainer、checkpoint、可复现工程实践|
|06|[06‑Capability‑Engineering.md](./06‑Capability‑Engineering.md)|能力工程：工具调用、规划、反思纠错、能力退化定位与修复|
|07|[07‑Knowledge‑Distillation.md](./07‑Knowledge‑Distillation.md)|Agent知识蒸馏：轨迹蒸馏、偏好蒸馏、prompt工程、过滤质检、避坑|
|08|[08‑Evaluation‑Benchmarking.md](./08‑Evaluation‑Benchmarking.md)|Agent完整评测体系：离线测试集、客观指标、LLM‑as‑Judge、人工评测、版本门禁|
|09|[09‑Production‑Troubleshooting.md](./09‑Production‑Troubleshooting.md)|生产故障排查：故障分类、排查决策树、典型案例、应急回退、case归档SOP|
|10|[10‑Agent‑Project‑Playbook.md](./10‑Agent‑Project‑Playbook.md)|Agent项目实战手册：完整生命周期、里程碑、风险、交付物清单|

## 🧩 知识链路（阅读顺序建议）
```
核心概念 → 架构 → 范式演进 → 基础设施
↓
训练脚手架开发 → 数据集&蒸馏
↓
能力打磨调优 → 完整评测体系
↓
生产故障排查 → 完整项目交付手册
```

> 💡 如果你只想解决某个具体问题，可以直接跳读对应文档：
> - 数据集怎么做 → 07‑Knowledge‑Distillation
> - Agent线上出问题如何排查 → 09‑Production‑Troubleshooting
> - 怎么做评测、版本门禁 → 08‑Evaluation‑Benchmarking
> - 从零启动一个Agent项目完整流程 → 10‑Agent‑Project‑Playbook

## 🎯 核心设计思想
1. **重工程落地**：不只讲算法理论，大量可复制的SOP、检查清单、决策树。
2. **重视可复现性**：强调数据集版本、实验元数据、训练‑推理一致性，这是Agent项目最大坑点。
3. **分层定位问题**：遇到坏case优先区分：工程bug / 数据集问题 / 模型能力上限 / 分布偏移，不要盲目堆样本。
4. **闭环思维**：Agent不是训练一次就结束，是「线上case回流‑数据集迭代‑训练‑评测‑上线」持续迭代闭环。

## 🔔 重要提醒
1. 微调不会凭空创造基座模型不具备的能力，只能对齐行为范式。
2. **训练‑推理不一致**是线上故障最高发来源，上线前务必执行完整核对清单。
3. 评测不能只看loss；Agent是交互式闭环，必须跑完整闭环做评测。
4. 分布外OOD场景优先运行时/编排兜底，不要盲目灌入训练集污染数据集。

## 📌 后续规划
后续计划补充文档：
- Agent安全与对齐
- Multi‑Agent多智能体工程实践
- Agent OS 前沿研究与工程现状
- MCP深度实践案例

## 📝 贡献
欢迎补充issue、PR，补充实践踩坑、案例、指标、最佳实践。

---
> 本系列属于 [awesome‑ai‑knowledge](https://github.com/momozi1996/awesome‑ai‑knowledge) 知识库，MIT License。
```
