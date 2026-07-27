# 仓库重新聚焦：从 AI 应用到具身智能与物理世界 AI

**日期**：2026-07-27  
**类型**：重大重构

## 改动概述

将仓库从"AI 应用进展追踪"重新聚焦为"具身智能与物理世界 AI 进展追踪"。

## 核心变更

### 1. 收录范围调整

**之前**：聚焦 AI 在医疗、金融、游戏、工业等领域的应用案例

**现在**：聚焦 AI 在物理世界中的感知、决策与行动，包括：
- **机器人技术**：人形机器人、工业机器人、服务机器人、机械臂控制、灵巧操作
- **自动驾驶**：感知系统、决策规划、端到端驾驶、仿真测试、车路协同
- **具身智能基础**：世界模型、物理推理、空间理解、因果推理、常识推理
- **感知与导航**：视觉 SLAM、语义地图、路径规划、obstacle avoidance、多模态感知
- **操作与交互**：物体抓取、场景理解、human-robot interaction、任务规划、技能学习
- **仿真与数据**：物理仿真器、sim-to-real、合成数据生成、数字孪生
- **多模态融合**：视觉-语言-动作（VLA）模型、embodied question answering、视觉导航
- **物理 AI 应用**：AI for Physics（物理学研究）、材料科学、分子动力学、量子计算
- **硬件与平台**：机器人硬件、传感器、边缘计算、实时系统

### 2. 内容清空

- 删除 14 篇应用案例翻译（医疗、金融、工业等）
- 清空 `works/imgs/` 图片目录
- 重置 `references/articles.md` 为 0 篇
- 保留仓库架构和流水线机制

### 3. 文档更新

**主要文档**：
- `README.md` — 更新项目定位和收录范围
- `AGENTS.md` — 更新仓库导航说明
- `references/articles.md` — 重置索引为空
- `works/AGENTS.md` — 更新作品方向参考
- `prompts/deep-research-tracker.md` — 完全重写情报追踪 prompt

**辅助文档**：
- `thinking/AGENTS.md` — 更新写作方向示例
- `prompts/AGENTS.md` — 更新场景分类
- `references/AGENTS.md` — 更新文件约定
- `.claude/skills/curate-research/SKILL.md` — 更新评审标准

### 4. 信源覆盖

**deep-research-tracker.md** 新增主流信源：

**机器人公司**：
- Boston Dynamics, Tesla AI, Waymo
- Figure AI, 1X Technologies, Unitree Robotics
- Agility Robotics, Sanctuary AI, Physical Intelligence
- Covariant AI, Skydio

**中国自动驾驶与机器人**：
- 小鹏汽车 AI, 理想汽车 AD, 蔚来 NAD
- 百度 Apollo

**研究机构**：
- DeepMind Robotics, OpenAI Robotics
- UC Berkeley BAIR, Stanford AI Lab, MIT CSAIL
- CMU Robotics Institute, ETH Zurich RSL
- Toyota Research Institute, NVIDIA Isaac

### 5. 保留的架构

✅ 6 阶段流水线（抓取→翻译→评审→收录→校验→清理）  
✅ C1-C12 一致性检查机制  
✅ works/ + references/ 双层结构  
✅ thinking/ + feedback/ 反思空间  
✅ 人类闸门决策机制  
✅ baoyu-translate 和 baoyu-url-to-markdown 子 skill

## 验证结果

```bash
bash scripts/check-consistency.sh
```

**结果**：✅ 全部检查通过（C1-C8，空仓库状态）

## Git 状态

**修改的文件**（9+ 个）：
- README.md
- AGENTS.md
- references/articles.md
- references/AGENTS.md
- works/AGENTS.md
- thinking/AGENTS.md
- prompts/AGENTS.md
- prompts/deep-research-tracker.md
- .claude/skills/curate-research/SKILL.md

**删除的文件**（14 个翻译作品）：
- works/*-translation.md（全部应用案例）
- works/imgs/（图片目录）

## 下一步

1. **启动内容收录**：使用更新后的 `deep-research-tracker.md` prompt 开始追踪
2. **首批候选**：可从 arXiv cs.RO、CoRL/ICRA 最新论文、主流机器人公司博客开始
3. **验证流水线**：用 1-2 篇机器人技术论文验证翻译流水线

## 技术债务

无。架构保持完整，一致性检查全部通过。

## 兼容性

⚠️ **破坏性变更**：删除了所有历史翻译内容。如需恢复，可从 Git 历史中找回。

## 审批

- [x] 一致性检查通过
- [x] 文档逻辑自洽
- [x] 流水线配置更新完成
- [x] 信源覆盖全面（机器人、自动驾驶、研究机构）
