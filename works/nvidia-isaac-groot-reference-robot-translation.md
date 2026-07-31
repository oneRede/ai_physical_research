---
sourceTitle: "NVIDIA Announces NVIDIA Isaac GR00T Reference Humanoid Robot for Academic Research"
sourceUrl: "https://nvidianews.nvidia.com/news/nvidia-open-humanoid-robot-reference-design"
requestedUrl: "https://nvidianews.nvidia.com/news/nvidia-open-humanoid-robot-reference-design"
coverImage: "imgs/img-001-nvidia-and-unitree_83d7a169-ce01-483a-b313-b01fd.jpg"
siteName: "NVIDIA Newsroom"
publishedAt: "2026-05-31T00:00:00+00:00"
sourceSummary: "NVIDIA today announced the NVIDIA Isaac™ GR00T Reference Humanoid Robot, the first open humanoid robot reference design built on NVIDIA Jetson Thor™ and the NVIDIA Isaac GR00T open development platform."
adapter: "generic"
capturedAt: "2026-07-28T10:53:56.818Z"
conversionMethod: "defuddle"
kind: "generic/article"
sourceLanguage: "en-us"
title: "NVIDIA 发布 Isaac GR00T 人形机器人参考设计，面向学术研究"
pipelineRunId: "batch-2026-07-28"
pipelineSource: "curate-research"
---

# NVIDIA 发布 Isaac GR00T 人形机器人参考设计，面向学术研究

**新闻摘要：**

- NVIDIA 发布基于 Isaac GR00T 平台的开放式**人形机器人**（humanoid robot）**参考设计**（reference design），整合了 Unitree H2 Plus 人形机器人本体、Sharpa 五指灵巧手、NVIDIA Jetson Thor 板载计算平台以及 Isaac GR00T 开源软件与模型。
- Isaac GR00T 开发平台涵盖从数据采集与生成到机器人模型评估与部署的完整流程，帮助研究人员和开发者加速人形机器人开发工作流。
- 包括 Ai2（Allen Institute for AI，艾伦人工智能研究所）、ETH Zurich（苏黎世联邦理工学院）、Stanford Robotics Center（斯坦福机器人中心）和 UC San Diego 先进机器人与控制实验室在内的领先研究机构将使用该参考设计推进前沿人形机器人研究。

