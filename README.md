# AI 具身智能与物理世界进展追踪

> 追踪和收录全球具身智能（Embodied AI）与物理世界 AI 的最新技术进展，聚焦智能体与物理世界的交互、感知、决策与行动。

## 前言

这是一个不断生长的学习项目。利用智能体（agent）实现具身智能与物理世界 AI 领域技术进展的自动化收录与整理。

## 收录范围

**聚焦 AI 在物理世界中的感知、决策与行动**，包括但不限于：

- **机器人技术**：人形机器人、工业机器人、服务机器人、机械臂控制、灵巧操作
- **自动驾驶**：感知系统、决策规划、端到端驾驶、仿真测试、车路协同
- **具身智能基础**：世界模型、物理推理、空间理解、因果推理、常识推理
- **感知与导航**：视觉 SLAM、语义地图、路径规划、obstacle avoidance、多模态感知
- **操作与交互**：物体抓取、场景理解、human-robot interaction、任务规划、技能学习
- **仿真与数据**：物理仿真器、sim-to-real、合成数据生成、数字孪生
- **多模态融合**：视觉-语言-动作（VLA）模型、embodied question answering、视觉导航
- **物理 AI 应用**：AI for Physics（物理学研究）、材料科学、分子动力学、量子计算
- **硬件与平台**：机器人硬件、传感器、边缘计算、实时系统

**不收录**：纯软件 AI（不涉及物理世界）、纯应用案例（除非展示技术突破）、商业分析与市场报告。

## 核心概念

1. 基于 agent 的搜索、爬取网页能力以及搜索 API，从给定的信源获取 AI 应用相关的信息
2. agent 对获取的文章进行翻译
3. agent 判断文章价值并判断处理方式
4. agent 确保整个项目的健康长久运行

## 📂 仓库结构

```
ai_application_research/
├── README.md              ← 你在这里
├── AGENTS.md              ← 仓库导航入口（给智能体看的）
├── thinking/              # Phase 2：独立思考与质疑
├── feedback/              # Phase 4：踩坑与迭代心得
├── works/                 # Phase 5：可展示的作品
├── prompts/               # 验证有效的提示词积累
└── references/            # 外部资源索引
```

每个子目录都有自己的 `AGENTS.md`，说明该目录的用途和写作约定。这本身就是原文「渐进式披露」的实践。

## 📚 收录内容

### 翻译作品（6 篇）

| # | 标题 | 原文 | 翻译 |
|---|------|------|------|
| 1 | NVIDIA 发布 Isaac GR00T 人形机器人参考设计 | [NVIDIA](https://nvidianews.nvidia.com/news/nvidia-open-humanoid-robot-reference-design) | [works/nvidia-isaac-groot-reference-robot-translation.md](works/nvidia-isaac-groot-reference-robot-translation.md) |
| 2 | 端到端自动驾驶时代：从基于规则的驾驶到大驾驶模型的转变 | [arXiv](https://arxiv.org/html/2603.16050v1) | [works/end-to-end-driving-survey-translation.md](works/end-to-end-driving-survey-translation.md) |
| 3 | Gemini Robotics ER 1.6：增强的具身推理能力 | [DeepMind](https://deepmind.google/blog/gemini-robotics-er-1-6/) | [works/gemini-robotics-er-1-6-translation.md](works/gemini-robotics-er-1-6-translation.md) |
| 4 | PNDbotics Adam 人形机器人 WAIC 2026 演示 | [Embodied Global](https://embodiedglobal.com/en/article/pndbotics-adam-humanoid-stair-climbing-waic-2026) | [works/pndbotics-adam-waic-2026-translation.md](works/pndbotics-adam-waic-2026-translation.md) |
| 5 | 通过机器人渲染实现机器人因子化世界模型 | [arXiv](https://arxiv.org/abs/2607.22535) | [works/robot-factored-world-models-translation.md](works/robot-factored-world-models-translation.md) |
| 6 | 具身 GPT-5.1：世界模型的证据？ | [arXiv](https://arxiv.org/abs/2607.23899) | [works/embodied-gpt-5.1-world-model-translation.md](works/embodied-gpt-5.1-world-model-translation.md) |

完整索引见 [references/articles.md](references/articles.md)

## 🛠️ 开发须知

仓库自带一致性检查脚本 `scripts/check-consistency.sh`，守护数量类漂移，覆盖八层校验：

- **C1** — `references/articles.md` 编号 1..N 连续
- **C2** — N 与下游 3 处声明同步（README、`prompts/deep-research-tracker.md` 头部、`references/AGENTS.md` 概览）。文件含独立行 `<!-- check-consistency: skip-count -->` 时豁免
- **C3** — `thinking/`、`feedback/` 的 `*.md` 实际数与 README 中"X 篇"声明一致
- **C4** — `works/*-translation.md` 文件数 ≡ 翻译计数所有声明（badges、`<details>` 摘要、Phase 5 注释、本文件 Phase 5 快照、READMEs 表格行数）
- **C5** — `references/articles.md` 末尾"不计入 N 篇"中的 N ≡ C1 权威值
- **C6** — 翻译流水线本地守卫：`translate/<...>/sources/<slug>/source-full.md` 存在时，对应 `01-analysis.md` 不得再声称"仅摘要页 / 建议补抓全文"。`translate/` 已 gitignore，CI 与干净 clone 自动 SKIP，仅本地有过程稿时触发
- **C7** — / `thinking/` / `feedback/` 正文不得裸写文库计数（"N 篇文章 / N 篇翻译 / N 大概念"）；历史性提法须带"写作时点 / 当时 / 此前 / 首批 / 首轮 / 截至 / 快照"限定词，否则去数字改链 `references/articles.md`
- **C8** — `works/*-translation.md` 必须在 frontmatter 声明 `pipelineRunId` 与 `pipelineSource`，防止绕过 `translate/<batch>/works-ready/` 直接写入正式档案

**首次 clone 后启用 pre-commit hook：**

```bash
git config core.hooksPath .githooks
```

启用后，每次 commit 涉及 README、`AGENTS.md`、`references/articles.md`、`references/AGENTS.md`、`prompts/deep-research-tracker.md`、或 `thinking/` / `feedback/` / `works/` 中的 `*.md` 时会自动跑检查；不涉及则不打扰。

**手动跑：** `bash scripts/check-consistency.sh`

**CI 兜底：** 即使本地未启用 hook，GitHub Actions（`.github/workflows/consistency.yml`）会在每次 push / PR 时跑同一脚本（不做路径过滤，保证分支保护的必需检查总能得到上报）。本地 hook 是开发期反馈，CI 才是真正的合并门。

详情见根 `AGENTS.md` 的"机械化检查"段。

## 🤖 自动化策展

> 这个仓库通过智能体实现具身智能与物理世界 AI 技术进展的自动化收录。
>
> 收录流程固化为 skill 流水线 [`curate-research`](.claude/skills/curate-research/SKILL.md)：评审由并行 agent 自动完成，`scripts/check-consistency.sh` 守护计数一致性，而"是否收录"的决策权始终由人类掌握。
