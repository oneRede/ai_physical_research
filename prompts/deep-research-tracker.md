# 深度研究追踪 Prompt

> 用途：定期（每周）运行，发现具身智能与物理世界 AI 领域的高价值新内容
> 推荐工具：ChatGPT Deep Research（广度搜索）→ Claude（深度分析 + 项目关联）

---

## Prompt A：ChatGPT Deep Research — 广度发现

```
你是一个物理 AI 技术情报分析师。请对以下领域进行深度网络搜索，找出过去 1 周内（{START_DATE} 至 {END_DATE}）发布的高价值内容。

### 搜索领域

核心主题：
- "Embodied AI"（具身智能）
- "Physical AI"（物理 AI）
- "Robotics"（机器人技术）
- "Autonomous systems"（自主系统）
- "Robot learning"（机器人学习）

相关关键词（中英文）：
- humanoid robot,人形机器人,双足机器人,bipedal robot,human-robot interaction
- robot manipulation,机器人操作,robotic grasping,灵巧操作,dexterous manipulation
- autonomous driving,自动驾驶,end-to-end driving,perception planning,vehicle autonomy
- embodied intelligence,具身智能,embodied agents,physical reasoning,world model
- visual navigation,视觉导航,SLAM,semantic mapping,spatial understanding
- sim-to-real,仿真迁移,physics simulation,digital twin,robot simulator
- vision-language-action,VLA model,multimodal robotics,embodied QA
- AI for physics,物理学 AI,materials science AI,molecular dynamics,quantum computing
- edge AI,边缘计算,real-time systems,robot hardware,sensors
- robot foundation model,机器人基础模型,generalist robot,zero-shot robot

### 搜索范围

必须覆盖的信源（按优先级）：

**Tier 1 — 高权重（顶级学术 + 行业媒体）：**
- Nature / Science / Nature Machine Intelligence（机器人与具身智能论文）
- arXiv (cs.RO, cs.AI, cs.CV 交叉领域)
- IEEE Robotics and Automation Letters (RA-L)
- CoRL / RSS / ICRA / IROS 会议论文
- MIT Technology Review（机器人与自动化板块）
- TechCrunch（机器人创业报道）
- The Robot Report
- 机器之心、量子位、新智元（机器人/自动驾驶报道）

**Tier 2 — 中权重（实验室 + 公司博客）：**
- Google DeepMind Blog（机器人研究）
- OpenAI Blog（机器人项目）
- Tesla AI Day / Tesla FSD 更新
- Boston Dynamics / Agility Robotics / Figure AI 技术博客
- NVIDIA Isaac Sim / Omniverse 更新
- UC Berkeley BAIR / CMU RI / MIT CSAIL / Stanford AI Lab
- Physical Intelligence / Covariant / Skild AI 博客
- 知乎专栏（机器人学习、自动驾驶实践）

**Tier 3 — 低权重但可能有惊喜：**
- GitHub Trending（机器人/仿真相关仓库）
- Hacker News（机器人讨论）
- Reddit (r/robotics, r/reinforcementlearning, r/selfdrivingcars)
- YouTube（机器人演示视频、学术报告）
- Twitter/X（机器人研究者、实验室账号）


### 我们已知的内容（用于去重和关联）

> **本节是 Prompt 的去重权威**——给外部搜索器（ChatGPT Deep Research 等）使用。
> 它必须自包含，因为搜索器无法访问 `references/articles.md`。
>
> **维护纪律：** 当 `references/articles.md` 新增/删除条目时，**同一次提交中**必须同步更新本节。两份内容的口径（脉络划分、篇数、产品/项目清单）应保持完全一致。


**已跟踪的开源项目/产品：**

**请重点发现：**
- 上述未覆盖的新作者 / 新视角 / 新组织
- 对上述文章的**深度回应或反驳**（不是简单转述）
- 与上述领域**互补或竞争**的新应用 / 产品 / 案例
- 中文社区针对上述材料的原创分析（少数派、掘金、知乎专栏等）

### 输出格式

请按以下格式输出，每条内容一个条目：

---

#### [编号]. {标题}

- **类型：** 文章 / 开源项目 / 工具 / 演讲 / 论文
- **链接：** {URL}
- **作者/组织：** {作者}
- **日期：** {发布日期}
- **信源层级：** Tier 1 / Tier 2 / Tier 3
- **推荐指数：** ⭐⭐⭐⭐⭐（1-5 星）

**一句话摘要：** {50 字以内}

**核心洞察（3-5 条）：**
1. ...
2. ...
3. ...

**与已知内容的关联：**
- 支持/挑战/扩展了哪篇已有文章的观点
- 填补了哪个已知缺口

**值得收录的理由 / 不值得的理由：**
{判断}

---

### 质量过滤标准

**必须满足（全部）：**
- 有实质性的技术内容（不是纯营销或产品公告）
- 有原创洞察或数据（不是对已有文章的简单转述）
- 来源可信（有署名，有技术背景）

**加分项（满足越多越好）：**
- 有实际数据或实验结果
- 有代码示例或可复现的方案
- 挑战了主流观点
- 来自一线实践者（不是纯理论）
- 有中文社区尚未覆盖的视角

**排除：**
- 纯软件 AI（不涉及物理世界交互）
- 纯商业分析与市场报告（除非包含技术突破）
- 过于初级的机器人入门教程
- 纯硬件产品发布（除非展示技术创新）
- 仅概念性讨论，无实验验证或实际部署

### 输出数量

- 文章/论文类：推荐 5-10 篇，按推荐指数排序
- 开源项目类：推荐 3-5 个
- 其他（技术报告/演示视频/数据集）：如有高质量内容，不限数量
- 将报告保存到report/目录下
```

