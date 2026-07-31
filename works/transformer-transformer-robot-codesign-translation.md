---
title: "Transformer Transformer: A Unified Model for Motion-Conditioned Robot Co-design"
url: "https://arxiv.org/html/2607.25798v1"
requestedUrl: "https://arxiv.org/html/2607.25798v1"
adapter: "generic"
capturedAt: "2026-07-30T13:33:06.522Z"
conversionMethod: "defuddle"
kind: "generic/article"
language: "en"
pipelineRunId: "20260730_213232"
pipelineSource: "translate/20260730_213232"
translationMode: "fast"
---# Transformer Transformer：运动条件的机器人协同设计统一模型

Huy Ha <sup>1,2</sup>, Karen Liu <sup>1</sup>, Shuran Song <sup>1,2</sup>
<sup>1</sup> 斯坦福大学   <sup>2</sup> 哥伦比亚大学

###### 摘要

机器人操作性能的一个常被忽视的因素是机器人本身的形态。受此问题驱动，我们研究运动条件的机器人协同设计（Motion-Conditioned Robot Co-design），其目标是生成完整的机器人设计，使其能够跟踪目标末端执行器（End-effector）轨迹（来自人类演示），同时优化用户定义的奖励函数。我们引入 Transformer Transformer，这是一个在 RoboToken 上训练的扩散 Transformer（Diffusion Transformer）。RoboToken 是机器人具身（Embodiment）、状态和动作的统一 token 化表示。相同的架构可以跨越不同的具身空间（如轮式双臂、四足、人形机器人）和多种用例（具身生成、跨具身控制器）。Transformer Transformer 不是针对单一奖励函数进行过拟合，而是一个动力学模型。该模型对状态和动作的预测与奖励无关（Reward-agnostic），但可以转换为针对特定奖励的价值预测。这些价值预测通过一个我们称为"动力学自引导（Dynamics Self-Guidance）"的过程，用于引导具身扩散朝向高价值的机器人设计。跨多个设计空间的实验表明，该方法能够零样本（Zero-shot）优化未见过的奖励函数和轨迹，在性能和运行时间上都优于进化算法基线。最后，我们制作了一个优化后的 ALOHA 设计，与原始设计相比，跟踪误差降低了 $70\%$ 以上。请访问 [transformer-transformer.github.io](https://transformer-transformer.github.io/) 查看总结和结果视频。

![Refer to caption](https://arxiv.org/html/2607.25798v1/figures/teaser_v6.jpg)

图 1：演示、生成、验证。从目标末端执行器运动和未见过的奖励函数出发，Transformer Transformer 生成能够最大化奖励的优化机器人设计，然后通过控制生成的机器人跟踪给定运动来验证设计。该模型在机器人具身、状态和动作 token 上进行训练，将具身优化和跨具身控制统一到一个模型中，为从人类演示进行机器人协同设计提供了一站式解决方案。

> 关键词：生成式机器人协同设计、跨具身控制

## 1 引言

机器人操作性能的一个常被忽视的因素是机器人本身的形态。机器人能够有效执行的运动取决于其具身形态。受此驱动，我们提出以下问题：

> 对于给定的操作任务，最佳的机器人形态是什么？

我们将操作任务形式化为末端执行器轨迹，这是操作策略学习中广泛采用的任务表示方式。虽然这些末端执行器轨迹与具身无关，但它们的执行却不是。相反，执行关键依赖于由机器人形态决定的跟踪性能。因此，在此类数据上训练的策略对不同机器人的迁移效果并不相同。从这个角度看，不完美的迁移不仅是一个局限性——它也是一个机会。

在本工作中，我们研究运动条件的机器人协同设计。给定（i）一组目标末端执行器轨迹和（ii）一组用户定义的奖励函数，我们的目标是自动生成能够获得高奖励的**完整**机器人形态（图 1）。这里，完整的机器人形态意味着需要对包含运动学（Kinematics）、几何和动力学参数的大型异构设计空间进行推理。此外，用户指定的奖励可以是机器人形态（如尺寸、质量）和动态行为（如关节速度、跟踪精度）的函数。

为解决这一问题，我们提出 Transformer Transformer，这是一个扩散 Transformer（DiT），旨在成为运动条件机器人协同设计的一站式解决方案，具有以下贡献：

- **统一的机器人表示**：我们提出 RoboToken，这是一种能够表示任何关节式机器人（Articulated Robot）的具身、状态和动作的 token 化方案。RoboToken 设计用于以一致的格式灵活存储完整的机器人数据，为单一模型跨越多样化的机器人具身空间提供了基础性的表示能力。
- **统一的架构**：通过在 RoboToken 上使用不同的掩码建模（Masked Modeling）方案进行训练，我们的模型同时充当协同设计问题的生成器、评估器和控制器。通过将所有协同设计模块整合到单一神经网络中，我们的优化流程简单且易于 GPU 并行化。
- **统一的动力学训练目标**：我们的网络不是针对特定奖励信号进行专门化训练，而是训练为对多样化机器人具身的通用动力学进行建模。为了在推理时零样本优化用户指定的奖励函数，我们将模型的奖励无关动力学预测转换为针对特定奖励的分数预测。这个预测分数用于引导模型的具身扩散过程，以产生高价值的具身形态。

在三个机器人设计空间（即固定基座、四足和双臂移动操作机器人）中，我们展示了 Transformer Transformer 能够在推理时零样本优化用户指定的末端执行器轨迹和奖励函数。以机器人具身为条件，同一模型还可以用作跨具身全身控制器（Whole-Body Controller），直接用于设计验证。最后，我们通过制作一个针对双臂抛掷布料展开任务优化的 ALOHA 设计来验证模型的预测，跟踪误差降低了 $73\%$，最大关节速度降低了 $30\%$。请访问 [transformer-transformer.github.io](https://transformer-transformer.github.io/) 查看更多结果。

## 2 方法

![Refer to caption](https://arxiv.org/html/2607.25798v1/figures/tokenization_v2e.png)

图 2：统一的机器人表示。RoboToken 被设计为刚性关节式机器人的完整、可用于学习的 token 表示。我们的 tokenizer 可以将任何机器人描述转换为 RoboToken 序列，包括时不变的具身 token 和时变的动力学 token。

**概述**：Transformer Transformer 的总体目标是对机器人的具身和动力学（即状态和动作）的联合分布进行建模。为实现这一目标，我们首先描述 RoboToken（§ 2.1），这是一种能够表示来自不同机器人的具身和动力学的 token 化方案。接下来，我们将描述如何在这些 token 上训练扩散 Transformer（§ 2.2），以及其在运动到机器人生成和跨具身控制策略中的应用。为了实现更高效的采样，我们描述动力学自引导（§ 2.3），这是一种引导扩散过程以优化未见过的奖励函数的方法，无需训练任何额外网络。

**范围**：RoboToken 表示被设计用于表示刚性关节式机器人（与 MJCF 范围相同），针对机器人学习进行了优化。它目前限于基于基本几何体（Primitive）的几何表示。此外，我们专注于来自 UMI 演示的末端执行器轨迹作为操作任务表示，这种表示方式被广泛采用。最后，我们的目标是设计一个能够泛化到机器人具身、状态和控制的奖励函数的框架。涉及其他属性（如结构强度、外观）的奖励超出了本研究范围。

### 2.1 RoboToken：统一的机器人表示

**完整性**。RoboToken 包含机器人具身（时不变）和动力学（时变）的所有属性。tokenizer 将任何机器人描述转换为五种具身 token 类型：连杆（Links）、固定关节（Fixed Joints）、滑动/旋转关节（Sliding/Rotating Joints）、球关节（Ball Joints）和电机（Motors）（图 2，仅展示三种具身类型以便说明）。此外，tokenizer 还将每个状态和动作序列转换为四种状态 token 类型（除固定关节外的所有类型）以及动作 token。例如，每个连杆 token 包含连杆的物理参数（基本体类型、基本体尺寸、惯性张量），而连杆状态 token 包含给定时间步的连杆位姿（Pose）。在每种 token 类型内，所有数据属性被连接成一个可用于学习的连续值向量。

![Refer to caption](https://arxiv.org/html/2607.25798v1/figures/robotokens_v3.png)

图 3：RoboToken 扩散。Transformer Transformer 将噪声扩散到 RoboToken 中（token 数量用蓝色标注），比 MJCF 文本 token（token 数量用灰色标注）紧凑 27-110 倍。

PLACEHOLDER_FOR_FLEXIBLE
## 7 附加实验

![Refer to caption](https://arxiv.org/html/2607.25798v1/figures/bimanual_control_performance_v2.jpg)

图 11：双臂移动控制验证。对于每种具身形态，我们使用跨具身控制器（小型/大型）和预言机 Mink 27 控制器进行控制。我们的控制器与预言机的性能具有强相关性。换句话说，使用我们的跨具身控制器评估机器人设计，与使用预言机机器人评估的结果是一致的。

在机器人协同设计（Robot Co-design）的背景下，学习型跨具身控制器（Cross-Embodiment Controller）仅对那些受益于昂贵学习型控制器的机器人具身（如足式机器人）进行验证时具有实际价值。然而，配备快速（预言机）运动学控制器的机器人能够更快地生成数据，因此代表了研究跨具身控制的一个实用设计空间。在本节中，我们在训练统一模型时进行容量消融实验，涵盖跨具身控制（§ 7.1）和运动到机器人（§ 7.2）实验。

![Refer to caption](https://arxiv.org/html/2607.25798v1/x1.png)

图 12：模型容量消融。尽管较大的模型性能更好，但它们的推理时间也更长。

### 7.1 双臂移动跨具身控制

实验设置。我们训练两种模型规模，一个具有 1160 万参数的小型模型和一个具有 6360 万参数的大型模型，同时在运动到机器人任务和跨具身控制器任务上进行训练。我们使用来自 UMI [^1] 双臂洗碗数据集的 26 条验证轨迹，在移动双臂设计空间中程序化生成的机器人上评估控制器性能。对于每条轨迹，我们在机器人的每个离散变体上包含 5 个连续变体，总共生成 25 个机器人，执行 650 个回合。由于这些机器人是随机生成的，一些设计次优的具身形态在所有控制方法下都表现出较差的控制性能（因此跟踪误差较高）。

实验结果。我们在表 1 中报告了方程 1 中定义的跟踪奖励以及其他跟踪指标。在图 11 中，我们观察到我们的控制器的跟踪性能与预言机 Mink 控制器之间存在强正相关性。在归一化梯度步数后，我们发现较大模型在跟踪指标和 Pearson 相关系数方面都更接近预言机性能。然而，当归一化训练 FLOP 时，较小模型应该接受 $5.5\times$ 倍的训练步数。我们将此研究以及其他模型蒸馏技术留作未来工作。

### 7.2 双臂移动运动到机器人优化

实验设置。我们使用前一节（§ 7.1）中具有 1160 万和 6360 万参数的统一模型，并应用零阶优化器（Zeroth Order Optimizer）进行多轨迹优化。与主论文中一样，每个机器人同时针对 UMI 洗碗数据集中的所有 26 条验证轨迹进行优化，我们重复 9 个推理种子。与主论文不同的是，我们使用学习型控制器（§ 7.1）而非预言机控制器。我们将这些结果聚焦于"仅跟踪"奖励。

实验结果。从图 12 可以看出，大型模型性能更好。然而，对于相同数量的并行样本，大型模型需要 $4\times$ 倍的运行时间。

| 方法 | 位置误差 | 方向误差 | 存活率 | 奖励 |
| --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | $\uparrow$ |
| 本文方法（小型） | 6.6 | 10.7 | 98.9 | 884.9 |
| 本文方法（大型） | 5.9 | 9.6 | 99.5 | 957.0 |
| Mink（预言机） | 4.8 | 7.6 | 100.0 | 1064.3 |

表 1：控制器性能与预言机对比

## 8 主实验的附加指标

本节定义实验中使用的所有奖励项，并报告主论文图 5 中实验的奖励特定指标。

### 8.1 仅跟踪

奖励定义。对于给定的目标位姿轨迹 $\{p^{t}_{target},o^{t}_{target}\}_{t\in T}$ 和实际机器人末端执行器（End-effector）位姿轨迹 $\{p^{t}_{achieved},o^{t}_{achieved}\}_{t\in T}$，每条轨迹长度为 $T$，我们计算位置误差 $\epsilon^{t}_{p}$ 为 $||p^{t}_{target}-p^{t}_{achieved}||_{2}$，方向误差 $\epsilon^{t}_{o}$ 为 $\arccos\left(\frac{\operatorname{Tr}(R)-1}{2}\right)$，其中 $R$ 是从 $o^{t}_{achieved}$ 到 $o^{t}_{target}$ 的旋转矩阵。基于每个时间步的跟踪误差，长度为 $T$ 的回合的跟踪奖励为：

$$
\sum_{t}^{T}\exp\left(-\frac{{||\epsilon^{t}_{p}||}^{2}}{\sigma_{p}}-\frac{{||\epsilon^{t}_{o}||}^{2}}{\sigma_{o}}\right)
$$

直观来说，如果位置和方向误差都为零，奖励为 1；如果任一误差非零，奖励小于 1。$\sigma_{p}$ 和 $\sigma_{o}$ 决定了跟踪误差增加时的奖励衰减速度，在所有实验中分别设置为 $0.01$ 和 $0.5$。对负跟踪误差取指数是机器人强化学习中常用的奖励函数形式 [^68] [^73] [^31]。如果机器人翻倒（四足机器人情况）或 $\epsilon^{t}_{p}$ 超过最大允许位置误差阈值，回合将被终止。该阈值对 ViperX 和移动双臂设计空间设置为 50cm，对四足设计空间设置为 80cm。对于双臂情况，我们将 $\epsilon^{t}_{p}$ 和 $\epsilon^{t}_{o}$ 定义为两个末端执行器的跟踪位置和方向误差之和。这意味着只有当两个末端执行器的位置和方向误差都很低时，才能获得高奖励。

奖励特定指标。我们报告所有时间步和所有末端执行器的平均位置和方向跟踪误差、以秒为单位的优化时间以及存活率（未提前终止的回合比例）。对于每种方法，我们使用达到最高奖励的 $n$ 值。对于 CMA-ES，$n$ 是每条轨迹的最大展开数。对于零阶优化器和动力学自引导（DGS），$n$ 是样本数。

| 方法 | 位置误差 | 方向误差 | 存活率 | 优化时间 |
| --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | sec $\downarrow$ |
| 随机 | 19.4 | 8.9 | 72.2 | \- |
| CMA-ES | 5.0 | 5.7 | 98.3 | 47.5 |
| 零阶 | 4.1 | 4.2 | 96.1 | 0.5 |
| DGS | 4.1 | 3.9 | 99.4 | 2.8 |

表 2：ViperX，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 优化时间 |
| --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | sec $\downarrow$ |
| 随机 | 17.0 | 9.4 | 80.6 | \- |
| CMA-ES | 7.1 | 6.1 | 86.7 | 663.8 |
| 零阶 | 3.4 | 3.9 | 95.6 | 1.2 |
| DGS | 2.4 | 3.5 | 98.9 | 43.5 |

表 3：ViperX，多轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 优化时间 |
| --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | sec $\downarrow$ |
| 随机 | 3.4 | 8.4 | 96.1 | \- |
| CMA-ES | 1.9 | 5.8 | 100.0 | 265.5 |
| 零阶 | 2.6 | 7.4 | 99.4 | 0.8 |
| DGS | 2.6 | 7.4 | 99.4 | 5.0 |

表 4：四足机器人，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 优化时间 |
| --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | sec $\downarrow$ |
| 随机 | 2.8 | 4.4 | 99.6 | \- |
| CMA-ES | 1.7 | 2.5 | 100.0 | 11505.2 |
| 零阶 | 1.8 | 2.5 | 99.6 | 20.7 |
| DGS | 1.7 | 2.7 | 99.1 | 30.8 |

表 5：双臂机器人，多轨迹

### 8.2 跟踪扭矩

奖励定义。设 $\tau^{t}$ 为时间步 $t$ 时机器人所有电机的总扭矩（Torque）。跟踪扭矩奖励为：

$$
\sum_{t}^{T}\max\left(\exp\left(-\frac{{||\epsilon^{t}_{p}||}^{2}}{\sigma_{p}}-\frac{{||\epsilon^{t}_{o}||}^{2}}{\sigma_{o}}\right)-\alpha_{torque}||\tau^{t}||^{2},0\right)
$$

其中扭矩权重 $\alpha_{torque}$ 在所有实验中设置为 $5\times 10^{-5}$。如果扭矩过大，它将超过非负跟踪项的权重。避免负奖励是常见做法 [^68] [^73] [^31]，可防止大负奖励鼓励智能体提前终止回合的情况。

奖励特定指标。除了跟踪指标（§ 8.1），我们还报告平均 $||\tau^{t}||_{2}$。

| 方法 | 位置误差 | 方向误差 | 存活率 | 扭矩 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | Nm $\downarrow$ | sec $\downarrow$ |
| 随机 | 19.4 | 8.9 | 72.2 | 3.8 | \- |
| CMA-ES | 5.9 | 5.7 | 95.0 | 4.1 | 45.4 |
| 零阶 | 4.1 | 4.5 | 97.2 | 4.4 | 0.5 |
| DGS | 4.1 | 4.1 | 98.3 | 4.5 | 1.9 |

表 6：ViperX，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 扭矩 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | Nm $\downarrow$ | sec $\downarrow$ |
| 随机 | 17.0 | 9.4 | 80.6 | 4.0 | \- |
| CMA-ES | 7.5 | 5.9 | 89.4 | 4.4 | 644.4 |
| 零阶 | 3.4 | 3.9 | 95.6 | 4.6 | 1.2 |
| DGS | 2.5 | 3.5 | 98.9 | 4.6 | 43.4 |

表 7：ViperX，多轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 扭矩 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | Nm $\downarrow$ | sec $\downarrow$ |
| 随机 | 3.4 | 8.4 | 96.1 | 4.6 | \- |
| CMA-ES | 2.0 | 6.0 | 100.0 | 3.2 | 250.0 |
| 零阶 | 2.5 | 7.1 | 99.4 | 3.1 | 3.3 |
| DGS | 2.5 | 7.0 | 98.9 | 3.2 | 5.5 |

表 8：四足机器人，单轨迹

### 8.3 跟踪速度

奖励定义。此奖励遵循方程 2，将 $\alpha_{torque}||\tau^{t}||^{2}$ 替换为 $\alpha_{velocity}||\dot{q}^{t}||^{2}$，其中 $\dot{q}^{t}$ 是时间步 $t$ 时每个执行器的速度。$\alpha_{velocity}$ 对 ViperX 设置为 0.1，对移动双臂设计空间设置为 0.5，对四足设计空间设置为 0.005。

奖励特定指标。除了跟踪指标（§ 8.1），我们还报告平均 $||\dot{q}^{t}||_{2}$。

| 方法 | 位置误差 | 方向误差 | 存活率 | 速度 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | rad/s $\downarrow$ | sec $\downarrow$ |
| 随机 | 19.4 | 8.9 | 72.2 | 0.391 | \- |
| CMA-ES | 6.1 | 5.8 | 92.5 | 0.366 | 38.5 |
| 零阶 | 4.1 | 4.3 | 98.9 | 0.397 | 0.5 |
| DGS | 3.9 | 4.1 | 97.2 | 0.385 | 1.5 |

表 9：ViperX，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 速度 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | rad/s $\downarrow$ | sec $\downarrow$ |
| 随机 | 17.0 | 9.4 | 80.6 | 0.384 | \- |
| CMA-ES | 8.5 | 7.0 | 87.8 | 0.385 | 615.7 |
| 零阶 | 3.4 | 3.7 | 97.8 | 0.362 | 7.3 |
| DGS | 5.5 | 4.2 | 97.8 | 0.394 | 43.4 |

表 10：ViperX，多轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 速度 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | rad/s $\downarrow$ | sec $\downarrow$ |
| 随机 | 3.4 | 8.4 | 96.1 | 0.561 | \- |
| CMA-ES | 2.1 | 5.9 | 100.0 | 0.376 | 251.1 |
| 零阶 | 2.8 | 8.0 | 98.9 | 0.450 | 1.1 |
| DGS | 3.5 | 9.0 | 99.4 | 0.455 | 9.1 |

表 11：四足机器人，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 速度 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | rad/s $\downarrow$ | sec $\downarrow$ |
| 随机 | 2.8 | 4.4 | 99.6 | 0.252 | \- |
| CMA-ES | 1.8 | 2.4 | 99.6 | 0.188 | 11505.2 |
| 零阶 | 1.9 | 2.9 | 99.1 | 0.271 | 61.95 |
| DGS | 1.9 | 2.9 | 98.7 | 0.247 | 53.5 |

表 12：双臂机器人，多轨迹

### 8.4 跟踪尺寸

奖励定义。为了捕捉机器人尺寸 $s_{achieved}$ 的概念，我们使用机器人所有几何体长度的总和，其中几何体的长度是其最长维度的大小。由于不同类型的机器人尺寸差异很大，我们使用目标尺寸 $s_{target}$ 并将尺寸惩罚定义为：

$$
-T\alpha_{size}\min\left(s_{achieved}-s_{target},0\right)
$$

只有当机器人尺寸低于目标尺寸时，该项才为零，并将其添加到方程 1 中。我们对所有设计空间使用 $\alpha_{size}=0.1$，同时调整 $s_{target}$ 为：ViperX 设计空间 2.0m，四足设计空间 6.5m，移动双臂设计空间 5.0m。

引导尺度。由于扩散训练需要模型查看其前一扩散时间步的噪声 token 来预测如何去噪，我们发现一个 token 对自身的注意力显著高于对其他 token 的注意力。这意味着使用具身 token 的奖励（如尺寸、重量）的梯度也显著高于使用动力学 token 的奖励（如跟踪误差）的梯度。我们发现如果不考虑这种幅度差异，会导致扩散输出无效的机器人。为了解决这个问题，我们对引导项使用 $\alpha_{size}=0.005$，但对实际报告的奖励保持 $\alpha_{size}=0.1$。

奖励特定指标。除了跟踪指标（§ 8.1），我们还报告上述定义的机器人尺寸。

| 方法 | 位置误差 | 方向误差 | 存活率 | 尺寸 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | m $\downarrow$ | sec $\downarrow$ |
| 随机 | 19.4 | 8.9 | 72.2 | 2.35 | \- |
| CMA-ES | 6.2 | 6.3 | 94.4 | 2.21 | 38.3 |
| 零阶 | 4.3 | 4.3 | 96.7 | 2.31 | 0.5 |
| DGS | 4.6 | 4.4 | 96.1 | 2.19 | 1.9 |

表 13：ViperX，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 尺寸 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | m $\downarrow$ | sec $\downarrow$ |
| 随机 | 17.0 | 9.4 | 80.6 | 2.42 | \- |
| CMA-ES | 7.4 | 6.7 | 91.1 | 2.28 | 564.6 |
| 零阶 | 3.3 | 3.8 | 96.7 | 2.32 | 1.2 |
| DGS | 2.8 | 3.7 | 98.9 | 2.29 | 3.6 |

表 14：ViperX，多轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 尺寸 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | m $\downarrow$ | sec $\downarrow$ |
| 随机 | 3.4 | 8.4 | 96.1 | 9.25 | \- |
| CMA-ES | 2.3 | 7.2 | 100.0 | 7.28 | 263.3 |
| 零阶 | 4.2 | 11.6 | 94.4 | 7.40 | 3.2 |
| DGS | 4.6 | 12.6 | 94.4 | 7.17 | 9.0 |

表 15：四足机器人，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 尺寸 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | m $\downarrow$ | sec $\downarrow$ |
| 随机 | 2.8 | 4.4 | 99.6 | 4.84 | \- |
| CMA-ES | 1.8 | 2.7 | 100.0 | 4.79 | 11505.2 |
| 零阶 | 1.7 | 2.7 | 99.1 | 4.71 | 10.7 |
| DGS | 1.6 | 2.8 | 99.6 | 4.73 | 58.9 |

表 16：双臂机器人，多轨迹

### 8.5 跟踪重量

奖励定义。机器人的总质量 $m_{achieved}$ 是其所有几何体质量的总和。与方程 3 类似，我们设置目标质量 $m_{target}$，并按时间步数进行缩放：

$$
-T\alpha_{mass}\min\left(m_{achieved}-m_{target},0\right)
$$

对于 ViperX 设计空间，$\alpha_{mass}$ 设置为 10，目标重量为 3.2kg。该项添加到方程 1 中。

引导尺度。与 § 8.4 类似，我们使用 $\alpha_{mass}=0.005$ 来考虑 token 对自身的更高注意力。

奖励特定指标。除了仅跟踪指标（§ 8.1），我们还报告机器人的总质量。

| 方法 | 位置误差 | 方向误差 | 存活率 | 重量 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | kg $\downarrow$ | sec $\downarrow$ |
| 随机 | 19.4 | 8.9 | 72.2 | 3.2 | \- |
| CMA-ES | 13.2 | 6.3 | 80.6 | 3.2 | 41.8 |
| 零阶 | 8.7 | 6.3 | 91.7 | 3.0 | 1.0 |
| DGS | 9.2 | 6.1 | 92.2 | 3.0 | 2.9 |

表 17：ViperX，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 重量 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | kg $\downarrow$ | sec $\downarrow$ |
| 随机 | 17.0 | 9.4 | 80.6 | 3.3 | \- |
| CMA-ES | 17.4 | 5.6 | 73.3 | 3.2 | 516.4 |
| 零阶 | 8.6 | 5.1 | 97.2 | 3.1 | 7.3 |
| DGS | 8.1 | 5.7 | 91.7 | 3.1 | 45.3 |

表 18：ViperX，多轨迹

## 9 附加方法细节

| RoboToken 类型 | 属性 | 维度 | 编码 |
| --- | --- | --- | --- |
| 连杆（Link） | 几何体类型 | 3 | 二进制 |
|  | 几何体尺寸 | 3 | 无 |
|  | 质量 | 1 | 对数 |
|  | 惯性位置 | 3 | 无 |
|  | 惯性旋转 | 9 | 无 |
|  | 对角惯性 | 3 | 对数 |
|  | 摩擦 | 3 | 无 |
|  | 接触维度 | 3 | 二进制 |
|  | 颜色 | 4 | 无 |
|  | 自由连杆 ID | 1 | 二进制 |
|  | 跟踪连杆 ID | 1 | 二进制 |
|  | 连杆 ID | \- | 位置嵌入 |
| 动力学关节（Dynamic Joint） | 关节类型 | 2 | 二进制 |
|  | 范围 | 2 | 无 |
|  | 电枢 | 1 | 对数 |
|  | 阻尼 | 1 | 对数 |
|  | 摩擦损耗 | 1 | 对数 |
|  | 刚度 | 1 | 对数 |
|  | 弹簧参考 | 1 | 无 |
|  | 位置参考 | 1 | 无 |
|  | 到连杆的位置 | 3 | 无 |
|  | 到连杆的旋转 | 9 | 无 |
|  | 连杆 ID | 7 | 二进制 |
|  | 动力学关节 ID | \- | 位置嵌入 |
| 固定关节（Fixed Joint） | 位置 | 3 | 无 |
|  | 旋转 | 9 | 无 |
|  | 连杆 ID | 7 | 二进制 |
|  | 固定关节 ID | \- | 位置嵌入 |
| 执行器（Actuator） | 控制范围 | 2 | 无 |
|  | 力范围 | 4 | 有符号对数 |
|  | 位置增益 | 1 | 对数 |
|  | 速度增益 | 1 | 对数 |
|  | 齿轮比 | 1 | 无 |
|  | 执行器类型 | 1 | 二进制 |
|  | 动力学关节 ID | 6 | 二进制 |
|  | 执行器 ID | \- | 位置嵌入 |
| 自由/跟踪连杆观测 | 位置 | 3 | 无 |
|  | 旋转 | 9 | 无 |
|  | 自由/跟踪连杆 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 动力学关节观测 | 关节位置 | 1 | 无 |
|  | 关节速度 | 1 | 无 |
|  | 动力学关节 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 执行器观测 | 速度 | 1 | 无 |
|  | 力 | 1 | 无 |
|  | 执行器 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 控制（Control） | 目标位置 | 1 | 无 |
|  | 执行器 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 目标位姿（Target Pose） | 位置 | 3 | 无 |
|  | 旋转 | 9 | 无 |
|  | 跟踪连杆 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |

表 19：RoboToken 模式。此处未列出球形关节，其模式与动力学关节相同，但其观测值为四元数和角速度。

### 9.1 RoboToken

Token 数量比较。主论文图 4 中的文本 token 数量是通过 [GPT-4o 的 tokenizer](https://platform.openai.com/tokenizer) 对机器人的 MJCF 格式进行 token 化计算得出的。我们与文本 token 进行比较，因为这些是唯一可以直接作为神经网络输入和输出的完整机器人表示。

惯性分割。为了保持 RoboToken 的一致性，我们要求所有惯性属性严格来自连杆的几何原语。当用户提供的机器人描述包含具有多个几何体但只有单个集总惯性张量的连杆时，我们应用启发式算法将这个集总惯性分割为各个几何体的惯性。

1. 我们假设连杆内所有原语的密度 $\rho$ 均匀。
2. 对于每个原语 $i$，我们根据其几何形状（例如，盒子、球体或圆柱体）计算其局部惯性 $I_{i}$ 和质量 $m_{i}$。
3. 我们应用平行轴定理（Parallel Axis Theorem）[^87] 将这些惯性转移到连杆的公共质心：
   $$
   I_{\text{total}}=\sum(I_{i}+m_{i}[(\mathbf{r}_{i}\cdot\mathbf{r}_{i})\mathbf{E}-\mathbf{r}_{i}\otimes\mathbf{r}_{i}])
   $$
   其中 $\mathbf{r}_{i}$ 是从原语中心到连杆中心的位移向量。

变换规范化。为了确保模型不必学习冗余的空间偏移，我们对所有 MJCF/URDF 文件进行预处理，将所有变换折叠到关节 token 的变换中。这意味着所有几何体实际上都在其连杆坐标系的原点。

属性编码。机器人具身属性可以跨越不同的数据类型（布尔值、枚举、整数、连续值）和范围。对于所有布尔/枚举/整数属性（如关节类型、几何体类型或 ID 属性），我们使用二进制编码。对于跨越多个数量级的连续值属性（如惯性、电机增益），我们使用对数或有符号对数编码。我们在表 19 中包含了 RoboToken 模式以及每个属性的编码。

上下文长度考虑。由于自注意力机制的复杂度为 $O(n^{2})$（关于序列长度），仔细确定模型的上下文长度对于训练和推理速度至关重要。因此，我们移除了除两种类型连杆之外的所有连杆位姿观测。第一种是自由基座机器人（如四足机器人）中的自由连杆。第二种是末端执行器，在模式中称为"跟踪连杆"（表 19），以允许泛化到其他机器人身体部位。由于机器人包含的连杆数量远多于通常与任务相关的连杆数量，因此省略所有这些其他连杆可以显著减少输入 token 的动力学 token 部分的上下文长度。对于每个设计空间，我们使用该设计空间的最大序列长度进行训练。

| 超参数 | 值 |
| --- | --- |
| 隐藏维度 | 256 |
| 层数 | 8 |
| 注意力头数 | 4 |
| Dropout | 0.0 |
| 学习率 | $5\times 10^{-3}$ |
| 学习率预热 | 500 步 |
| 批量大小 | 64 |
| 权重衰减 | 0.0 |
| EMA 幂次 | 0.75 |
| 训练轮数 | 50 |
| 每轮步数 | 16,384 |

表 20：模型超参数

### 9.2 Transformer Transformer

超参数。我们的模型和训练超参数列于表 20。我们发现在训练统一模型时，较大的模型训练速度显著更快。我们选择将隐藏维度和注意力头数加倍，并将层数增加到 12（大型模型，图 11）。为了帮助这个较大模型稳定训练，我们使用较小的学习率 $1\times 10^{-4}$。

关节 Token 排序。我们用两个具身 token 表示每个关节，每个部分负责指向一个连杆并编码到该连杆的变换。关节的所有其他属性在两个 token 之间重复。为了帮助扩散模型区分每个关节的两个 token，我们添加了一个关节排序 ID 属性，对于连杆 ID 较高的 token，该属性为 1。关节顺序 ID 被转换为可学习的位置嵌入并添加到关节 token 中。

动力学模型时间分辨率。我们尝试在模型上下文中包含更多回合时间步，假设增加的时间分辨率将允许模型学习更准确的动力学模型。然而，这些模型并未显著改善下游硬件优化性能，但它们显著增加了训练和推理时间。因此，我们选择了 8 个时间步，我们在实验中观察到这在速度和性能之间取得了最佳平衡。

扩散过程长度。我们还发现，虽然建模动力学受益于更长的扩散过程，但使用较短的扩散过程建模动作就足够了。具体来说，用于下游奖励引导扩散的精确状态预测，特别是对于跟踪误差目标，对于正确引导至关重要，而更长的扩散过程可以实现更低的预测误差。同时，动作预测对专家的小偏差具有鲁棒性。因此，我们对运动到机器人和跨具身控制分别使用 100 和 5 个时间步。

显式填充 Token。对于运动到机器人任务，模型可以根据将生成的机器人的连杆、关节和执行器数量生成可变长度的序列，因此模型被训练（在注意力机制中不使用掩码）来显式预测填充 token，这些是我们的去 token 化器可以检测到的特殊值 token。同时，对于跨具身控制，所有填充 token 都被屏蔽掉。

检查点选择。我们使用最佳检查点，通常是最后几个检查点之一。我们发现，尽管运动到机器人优化性能在训练早期快速提高，但跨具身控制性能仅在后期检查点才达到平台期。

### 9.3 数据

#### 9.3.1 程序化生成

我们定义了一个程序化生成语法，从公共根组件向外生长机器人。我们扩展了 MuJoCo MJCF XML 格式，以支持自定义数据 XML 字段中的元级语言，并注入解析器来处理这些元级字段。这种额外的元级功能允许用户为子组件定义随机化的连续属性（例如，腿的尺寸）、属性之间的约束（例如，左小腿的长度应等于右小腿的长度），以及随机化的离散属性（例如，关节的旋转轴，是否包含弹簧加载的腿部连杆机构）。因此，给定一个程序化机器人设计空间，每个机器人具身可以通过离散选择列表和连续选择列表进行参数化，其中两个列表的长度都是可变的（某些离散选择可以改变机器人的自由度）。使用我们的系统，现有的机器人定义可以轻松扩展以支持广泛的程序化几何、运动学和动力学变化，从而生成我们数据集中具身多样性的来源。

ViperX。对于离散变化，我们包括 4 种不同的安装方向（左、右、直立、向下），3 种不同的自由度变化（5 自由度、6 自由度和 7 自由度），以及每个上部关节的 3 种关节旋转轴变化（X 轴、Y 轴和 Z 轴旋转）。对于连续变化，我们包括安装位置（XYZ 方向分别在 0.7m、1.0m 和 0.6m 内），安装 Z 角（$-\pi/2\,\text{rad}$ 到 $+\pi/2\,\text{rad}$），以及连杆长度变化（相对于初始长度 $-5\,\text{cm}$ 到 $+20\,\text{cm}$）。

四足操作臂。对于离散变化，我们包括 2 种手臂安装选项（固定或沿背部滑动的线性导轨），每条腿 2 种腿部设计选项（弹簧加载连杆或串联腿，4 条腿独立选择），以及膝盖方向（向前翻转与向后翻转，前对和后对内部共享），总共 7 个二元设计选择。对于连续变化，我们包括手臂连杆长度变化（相对于初始长度 $-10\,\text{cm}$ 到 $40\,\text{cm}$），手臂安装位置（$-20\,\text{cm}$ 到 $+20\,\text{cm}$），身体长度（$10\,\text{cm}$ 到 $50\,\text{cm}$），腿部连杆长度（$20\,\text{cm}$ 到 $60\,\text{cm}$），每条腿的腿部安装 Z 角（$-0.7\,\text{rad}$ 到 $0.7\,\text{rad}$），电池放置沿机器人长度方向（机器人核心的重型绿色盒子，$-20\,\text{cm}$ 到 $+20\,\text{cm}$）。

移动双臂。对于离散变化，我们包括三种脊柱设计选择（固定脊柱、伸缩滑动脊柱和弯曲脊柱）以及躯干 $45^{\circ}$ 俯仰自由度（包含或不包含，允许机器人向前倾斜）。对于连续变化，我们包括脊柱长度变化（固定脊柱 $60\,\text{cm}$ 到 $140\,\text{cm}$，伸缩滑动脊柱 $10\,\text{cm}$ 到 $50\,\text{cm}$，弯曲脊柱 $20\,\text{cm}$ 到 $80\,\text{cm}$），肩部偏移（y 方向 $4\,\text{cm}$ 到 $10\,\text{cm}$，z 方向 $-3\,\text{cm}$ 到 $8\,\text{cm}$），以及手臂连杆长度（肩部连杆 $2\,\text{cm}$ 到 $8\,\text{cm}$，大臂和前臂 $1\,\text{cm}$ 到 $18\,\text{cm}$）。

ALOHA。对于离散变化，我们包括三种安装点方向（直立面对面、垂直安装在两侧或倒置）。对于连续变化，我们包括安装位置变化（x 方向 $-40\,\text{cm}$ 到 $0\,\text{cm}$，手臂间距离 $40\,\text{cm}$ 到 $60\,\text{cm}$，z 高度 $60\,\text{cm}$ 到 $90\,\text{cm}$），以及手臂长度变化（手臂延伸 $10\,\text{cm}$ 到 $50\,\text{cm}$，前臂延伸 $5\,\text{cm}$ 到 $25\,\text{cm}$）。我们将双臂设置约束为关于 XZ 平面对称。

#### 9.3.2 全身控制器（WBC）训练

遵循 UMI on Legs [^31] 的全身控制器（Whole-Body Controller, WBC）公式，我们使用 PPO [^30] 训练我们的 WBC。这遵循标准的强化学习（Reinforcement Learning, RL）控制器训练程序，使用任务奖励（在我们的例子中是跟踪，方程 1）加上正则化惩罚项（关节速度、加速度、限制、身体方向等）以及训练中的扰动（随机踢击、传送）以使策略更加鲁棒。除了机器人的状态外，这些强化学习专家还观察机器人具身的连续变化参数（§ 9.3.1）以及未来 4 个目标位姿。在每个回合开始时，从训练集的末端执行器轨迹中选择一条随机轨迹，应用随机变换增强，然后控制机器人跟踪目标轨迹。如果机器人翻倒，回合将被终止，并应用终止惩罚。我们为每个离散选择训练一个强化学习专家，对于四足机器人的 7 个二元设计选择，这产生了 128 个专门的专家。训练每个强化学习策略在 NVIDIA A100 上需要 16 小时。在数据生成时，我们从随机化的离散和连续选择流中程序化生成机器人，加载相应的强化学习策略，并控制机器人跟踪训练轨迹。然后将每次展开 token 化为 RoboTokens 数据集。我们向专家动作添加控制扰动以增加状态多样性，但记录未扰动的专家动作用于监督 [^88]。

#### 9.3.3 数据质量

我们通过两种方式确保训练数据集中的高跟踪性能。首先，我们过滤所有持续时间少于 100 个时间步（即 2 秒）的回合，这代表了在此期间翻倒（对于四足机器人）或超过跟踪位置误差终止阈值的具身。其次，我们将数据生成偏向于 ViperX 和 ALOHA 设计空间中性能更高的具身。虽然四足和移动双臂设计空间的跟踪误差是合理的，但随机 ViperX 和 ALOHA 具身的跟踪误差非常高（$19.4\,\text{cm}$、$8.9^{\circ}$，表 2），因为固定基座手臂的运动学关键决定了目标位姿是否甚至可达。为了提高训练具身质量，我们在训练轨迹上运行 CMA-ES，种群大小为 5，迭代 3 代，并使用 CMA-ES 期间采样的所有具身作为训练数据。尽管提高训练数据质量是 CMA-ES 与我们方法配对的一种方式，但我们认为它们的整合也可以在推理时发生，其中我们的算法充当进化算法的采样器。我们将此研究留作未来工作。

#### 9.3.4 数据集规模

我们的 ViperX 数据集包含 380 万个回合，总共 20 亿个时间步。我们的四足数据集包含 130 万个回合，总共 5 亿个时间步。最后，我们的双臂移动数据集包含 5 万个回合和 6900 万个时间步。在每个数据集中，每个机器人具身设计用于 10 个回合，因此 ViperX、四足和双臂设计空间的训练具身总数分别为 38 万、13 万和 5 千个。
| CMA-ES | 13.2 | 6.3 | 80.6 | 3.2 | 41.8 |
| 零阶 | 8.7 | 6.3 | 91.7 | 3.0 | 1.0 |
| DGS | 9.2 | 6.1 | 92.2 | 3.0 | 2.9 |

表 17：ViperX，单轨迹

| 方法 | 位置误差 | 方向误差 | 存活率 | 重量 | 优化时间 |
| --- | --- | --- | --- | --- | --- |
|  | cm $\downarrow$ | deg $\downarrow$ | % $\uparrow$ | kg $\downarrow$ | sec $\downarrow$ |
| 随机 | 17.0 | 9.4 | 80.6 | 3.3 | \- |
| CMA-ES | 17.4 | 5.6 | 73.3 | 3.2 | 516.4 |
| 零阶 | 8.6 | 5.1 | 97.2 | 3.1 | 7.3 |
| DGS | 8.1 | 5.7 | 91.7 | 3.1 | 45.3 |

表 18：ViperX，多轨迹

## 9 附加方法细节

| RoboToken 类型 | 属性 | 维度 | 编码 |
| --- | --- | --- | --- |
| 连杆（Link） | 几何体类型 | 3 | 二进制 |
|  | 几何体尺寸 | 3 | 无 |
|  | 质量 | 1 | 对数 |
|  | 惯性位置 | 3 | 无 |
|  | 惯性旋转 | 9 | 无 |
|  | 对角惯性 | 3 | 对数 |
|  | 摩擦 | 3 | 无 |
|  | 接触维度 | 3 | 二进制 |
|  | 颜色 | 4 | 无 |
|  | 自由连杆 ID | 1 | 二进制 |
|  | 跟踪连杆 ID | 1 | 二进制 |
|  | 连杆 ID | \- | 位置嵌入 |
| 动力学关节（Dynamic Joint） | 关节类型 | 2 | 二进制 |
|  | 范围 | 2 | 无 |
|  | 电枢 | 1 | 对数 |
|  | 阻尼 | 1 | 对数 |
|  | 摩擦损耗 | 1 | 对数 |
|  | 刚度 | 1 | 对数 |
|  | 弹簧参考 | 1 | 无 |
|  | 位置参考 | 1 | 无 |
|  | 到连杆的位置 | 3 | 无 |
|  | 到连杆的旋转 | 9 | 无 |
|  | 连杆 ID | 7 | 二进制 |
|  | 动力学关节 ID | \- | 位置嵌入 |
| 固定关节（Fixed Joint） | 位置 | 3 | 无 |
|  | 旋转 | 9 | 无 |
|  | 连杆 ID | 7 | 二进制 |
|  | 固定关节 ID | \- | 位置嵌入 |
| 执行器（Actuator） | 控制范围 | 2 | 无 |
|  | 力范围 | 4 | 有符号对数 |
|  | 位置增益 | 1 | 对数 |
|  | 速度增益 | 1 | 对数 |
|  | 齿轮比 | 1 | 无 |
|  | 执行器类型 | 1 | 二进制 |
|  | 动力学关节 ID | 6 | 二进制 |
|  | 执行器 ID | \- | 位置嵌入 |
| 自由/跟踪连杆观测 | 位置 | 3 | 无 |
|  | 旋转 | 9 | 无 |
|  | 自由/跟踪连杆 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 动力学关节观测 | 关节位置 | 1 | 无 |
|  | 关节速度 | 1 | 无 |
|  | 动力学关节 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 执行器观测 | 速度 | 1 | 无 |
|  | 力 | 1 | 无 |
|  | 执行器 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 控制（Control） | 目标位置 | 1 | 无 |
|  | 执行器 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |
| 目标位姿（Target Pose） | 位置 | 3 | 无 |
|  | 旋转 | 9 | 无 |
|  | 跟踪连杆 ID | \- | 位置嵌入 |
|  | 时间 ID | \- | 位置嵌入 |

表 19：RoboToken 模式。此处未列出球形关节，其模式与动力学关节相同，但其观测值为四元数和角速度。

### 9.4 推理

**随机 DDIM 采样**。DDIM [^22] 有一个超参数用于控制采样过程中注入的噪声量，在 DDIM 实现中通常表示为 $\eta$。尽管确定性 DDIM 采样（$\eta=0.0$）很常见，但我们发现对于动力学自引导（Dynamics Self-Guidance, DGS）而言，使用 $\eta=1.0$ 的随机采样能够显著提升运动到机器人的优化性能。与此同时，调整 $\eta$ 对我们的零阶采样器的性能没有影响。这表明，与所有一阶方法一样，我们的 DGS 采样器容易陷入局部最小值，但注入逐渐衰减的噪声项有助于在扩散过程中进行探索。

**引导尺度**。对于 DGS，我们使用最高的引导尺度，但不会将扩散模型推出分布从而生成无效的机器人。对于 ViperX、四足和双臂设计空间，引导尺度分别设置为 50、100 和 0.2，而在多轨迹 ViperX 实验中提高到 500。

[^1]: C. Chi, Z. Xu, C. Pan, E. Cousineau, B. Burchfiel, S. Feng, R. Tedrake, and S. Song. Universal manipulation interface: In-the-wild robot teaching without in-the-wild robots. In *Proceedings of Robotics: Science and Systems (RSS)*, 2024.

[^2]: Z. Liu, C. Chi, E. Cousineau, N. Kuppuswamy, B. Burchfiel, and S. Song. Maniwav: Learning robot manipulation from in-the-wild audio-visual data. *arXiv preprint arXiv:2406.19464*, 2024.

[^3]: F. Lin, Y. Hu, P. Sheng, C. Wen, J. You, and Y. Gao. Data scaling laws in imitation learning for robotic manipulation. *arXiv preprint arXiv:2410.18647*, 2024.

[^4]: Z. Wu, T. Wang, C. Guan, Z. Jia, S. Liang, H. Song, D. Qu, D. Wang, Z. Wang, N. Cao, et al. Fast-umi: A scalable and hardware-independent universal manipulation interface. *arXiv preprint arXiv:2409.19499*, 2024.

[^5]: M. Seo, H. A. Park, S. Yuan, Y. Zhu,, and L. Sentis. Legato: Cross-embodiment imitation using a grasping tool. *IEEE Robotics and Automation Letters (RA-L)*, 2025.

[^6]: F. Liu, C. Li, Y. Qin, A. Shaw, J. Xu, P. Abbeel, and R. Chen. Vitamin: Learning contact-rich tasks through robot-free visuo-tactile manipulation interface. *arXiv preprint arXiv:2504.06156*, 2025.

[^7]: M. Xu, H. Zhang, Y. Hou, Z. Xu, L. Fan, M. Veloso, and S. Song. Dexumi: Using human hand as the universal manipulation interface for dexterous manipulation. *arXiv preprint arXiv:2505.21864*, 2025.

[^8]: T. Tao, M. K. Srirama, J. J. Liu, K. Shaw, and D. Pathak. Dexwild: Dexterous human interactions for in-the-wild robot policies. *Robotics: Science and Systems (RSS)*, 2025.

[^9]: X. Zhu, B. Huang, and Y. Li. Touch in the wild: Learning fine-grained manipulation with a portable visuo-tactile gripper. *arXiv preprint arXiv:2507.15062*, 2025.

[^10]: G. Lee, Y. Lee, K. Kim, S. Lee, S. Noh, S. Back, and K. Lee. Manipforce: Force-guided policy learning with frequency-aware representation for contact-rich manipulation. *arXiv preprint arXiv:2509.19047*, 2025.

[^11]: O. Rayyan, J. Abanes, M. Hafez, A. Tzes, and F. Abu-Dakka. Mv-umi: A scalable multi-view interface for cross-embodiment learning. *arXiv preprint arXiv:2509.18757*, 2025.

[^12]: Y. Xu, L. Wei, P. An, Q. Zhang, and Y.-L. Li. exumi: Extensible robot teaching system with action-aware task-agnostic tactile representation. In *Conference on Robot Learning*, pages 2536–2554. PMLR, 2025.

[^13]: H. Gupta, X. Guo, H. Ha, C. Pan, M. Cao, D. Lee, S. Sherer, S. Song, and G. Shi. Umi-on-air: Embodiment-aware guidance for embodiment-agnostic visuomotor policies, 2025. URL [https://arxiv.org/abs/2510.02614](https://arxiv.org/abs/2510.02614).

[^14]: W. Peebles and S. Xie. Scalable diffusion models with transformers. In *Proceedings of the IEEE/CVF international conference on computer vision*, pages 4195–4205, 2023.

[^15]: H. Furuta, Y. Iwasawa, Y. Matsuo, and S. S. Gu. A system for morphology-task generalization via unified representation and behavior distillation. *arXiv preprint arXiv:2211.14296*, 2022.

[^16]: C. Schaff, D. Yunis, A. Chakrabarti, and M. R. Walter. Jointly learning to construct and control agents using deep reinforcement learning. In *2019 international conference on robotics and automation (ICRA)*, pages 9798–9805. IEEE, 2019.

[^17]: V. Kurin, M. Igl, T. Rocktäschel, W. Boehmer, and S. Whiteson. My body is a cage: the role of morphology in graph-based incompatible control. *arXiv preprint arXiv:2010.01856*, 2020.

[^18]: A. Gupta, L. Fan, S. Ganguli, and L. Fei-Fei. Metamorph: Learning universal controllers with transformers. *arXiv preprint arXiv:2203.11931*, 2022.

[^19]: H. Lu, Z. Wu, J. Xing, J. Li, R. Li, Z. Li, and Y. Shi. Bodygen: Advancing towards efficient embodiment co-design. *arXiv preprint arXiv:2503.00533*, 2025.

[^20]: A. Patel and S. Song. Get-zero: Graph embodiment transformer for zero-shot embodiment generalization. In *2025 IEEE International Conference on Robotics and Automation (ICRA)*, pages 14262–14269. IEEE, 2025.

[^21]: T. Wang, Y. Zhou, S. Fidler, and J. Ba. Neural graph evolution: Towards efficient automatic robot design. *arXiv preprint arXiv:1906.05370*, 2019.

[^22]: J. Song, C. Meng, and S. Ermon. Denoising diffusion implicit models. In *International Conference on Learning Representations*.

[^23]: X. Xu, H. Ha, and S. Song. Dynamics-guided diffusion model for robot manipulator design. *arXiv preprint arXiv:2402.15038*, 2024.

[^24]: T.-H. J. Wang, J. Zheng, P. Ma, Y. Du, B. Kim, A. Spielberg, J. Tenenbaum, C. Gan, and D. Rus. Diffusebot: Breeding soft robots with physics-augmented generative diffusion models. *Advances in Neural Information Processing Systems*, 36:44398–44423, 2023.

[^25]: P. Dhariwal and A. Nichol. Diffusion models beat gans on image synthesis. *Advances in neural information processing systems*, 34:8780–8794, 2021.

[^26]: H. Choi, Y. Hou, C. Pan, S. Hong, A. Patel, X. Xu, M. R. Cutkosky, and S. Song. In-the-wild compliant manipulation with umi-ft. *arXiv preprint arXiv:2601.09988*, 2026.

[^27]: K. Zakka. Mink: Python inverse kinematics based on MuJoCo, Dec. 2025. URL [https://github.com/kevinzakka/mink](https://github.com/kevinzakka/mink).

[^28]: E. Todorov, T. Erez, and Y. Tassa. Mujoco: A physics engine for model-based control. In *2012 IEEE/RSJ international conference on intelligent robots and systems*, pages 5026–5033. IEEE, 2012.

[^29]: R. S. Sutton, A. G. Barto, et al. *Reinforcement learning: An introduction*, volume 1. MIT press Cambridge, 1998.

[^30]: J. Schulman, F. Wolski, P. Dhariwal, A. Radford, and O. Klimov. Proximal policy optimization algorithms. *arXiv preprint arXiv:1707.06347*, 2017.

[^31]: H. Ha, Y. Gao, Z. Fu, J. Tan, and S. Song. UMI on legs: Making manipulation policies mobile with manipulation-centric whole-body controllers. In *Proceedings of the 2024 Conference on Robot Learning*, 2024.

[^32]: Z. Fu, X. Cheng, and D. Pathak. Deep whole-body control: learning a unified policy for manipulation and locomotion. In *Conference on Robot Learning*, pages 138–149. PMLR, 2023.

[^33]: M. Ji, X. Peng, F. Liu, J. Li, G. Yang, X. Cheng, and X. Wang. Exbody2: Advanced expressive humanoid whole-body control. *arXiv preprint arXiv:2412.13196*, 2024.

[^34]: M. Liu, Z. Chen, X. Cheng, Y. Ji, R.-Z. Qiu, R. Yang, and X. Wang. Visual whole-body control for legged loco-manipulation. *arXiv preprint arXiv:2403.16967*, 2024.

[^35]: T. Portela, A. Cramariuc, M. Mittal, and M. Hutter. Whole-body end-effector pose tracking. In *2025 IEEE International Conference on Robotics and Automation (ICRA)*, pages 11205–11211. IEEE, 2025.

[^36]: T. Portela, G. B. Margolis, Y. Ji, and P. Agrawal. Learning force control for legged manipulation. In *2024 IEEE International Conference on Robotics and Automation (ICRA)*, pages 15366–15372. IEEE, 2024.

[^37]: H. Xue, C. Pan, Z. Yi, G. Qu, and G. Shi. Full-order sampling-based mpc for torque-level locomotion control via diffusion-style annealing. In *2025 IEEE International Conference on Robotics and Automation (ICRA)*, pages 4974–4981. IEEE, 2025.

[^38]: K. Zakka, Y. Tassa, and MuJoCo Menagerie Contributors. MuJoCo Menagerie: A collection of high-quality simulation models for MuJoCo, 2022. URL [http://github.com/google-deepmind/mujoco/_menagerie](http://github.com/google-deepmind/mujoco_menagerie).

[^39]: Y. Song and S. Ermon. Generative modeling by estimating gradients of the data distribution. *Advances in neural information processing systems*, 32, 2019.

[^40]: C. He, X. Liu, G. M. S. Camps, J. Bruno, G. A. Sartoretti, and M. Schwager. Demystifying robot diffusion policies: Action memorization and a simple lookup table alternative. In *The Fourteenth International Conference on Learning Representations*, 2026.

[^41]: N. Hansen and A. Ostermeier. Completely derandomized self-adaptation in evolution strategies. *Evolutionary computation*, 9(2):159–195, 2001.

[^42]: N. Hansen, Y. Akimoto, and P. Baudis. CMA-ES/pycma on Github. Zenodo, DOI:10.5281/zenodo.2559634, Feb. 2019. URL [https://doi.org/10.5281/zenodo.2559634](https://doi.org/10.5281/zenodo.2559634).

[^43]: T. Chen, Z. He, and M. Ciocarlie. Hardware as policy: Mechanical and computational co-optimization using deep reinforcement learning. *arXiv preprint arXiv:2008.04460*, 2020.

[^44]: J. Xu, T. Chen, L. Zlokapa, M. Foshey, W. Matusik, S. Sueda, and P. Agrawal. An end-to-end differentiable framework for contact-aware robot design. *arXiv preprint arXiv:2107.07501*, 2021.

[^45]: C. Rajani, K. Arndt, D. Blanco-Mulero, K. S. Luck, and V. Kyrki. Co-imitation: learning design and behaviour by imitation. In *Proceedings of the AAAI Conference on Artificial Intelligence*, volume 37, pages 6200–6208, 2023.

[^46]: N. Liu, S. Li, Y. Du, A. Torralba, and J. B. Tenenbaum. Compositional visual generation with composable diffusion models. In *European conference on computer vision*, pages 423–439. Springer, 2022.

[^47]: Y. Du, C. Durkan, R. Strudel, J. B. Tenenbaum, S. Dieleman, R. Fergus, J. Sohl-Dickstein, A. Doucet, and W. S. Grathwohl. Reduce, reuse, recycle: Compositional generation with energy-based diffusion models and mcmc. In *International conference on machine learning*, pages 8489–8510. PMLR, 2023.

[^48]: M. Kodnongbua, I. G. Y. Lou, J. Lipton, and A. Schulz. Computational design of passive grippers. *arXiv preprint arXiv:2306.03174*, 2023.

[^49]: H. Ha, S. Agrawal, and S. Song. Fit2form: 3d generative model for robot gripper form design. In *Conference on Robot Learning*, pages 176–187. PMLR, 2021.

[^50]: M. Li, R. Antonova, D. Sadigh, and J. Bohg. Learning tool morphology for contact-rich manipulation tasks with differentiable simulation. *arXiv preprint arXiv:2211.02201*, 2022.

[^51]: R. Liu, J. Liang, S. Sudhakar, H. Ha, C. Chi, S. Song, and C. Vondrick. Paperbot: Learning to design real-world tools using paper, 2024.

[^52]: K. R. Allen, T. Lopez-Guevara, K. Stachenfeld, A. Sanchez-Gonzalez, P. Battaglia, J. Hamrick, and T. Pfaff. Physical design using differentiable learned simulators. *arXiv preprint arXiv:2202.00728*, 2022.

[^53]: A. Khazatsky, K. Pertsch, S. Nair, A. Balakrishna, S. Dasari, S. Karamcheti, S. Nasiriany, M. K. Srirama, L. Y. Chen, K. Ellis, P. D. Fagan, J. Hejna, M. Itkina, M. Lepert, Y. J. Ma, P. T. Miller, J. Wu, S. Belkhale, S. Dass, H. Ha, A. Jain, A. Lee, Y. Lee, M. Memmel, S. Park, I. Radosavovic, K. Wang, A. Zhan, K. Black, C. Chi, K. B. Hatch, S. Lin, J. Lu, J. Mercat, A. Rehman, P. R. Sanketi, A. Sharma, C. Simpson, Q. Vuong, H. R. Walke, B. Wulfe, T. Xiao, J. H. Yang, A. Yavary, T. Z. Zhao, C. Agia, R. Baijal, M. G. Castro, D. Chen, Q. Chen, T. Chung, J. Drake, E. P. Foster, J. Gao, V. Guizilini, D. A. Herrera, M. Heo, K. Hsu, J. Hu, M. Z. Irshad, D. Jackson, C. Le, Y. Li, K. Lin, R. Lin, Z. Ma, A. Maddukuri, S. Mirchandani, D. Morton, T. Nguyen, A. O'Neill, R. Scalise, D. Seale, V. Son, S. Tian, E. Tran, A. E. Wang, Y. Wu, A. Xie, J. Yang, P. Yin, Y. Zhang, O. Bastani, G. Berseth, J. Bohg, K. Goldberg, A. Gupta, A. Gupta, D. Jayaraman, J. J. Lim, J. Malik, R. Martín-Martín, S. Ramamoorthy, D. Sadigh, S. Song, J. Wu, M. C. Yip, Y. Zhu, T. Kollar, S. Levine, and C. Finn. Droid: A large-scale in-the-wild robot manipulation dataset. 2024.

[^54]: OpenAI. Learning to reason with LLMs, Sept. 2024. URL [https://openai.com/index/learning-to-reason-with-llms/](https://openai.com/index/learning-to-reason-with-llms/).

[^55]: D. Guo, D. Yang, H. Zhang, J. Song, R. Zhang, R. Xu, Q. Zhu, S. Ma, P. Wang, X. Bi, et al. Deepseek-r1: Incentivizing reasoning capability in llms via reinforcement learning. *arXiv preprint arXiv:2501.12948*, 2025.

[^56]: G. Comanici, E. Bieber, M. Schaekermann, I. Pasupat, N. Sachdeva, I. Dhillon, M. Blistein, O. Ram, D. Zhang, E. Rosen, et al. Gemini 2.5: Pushing the frontier with advanced reasoning, multimodality, long context, and next generation agentic capabilities. *arXiv preprint arXiv:2507.06261*, 2025.

[^57]: B. Brown, J. Juravsky, R. Ehrlich, R. Clark, Q. V. Le, C. Ré, and A. Mirhoseini. Large language monkeys: Scaling inference compute with repeated sampling. *arXiv preprint arXiv:2407.21787*, 2024.

[^58]: M. Ballon, A. Algaba, and V. Ginis. The relationship between reasoning and performance in large language models–o3 (mini) thinks harder, not longer. *arXiv preprint arXiv:2502.15631*, 2025.

[^59]: Y. Wu, Z. Sun, S. Li, S. Welleck, and Y. Yang. Inference scaling laws: An empirical analysis of compute-optimal inference for problem-solving with language models. *arXiv preprint arXiv:2408.00724*, 2024.

[^60]: T. Z. Zhao, V. Kumar, S. Levine, and C. Finn. Learning fine-grained bimanual manipulation with low-cost hardware. *arXiv preprint arXiv:2304.13705*, 2023.

[^61]: T. Z. Zhao, J. Tompson, D. Driess, P. Florence, K. Ghasemipour, C. Finn, and A. Wahid. Aloha unleashed: A simple recipe for robot dexterity. *arXiv preprint arXiv:2410.13126*, 2024.

[^62]: H. Ha and S. Song. Flingbot: The unreasonable effectiveness of dynamic manipulation for cloth unfolding. In *Conference on Robot Learning*, pages 24–33. PMLR, 2022.

[^63]: S. Ha, S. Coros, A. Alspach, J. M. Bern, J. Kim, and K. Yamane. Computational design of robotic devices from high-level motion specifications. *IEEE Transactions on Robotics*, 34(5):1240–1251, 2018.

[^64]: H. T. Suh, M. Simchowitz, K. Zhang, T. Pang, and R. Tedrake. Pathologies and challenges of using differentiable simulators in policy optimization for contact-rich manipulation. In *ICRA 2022 Workshop: Reinforcement Learning for Contact-Rich Manipulation*, 2022.

[^65]: J. Xu, V. Makoviychuk, Y. Narang, F. Ramos, W. Matusik, A. Garg, and M. Macklin. Accelerated policy learning with parallel differentiable simulation. *arXiv preprint arXiv:2204.07137*, 2022.

[^66]: S. Ha, S. Coros, A. Alspach, J. Kim, and K. Yamane. Computational co-optimization of design parameters and motion trajectories for robotic systems. *The International Journal of Robotics Research*, 37(13-14):1521–1536, 2018.

[^67]: J. Külz, S. Ha, and M. Althoff. A design co-pilot for task-tailored manipulators. *arXiv preprint arXiv:2509.13077*, 2025.

[^68]: N. Rudin, D. Hoeller, P. Reist, and M. Hutter. Learning to walk in minutes using massively parallel deep reinforcement learning. In *Conference on robot learning*, pages 91–100. PMLR, 2022.

[^69]: G. Authors. Genesis: A generative and universal physics engine for robotics and beyond, December 2024. URL [https://github.com/Genesis-Embodied-AI/Genesis](https://github.com/Genesis-Embodied-AI/Genesis).

[^70]: A. Kumar, Z. Fu, D. Pathak, and J. Malik. Rma: Rapid motor adaptation for legged robots. *arXiv preprint arXiv:2107.04034*, 2021.

[^71]: Z. Zhuang, Z. Fu, J. Wang, C. Atkeson, S. Schwertfeger, C. Finn, and H. Zhao. Robot parkour learning. *arXiv preprint arXiv:2309.05665*, 2023.

[^72]: Z. Fu, A. Kumar, J. Malik, and D. Pathak. Minimizing energy consumption leads to the emergence of gaits in legged robots. *arXiv preprint arXiv:2111.01674*, 2021.

[^73]: G. B. Margolis and P. Agrawal. Walk these ways: Tuning robot control for generalization with multiplicity of behavior. In *Conference on Robot Learning*, pages 22–31. PMLR, 2023.

[^74]: Z. Mandi, Y. Weng, D. Bauer, and S. Song. Real2code: Reconstruct articulated objects via code generation. In *The Thirteenth International Conference on Learning Representations*, 2025.

[^75]: K. Sims. Evolving virtual creatures. In *Seminal Graphics Papers: Pushing the Boundaries, Volume 2*, pages 699–706. 2023.

[^76]: H. Lipson and J. B. Pollack. Automatic design and manufacture of robotic lifeforms. *Nature*, 406(6799):974–978, 2000.

[^77]: K. De Jong. Evolutionary computation: a unified approach. In *Proceedings of the Genetic and Evolutionary Computation Conference Companion*, pages 373–388, 2017.

[^78]: C. G. Langton. Artificial life: An overview. 1997.

[^79]: N. Cheney, R. MacCurdy, J. Clune, and H. Lipson. Unshackling evolution: evolving soft robots with multiple materials and a powerful generative encoding. *ACM SIGEVOlution*, 7(1):11–23, 2014.

[^80]: Y. Yuan, Y. Song, Z. Luo, W. Sun, and K. Kitani. Transform2act: Learning a transform-and-control policy for efficient agent design. *arXiv preprint arXiv:2110.03659*, 2021.

[^81]: D. Ha. Reinforcement learning for improving agent design. *Artificial life*, 25(4):352–365, 2019.

[^82]: J. Hu, J. Whitman, and H. Choset. Glso: Grammar-guided latent space optimization for sample-efficient robot design automation. In *Conference on Robot Learning*, pages 1321–1331. PMLR, 2023.

[^83]: J. Hu, J. Whitman, M. Travers, and H. Choset. Modular robot design optimization with generative adversarial networks. In *2022 International Conference on Robotics and Automation (ICRA)*, pages 4282–4288. IEEE, 2022.

[^84]: A. Zhao, J. Xu, M. Konaković-Luković, J. Hughes, A. Spielberg, D. Rus, and W. Matusik. Robogrammar: graph grammar for terrain-optimized robot design. *ACM Transactions on Graphics (TOG)*, 39(6):1–16, 2020.

[^85]: J. Xu, A. Spielberg, A. Zhao, D. Rus, and W. Matusik. Multi-objective graph heuristic search for terrestrial robot design. In *2021 IEEE international conference on robotics and automation (ICRA)*, pages 9863–9869. IEEE, 2021.

[^86]: K. Fay, D. A. Djapri, A. Zorin, J. Clinton, A. E. Lahib, H. Su, M. T. Tolley, S. Yi, and X. Wang. Cross-embodied co-design for dexterous hands. *arXiv preprint arXiv:2512.03743*, 2025.

[^87]: Wikipedia. Parallel axis theorem — Wikipedia, the free encyclopedia. [http://en.wikipedia.org/w/index.php?title=Parallel%20axis%20theorem&oldid=1327910993](http://en.wikipedia.org/w/index.php?title=Parallel%20axis%20theorem&oldid=1327910993), 2026. \[Online; accessed 05-February-2026\].

[^88]: T. E. Truong, M. Piseno, Z. Xie, and K. Liu. Pdp: Physics-based character animation via diffusion policy. In *SIGGRAPH Asia 2024 Conference Papers*, pages 1–10, 2024.
