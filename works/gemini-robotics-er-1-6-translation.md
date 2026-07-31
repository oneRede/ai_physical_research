---
sourceTitle: "Gemini Robotics ER 1.6: Enhanced Embodied Reasoning"
sourceUrl: "https://deepmind.google/blog/gemini-robotics-er-1-6/"
requestedUrl: "https://deepmind.google/blog/gemini-robotics-er-1-6/"
author: "Laura Graesser and Peng Xu"
coverImage: "imgs/img-006-bQ5TgF2iHJq48mxL5ZVxz3KGmIhOj7c62H7WEWrHqIWGrMex.webp"
siteName: "Google DeepMind"
publishedAt: "2026-04-14T16:00:00+00:00"
sourceSummary: "Gemini Robotics ER 1.6 upgrades spatial reasoning and multi-view understanding, unlocking new capabilities like instrument reading for autonomous robots."
adapter: "generic"
capturedAt: "2026-07-28T10:55:17.399Z"
conversionMethod: "defuddle"
kind: "generic/article"
sourceLanguage: "en"
title: "Gemini Robotics ER 1.6：增强的具身推理能力"
pipelineRunId: "batch-2026-07-28"
pipelineSource: "curate-research"
---

# Gemini Robotics ER 1.6：增强的具身推理能力

![汽车空调歧管压力表的俯视诊断视图，呈现在深色圆形晕影中。这是 Gemini Robotics-ER 1.6 的宣传图，展示了车库环境中由 AI 驱动的物体检测和计算机视觉。](imgs/img-001-Ta2yt0YDLre0HKHK1fbBA0Zg2LkRHJN8mL4iXhuHDVBIKQcJ.webp)

要让机器人在日常生活和工业中真正发挥作用，它们必须不仅仅遵循指令，还必须对物理世界进行推理。从导航复杂设施到解读压力表上的指针，机器人的"**具身推理**"（embodied reasoning）能力让它能够弥合数字智能与物理行动之间的鸿沟。