**NVIDIA GTC Taipei** —— NVIDIA 今日宣布推出 NVIDIA Isaac™ GR00T Reference Humanoid Robot，这是首个基于 [NVIDIA Jetson Thor](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-thor/)™ 和 [NVIDIA Isaac GR00T 开放开发平台](https://developer.nvidia.com/isaac/gr00t)构建的开放式人形机器人参考设计。

该参考设计提供先进硬件和开放软件栈，无需依赖专有平台，从而降低前沿人形机器人研究的准入门槛。

通用人形机器人的需求快速增长，但研究人员仍面临硬件集成、数据采集、仿真、训练、评估和部署等碎片化流程的挑战。

NVIDIA Isaac GR00T Reference Humanoid Robot 将这些环节统一到单一参考设计中：Unitree H2 Plus 人形机器人本体和 Sharpa Wave 触觉五指手作为"身体"，NVIDIA Jetson Thor 驱动的板载计算平台及 Isaac GR00T 软件和工作流作为"大脑"。这一整合帮助研究团队更快地从机器人搭建推进到技能开发和真实世界验证。

通过将 NVIDIA 的计算平台和开源软件栈置于核心位置，该参考设计为研究团队提供了统一、安全的基础，用于推进人形机器人技术。

"人形机器人将把**物理 AI**（physical AI）带入全球最大的产业，带来数万亿美元的市场机会，" NVIDIA 创始人兼首席执行官黄仁勋表示。"NVIDIA Isaac GR00T Reference Humanoid Robot 为研究人员提供了统一的开放平台，助力在通用物理智能领域取得突破性发现。"

## 面向物理 AI 开发的先进人形机器人平台

NVIDIA Isaac GR00T Reference Humanoid Robot 将前沿人形机器人研究所需的关键构建模块整合到一个系统中，将人类尺度的机器人本体与**灵巧操作**（dexterous manipulation）、感知、控制和板载 AI 计算相结合。

该参考设计具有以下特性：

- **Unitree H2 人形机器人底盘**，高近 6 英尺（约 1.8 米），重 150 磅（约 68 公斤），全身具有 31 个**自由度**（degrees of freedom, DOF），适用于人类尺度测试。
- **双 [Sharpa Wave](https://www.sharpa.com/pages/wave) 触觉五指手**，具备 22 个自由度的灵巧操作能力，使机器人全身及双手总计达到 75 个自由度。
- **多视角感知**，包括头部安装的立体相机（视野范围广：水平 140 度，垂直 102 度）、用于近距离操作的腕部相机，以及用于运动跟踪的惯性测量单元。
- **全身控制**，手臂扭矩高达 120 牛·米，腿部扭矩高达 360 牛·米，手臂额定负载 7 千克、峰值负载 15 千克，实现强大的提升和触达能力。
- **NVIDIA Jetson AGX Thor™ T5000 板载计算**，搭载 NVIDIA Blackwell GPU，提供 2,070 FP4 teraflops 的 AI 性能、14 核 Arm CPU、128GB 统一内存，以及可配置的 40-130 瓦功耗范围，用于实时传感器处理和机器人推理。
- **连接能力**，支持以太网、Wi-Fi 6、蓝牙 5.2、USB，并配备麦克风和扬声器阵列，实现语音交互。
- **长续航电池**，容量 15Ah、0.972kWh，续航约 3 小时。
- **远程紧急停止**功能，可快速安全地停止机器人运行。

## NVIDIA Isaac GR00T 提供人形机器人开发的全栈平台

NVIDIA 软件栈为仿真、训练、评估和部署提供了开发环境，同时研究人员保留对机器人数据、训练数据、遥测数据和日志的控制权。

Isaac GR00T 平台包括：

- [**NVIDIA Isaac Teleop**](https://nvidia.github.io/IsaacTeleop/main/index.html)，用于采集高质量机器人演示数据，支持训练和策略开发。
- [**NVIDIA Isaac GR00T 开源**基础模型**](https://github.com/NVIDIA/Isaac-GR00T)（foundation model），支持人形机器人推理、学习和多任务行为。
- [**NVIDIA Isaac Sim**](https://developer.nvidia.com/isaac/sim)**™** 和 [**Isaac Lab**](https://developer.nvidia.com/isaac/lab)，用于在真实世界部署前仿真、训练、测试和评估机器人策略。
- [**加速的 NVIDIA Isaac ROS 中间件**](https://developer.nvidia.com/isaac/ros)，用于将训练好的策略部署到机器人上。
- [**NVIDIA Jetson Thor**](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-thor/)，用于运行实时机器人推理和控制。

其模块化设计允许机器人团队使用完整平台，或将特定功能集成到现有开发流程中，帮助他们扩展人形机器人开发而无需为每个机器人或任务重建相同的基础设施。

NVIDIA Isaac GR00T 开发平台还将支持 Unitree G1 人形机器人，将相同的开发方法扩展到这款被领先研究机构和人形机器人开发者广泛采用的机器人。

## 加速机器人研究生态系统

包括 Ai2、ETH Zurich、Stanford Robotics Center 和 UC San Diego 先进机器人与控制实验室在内的领先研究机构将使用该人形机器人参考设计推进前沿人形机器人研究。

**Stanford Robotics Center 的视角**

"当研究人员能够基于开放平台构建、共享代码并在真实机器上测试想法时，机器人技术的发展最快，" Stanford Robotics Center 执行主任 Steve Cousins 表示。"NVIDIA Isaac GR00T Reference Robot 为我们的学生和合作者提供了开放的人形机器人参考设计，配备灵巧手、板载 AI 计算以及 NVIDIA Isaac GR00T 开发平台，用于在物理硬件上创建、比较和共享机器人行为。"

**ETH Zurich 的研究重点**

"ETH Zurich 的机器人研究旨在推进能够在真实世界中可靠移动、感知和操作的机器，" ETH Zurich 机器人系统实验室教授 Marco Hutter 表示。"NVIDIA Isaac GR00T 参考设计为我们的团队提供了先进的人形机器人平台，用于收集数据、测试算法，并通过 NVIDIA Isaac GR00T 开发平台验证机器人行为。"

**Skild AI 对开放平台的期待**

"要在通用机器人方面取得进展，研究人员需要既强大又广泛可及的平台，" Skild AI 联合创始人兼首席执行官 Deepak Pathak 表示。"参考设计让更多研究人员能够参与前沿人形机器人研究，更快地从想法推进到实验。这有助于推动整个机器人研究生态系统向前发展。"

**Ai2 的开放科学使命**

"在 Ai2，我们的使命是通过开放科学加速机器人技术发展，" Ai2 高级研究总监、华盛顿大学教授 Dieter Fox 表示。"NVIDIA Isaac GR00T Reference Robot 基于 NVIDIA 的开放技术构建，为我们的研究人员提供了继续广义能力机器人研究所需的硬件和软件组件。"

**UC San Diego 关注移动操作研究**

"推进解决真实世界问题的机器人研究需要能够在动态环境中精确移动、交互和操作的人形机器人，" UC San Diego 教授、先进机器人与控制实验室主任 Michael Yip 表示。"一个连接机器人硬件、数据采集、策略学习和物理评估的集成平台可以帮助研究人员加速**移动操作**（loco-manipulation，**即移动能力与操作能力的结合**）研究，开发更实用的真实世界系统。"

[NVIDIA Research](https://www.nvidia.com/en-us/research/) 也将使用该参考设计来推进 Isaac GR00T 开源模型、框架和硬件。

## 可用性

NVIDIA Isaac GR00T Reference Humanoid Robot 将于 2026 年底[通过 Unitree](https://www.unitree.com/H2plus) 发售。

面向 Unitree G1 的 NVIDIA Isaac GR00T 参考工作流预计将很快在 GitHub 和 Hugging Face 上向机器人开发者开放。

*观看黄仁勋的[主题演讲](https://www.nvidia.com/en-tw/gtc/taipei/keynote/?nvid=nv-int-bnr-823296)，并在 [NVIDIA GTC Taipei](https://www.nvidia.com/en-tw/gtc/taipei/) 了解更多信息。*