---

## Prompt B：Claude — 深度分析与项目关联

> 在 ChatGPT 返回结果后，将结果喂给 Claude（在本项目中），做深度分析

```
以下是最近 1 周的技术情报搜索结果。请基于我们项目的已有内容，做以下分析：

{粘贴 ChatGPT 的输出}

### 请分析：

1. **优先级排序**：哪些内容最值得我们收录？考虑因素：
   - 对已收录文章的补充价值
   - 技术突破性和创新性
   - 实验数据和实际部署案例的真实性与深度
   - 对具身智能与物理世界 AI 发展的启发意义

2. **缺口分析**：这批内容覆盖了我们的哪些知识缺口？还有哪些缺口未被触及？
   当前已知缺口：
   - 机器人操作的泛化能力验证案例
   - 自动驾驶的端到端学习实践
   - Sim-to-real 迁移的成功案例
   - 物理推理与世界模型的实际应用
   - 多模态机器人基础模型

3. **趋势信号**：这批内容中是否有新的趋势或方向？是否揭示了新的技术路径或研究范式？

4. **收录建议**：对每条推荐内容给出具体建议：
   - 收录到 references/articles.md（哪个脉络）
   - 值得翻译到 works/
   - 值得在 thinking/ 中写分析
   - 暂不收录，持续观察
```

---

## Prompt C：Manus / OpenClaw — 自动化监控

> 用于设置定时监控的固定信源

```
定时任务：每日检查以下信源的更新

监控列表：
**Tier 1 — 高权重（顶级实验室 + 机器人公司）：**
- Google DeepMind Blog (deepmind.google/research/robotics)
- OpenAI Blog (openai.com/research - 机器人相关)
- Boston Dynamics Blog (bostondynamics.com/blog)
- Tesla AI (tesla.com/AI)
- Physical Intelligence (physicalintelligence.company)
- Covariant Blog (covariant.ai/insights)
- UC Berkeley BAIR (bair.berkeley.edu/blog)
- MIT CSAIL (csail.mit.edu/news)
- Stanford AI Lab (ai.stanford.edu/blog)

**Tier 2 — 中权重（社区 + 行业）：**
- arXiv (cs.RO, cs.AI, cs.LG 交叉)
- GitHub Trending (robotics, simulation, autonomous-driving)
- Hacker News (前 100 讨论 - 机器人相关)
- The Robot Report (therobotreport.com)
- IEEE Spectrum Robotics (spectrum.ieee.org/robotics)
- 中文社区：机器之心、量子位、新智元
- Reddit (r/robotics, r/reinforcementlearning, r/selfdrivingcars)

**Tier 3 — 低权重但可能有惊喜：**
- YouTube 技术频道（机器人演示、学术报告）
- Twitter/X 研究者账号
- Medium 机器人专栏
- 个人技术博客（机器人研究者）

匹配关键词：
- humanoid robot,人形机器人,双足机器人,bipedal robot
- robot manipulation,机器人操作,robotic grasping,灵巧操作
- autonomous driving,自动驾驶,end-to-end driving,FSD
- embodied intelligence,具身智能,embodied agents,physical reasoning
- visual navigation,视觉导航,SLAM,semantic mapping
- sim-to-real,仿真迁移,physics simulation,digital twin
- vision-language-action,VLA model,robot foundation model
- AI for physics,材料科学 AI,molecular dynamics
- edge AI,实时系统,robot hardware
- world model,物理推理,spatial understanding

输出：
- 有更新时，发送通知（标题 + 链接 + 匹配的关键词）
- 无更新时，静默
```

---

## 工作流总结

```
┌─────────────────────────────────────────────────┐
│  Layer 1: 自动化监控（每日）                       │
│  工具：OpenClaw / Manus                          │
│  输出：固定信源的新内容通知                         │
└──────────────────────┬──────────────────────────┘
                       ↓ 有新内容时触发
┌─────────────────────────────────────────────────┐
│  Layer 2: 广度搜索（每周）                         │
│  工具：ChatGPT Deep Research                     │
│  输入：Prompt A                                   │
│  输出：5-10 篇文章 + 3-5 个项目的结构化摘要         │
└──────────────────────┬──────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────┐
│  Layer 3: 深度分析（按需）                         │
│  工具：Claude（本项目内）                          │
│  输入：Prompt B + ChatGPT 输出                    │
│  输出：优先级排序 + 缺口分析 + 收录建议              │
└──────────────────────┬──────────────────────────┘
                       ↓
┌─────────────────────────────────────────────────┐
│  Layer 4: 人工确认                                │
│  你决定：收录 / 翻译 / 写分析 / 跳过               │
└─────────────────────────────────────────────────┘
```