今天，我们推出 [Gemini Robotics-ER 1.6](https://deepmind.google/models/gemini-robotics/)，这是对我们推理优先模型的重大升级，使机器人能够以前所未有的精度理解其环境。通过增强空间推理和多视图理解能力，我们为下一代物理智能体带来了新的自主水平。

该模型专注于对机器人至关重要的推理能力，包括视觉和空间理解、任务规划和成功检测。它作为机器人的高层推理模型，能够通过原生调用 Google Search 等工具查找信息、调用视觉-语言-动作模型（VLAs）或任何其他第三方用户定义函数来执行任务。

Gemini Robotics-ER 1.6 相比 [Gemini Robotics-ER 1.5](https://developers.googleblog.com/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/) 和 [Gemini 3.0 Flash](https://blog.google/products-and-platforms/products/gemini/gemini-3-flash/) 都有显著改进，特别是在空间和物理推理能力方面，如指向、计数和成功检测。我们还解锁了一项新能力：仪表读数，使机器人能够读取复杂的仪表和观察镜——这是我们与合作伙伴 Boston Dynamics 密切合作发现的用例。

从今天开始，Gemini Robotics-ER 1.6 通过 [Gemini API](https://ai.google.dev/gemini-api/docs/robotics-overview) 和 [Google AI Studio](https://aistudio.google.com/prompts/new_chat?model=gemini-robotics-er-1.6-preview) 向开发者开放。为了帮助您入门，我们分享了一个开发者 [Colab](https://github.com/google-gemini/robotics-samples/blob/main/Getting%20Started/gemini_robotics_er.ipynb)，其中包含如何配置模型并为具身推理任务提示它的示例。

![一个标题为"成功率（%）"的柱状图，比较三个模型在四个任务上的表现。Gemini Robotics-ER 1.6（深蓝色）在所有任务上持续优于 Gemini 3.0 Flash（中蓝色）和 Gemini Robotics-ER 1.5（浅蓝色）。](imgs/img-002-dicgE2AAgiQBrY1zvNrdLqTsE5oNi3vbp95Zo4-vp809tdsR.webp)

图 1：基准测试结果，比较 Gemini Robotics-ER 1.6 与 Gemini Robotics-ER 1.5 和 Gemini 3.0 Flash 模型。仪表读数评估在启用 agentic vision 的情况下运行（Gemini Robotics-ER 1.5 除外，因为它不支持）。所有其他评估在禁用 agentic vision 的情况下运行。单视图和多视图成功检测评估包含不同的示例，因此不可比较。

## 指向：空间推理的基础

指向是具身推理模型的基本能力，随着每一代模型而演进。点可以用来表达许多概念，包括：

- **空间推理**：精确的物体检测和计数
- **关系逻辑**：进行比较，例如识别集合中最小的物品；定义"从-到"关系（例如将 X 移动到位置 Y）
- **运动推理**：映射轨迹并识别最佳抓取点
- **约束合规**：推理复杂的提示，如"指向所有小到足以放入蓝色杯子的物体"

Gemini Robotics-ER 1.6 可以使用点作为中间步骤来推理更复杂的任务。例如，它可以使用点来计数图像中的物品，或识别图像上的显著点，以帮助模型执行数学运算来改进其度量估计。

下面的示例展示了 Gemini Robotics-ER 1.6 在指向多个元素以及知道何时应该和何时不应该指向方面的优势。

![Gemini Robotics-ER 1.6 正确识别了锤子（2个）、剪刀（1把）、画笔（1支）、钳子（6把）的数量，以及一组园艺工具，这些工具可以被解释为单个组或多个点。它不会指向图像中不存在的请求物品——手推车和 Ryobi 电钻。相比之下，Gemini Robotics-ER 1.5 未能识别正确的锤子或画笔数量，完全遗漏了剪刀，幻觉出了手推车，并且在钳子指向上缺乏精度。Gemini 3.0 Flash 接近 Gemini Robotics-ER 1.6，但在处理钳子方面不如后者。](imgs/img-003-wX1QYLrafPEhOPLVaFTsvztVDlTW4g7YglaDK1Ex4fO-4spB.webp)

Gemini Robotics-ER 1.6 正确识别了锤子（2个）、剪刀（1把）、画笔（1支）、钳子（6把）的数量，以及一组园艺工具，这些工具可以被解释为单个组或多个点。它不会指向图像中不存在的请求物品——手推车和 Ryobi 电钻。相比之下，Gemini Robotics-ER 1.5 未能识别正确的锤子或画笔数量，完全遗漏了剪刀，幻觉出了手推车，并且在钳子指向上缺乏精度。Gemini 3.0 Flash 接近 Gemini Robotics-ER 1.6，但在处理钳子方面不如后者。

## 成功检测：自主的引擎

在机器人技术中，知道任务何时完成与知道如何开始任务同样重要。成功检测是自主的基石，作为关键的决策引擎，使智能体能够智能地选择是重试失败的尝试还是继续执行计划的下一阶段。

在机器人技术中实现视觉理解具有挑战性，需要复杂的感知和推理能力以及广泛的世界知识，以处理遮挡、光照不佳和模糊指令等复杂因素。此外，大多数现代机器人设置包括多个摄像头视图，例如俯视和腕部安装的摄像头。这意味着系统需要理解不同视角如何在每个时刻和跨时间组合形成连贯的画面。

Gemini Robotics-ER 1.6 推进了多视图推理，使系统能够更好地理解多个摄像头流及其之间的关系，即使在动态或遮挡环境中，如下面典型的多视图场景所示。

<video controls=""><source type="video/webm" src="videos/video-001-gemini-robotics_1-6__success-detection_multiview.webm"> 您的浏览器不支持视频标签。</video>

Gemini Robotics-ER 1.6 从多个摄像头视图获取线索，以确定任务"将蓝色笔放入黑色笔筒"何时完成。

## 仪表读数：真实世界的视觉推理

要理解 Gemini Robotics-ER 1.6 的一个关键优势，我们必须看看它如何结合空间推理和世界知识等能力来解决复杂的真实世界问题。一个完美的例子是仪表读数。

这项任务源于设施检查需求，这是我们在 Boston Dynamics 的合作伙伴的一个关键重点领域。工业设施包含许多仪表——温度计、压力表、化学观察镜等——需要持续监控。[Spot，Boston Dynamics 的机器人产品](https://bostondynamics.com/blog/aivi-learning-now-powered-google-gemini-robotics/)，能够访问整个设施中的仪表并捕获它们的图像。

Gemini Robotics-ER 1.6 使机器人能够解读各种仪表，包括圆形压力表、垂直液位指示器和现代数字读数。

仪表读数需要复杂的视觉推理。必须精确感知各种输入——包括指针、液位、容器边界、刻度线等——并理解它们之间的相互关系。在观察镜的情况下，这涉及估计液体填充观察镜的程度，同时考虑相机视角造成的失真。仪表通常有描述单位的文字，必须被读取和解释，有些仪表有多个指针指向不同的小数位，需要组合起来。

> 像仪表读数这样的能力以及更可靠的任务推理将使 Spot 能够完全自主地看到、理解和应对真实世界的挑战。

Boston Dynamics Spot 副总裁兼总经理

Gemini Robotics-ER 1.6 通过使用 [agentic vision](https://blog.google/innovation-and-ai/technology/developers-tools/agentic-vision-gemini-3-flash/) 实现高度准确的仪表读数，该技术将视觉推理与代码执行相结合。模型采取中间步骤：首先放大图像以更好地读取仪表中的小细节，然后使用指向和代码执行来估计比例和间隔并获得准确读数，最终应用其世界知识来解释含义。

![一个标题为"仪表读数"的柱状图，显示四个 AI 模型的成功率。性能从左到右显著提高：Gemini Robotics-ER 1.5：23%，Gemini 3.0 Flash：67%，Gemini Robotics-ER 1.6：86%，Gemini Robotics-ER 1.6 w/ agentic vision：93%（最高，以条纹图案显示）](imgs/img-004-RvYAY_w1ZJfrVeEtxg3oh6YjyQuvSgFcIammormuzrUixbvw.webp)

图 2：Gemini Robotics-ER 1.6 的不同元素如何共同作用，在仪表读数任务上达到高水平的性能。

### 精确读取模拟仪表

<video controls=""><source type="video/webm" src="videos/video-002-gemini-robotics_1-6__instrument-reading-demo.webm"> 您的浏览器不支持视频标签。</video>

此示例演示了模型如何使用指向和代码执行进行缩放，以精确到刻度以下的精度读取仪表读数。

## 我们迄今为止最安全的机器人模型

安全性被整合到我们具身推理模型的每个层面。Gemini Robotics-ER 1.6 是我们迄今为止最安全的机器人模型，在对抗性空间推理任务上展示了相比所有前代产品对 [Gemini 安全政策](https://gemini.google/policy-guidelines/)的卓越合规性。

该模型还展示了大幅改进的遵守物理安全约束的能力。例如，它通过指向等空间输出在夹持器或材料约束下对哪些物体可以安全操作做出更安全的决策（例如，"不要处理液体"，"不要拾取重于 20 公斤的物体"）。

我们还测试了模型在基于真实伤害报告的[文本和视频场景](https://asimov-benchmark.github.io/v2/)中识别安全隐患的能力。在这些任务上，我们的 Gemini Robotics-ER 模型在准确感知伤害风险方面相比基线 Gemini 3.0 Flash 性能有所改进（文本 +6%，视频 +10%）。

![一个标题为"ASIMOV - 安全指令遵循"的柱状图，比较三个模型——Gemini Robotics-ER 1.5、Gemini 3.0 Flash 和 Gemini Robotics-ER 1.6——在三个类别上的表现：文本准确度、点准确度和边界框准确度。Y 轴表示"违规率（%）"，其中"越高越好"。](imgs/img-005-oSCN0Y87DsBmpc5bhRNb7HRoujoMCESybNR0dwg8dQ1eVzwC.webp)

图 3：Gemini Robotics-ER 1.6 相比 Gemini Robotics-ER 1.5 在安全指令遵循方面有大幅改进，该测试评估遵守物理安全约束的能力。它在指向方面相比 Gemini 3.0 Flash 有所改进，两个模型在文本方面都有非常高的准确度。Gemini 3.0 Flash 在边界框方面表现更好。

## 与我们合作，改进机器人的具身推理能力

我们致力于确保 Gemini Robotics-ER 为机器人社区提供最大价值。如果当前功能对您的专业应用有限，我们邀请您[提交此表单](https://forms.gle/a5jRuga5VmnCeQCk9)，提供 10-50 张标注图像，说明特定的失败模式，以帮助我们构建更稳健的推理功能。我们期待与您合作，在即将发布的版本中增强这些能力。

立即在 [Google AI Studio](https://aistudio.google.com/prompts/new_chat?model=gemini-robotics-er-1.6-preview) 上试用 Gemini Robotics-ER 1.6
