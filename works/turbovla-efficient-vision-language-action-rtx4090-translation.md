---
title: "TurboVLA：消费级 RTX 4090 上 32Hz 实时视觉-语言-动作模型，显存占用 <1GB"
sourceUrl: "https://arxiv.org/html/2607.27205v1"
sourceTitle: "TurboVLA: Real-Time Vision-Language-Action Model at 32 Hz on an RTX 4090 with <1 GB VRAM"
sourceCapturedAt: "2026-07-30T12:05:05.816Z"
requestedUrl: "https://arxiv.org/html/2607.27205v1"
adapter: "generic"
conversionMethod: "defuddle"
kind: "generic/article"
language: "zh-CN"
pipelineRunId: "20260730_turbovla"
pipelineSource: "translate/20260730_turbovla/works-ready/turbovla-translation.md"
sourceFigureCount: null
---

---

# TurboVLA：消费级 RTX 4090 上 32Hz 实时视觉-语言-动作模型，显存占用 <1GB

Hengyi Xie <sup>1</sup>, Chenfei Yao <sup>1∗</sup>, Xianjin Wu <sup>1</sup>, Xuanyang Xi <sup>2</sup>, Yiping Tang <sup>2</sup>, Di Xu <sup>2</sup>,
Yingying Zhu <sup>1</sup>, Dingkang Liang <sup>1</sup> <sup><math xmlns="http://www.w3.org/1998/Math/MathML" display="inline" data-latex="\dagger"><semantics><mo>†</mo> <annotation>\dagger</annotation></semantics></math></sup>, Xiang Bai <sup>1</sup>, Han Ding <sup>1</sup>

<sup>1</sup> 华中科技大学, <sup>2</sup> 华为技术有限公司, 中国
{hengyi\_xie, yaochenfei, dkliang, xbai}@hust.edu.cn
{xixuanyang, tangyiping, xudi21}@huawei.com
[https://github.com/H-EmbodVis/TurboVLA](https://github.com/H-EmbodVis/TurboVLA) [https://h-embodvis.github.io/TurboVLA](https://h-embodvis.github.io/TurboVLA) 
同等贡献，按姓氏字母顺序排列。<sup><math xmlns="http://www.w3.org/1998/Math/MathML" display="inline" data-latex="\dagger"><semantics><mo>†</mo> <annotation>\dagger</annotation></semantics></math></sup> 项目负责人。

###### 摘要

视觉-语言-动作（Vision-Language-Action, VLA）模型通常采用以大语言模型为中心的 V→L→A 路径，在这一路径中，视觉观测首先投影到大语言模型的表示空间，然后解码为机器人动作。尽管这种设计有效，但它在每次策略调用时都会产生大量计算和内存开销。本文提出 TurboVLA，一种全新的 VLA 范式，将传统的 V→L→A 路径重构为直接的 V+L→A 映射。TurboVLA 不再使用大语言模型作为连接感知与动作的核心中枢，而是独立编码视觉观测和语言指令，通过轻量级双向视觉-语言交互直接在两者之间交换信息，并使用紧凑解码器预测连续动作块（action chunk，指一次预测多步连续动作的序列）。这种简洁设计直接从视觉和语言特征构建任务条件化表示，显著降低了 VLA 推理的计算和内存成本。在 LIBERO 基准上，TurboVLA 仅用 0.2B 参数、31.2 毫秒推理延迟和 0.9 GB 推理显存，在消费级 RTX 4090 上达到了 97.7% 的平均成功率，与规模大得多的 VLA 策略性能相当甚至更优。这些结果表明，TurboVLA 是主流以大语言模型为中心的 VLA 范式的简洁而有效的替代方案，为如何连接视觉、语言和动作以实现高效机器人操作提供了新的视角。

![Refer to caption](https://arxiv.org/html/2607.27205v1/x1.png)

图 1：TurboVLA 实现了紧凑的本地部署和实时语言条件化操作，参数量 0.2B，推理显存 0.9 GB，策略延迟 31.2 毫秒。

## 1 引言

视觉-语言-动作（Vision-Language-Action, VLA）模型已成为语言条件化机器人操作的强大框架，在统一策略中连接视觉观测、自然语言指令和机器人动作 [^5] [^70] [^21] [^40] [^3] [^42] [^33] [^22] [^14]。一种常见设计是将大语言模型置于这一过程的中心。这类系统实际上遵循间接的 V→L→A 路径：视觉观测转换为语言对齐的表示，与任务指令结合，由大语言模型处理，随后解码为动作 [^12] [^70] [^21]。这种设计将大规模预训练获得的广泛语义知识迁移到机器人控制中，支持开放词汇理解（open-vocabulary understanding，指能理解训练时未见过的词汇）、语义泛化和高层推理。

然而，以大语言模型为中心的 VLA 模型为实时机器人执行引入了巨大瓶颈。实时性对于响应式交互、高吞吐量操作以及在资源受限平台上的部署都至关重要。如图 2(a) 所示，现有以大语言模型为中心的 VLA 模型主要遵循两种动作生成设计。自回归 VLA 模型（如 OpenVLA [^21] 和 RT-2 [^70]）将动作表示为 token，因此继承了语言生成的顺序解码成本。近期方法通过并行动作解码、连续动作头或专用动作专家来缓解这一成本 [^3] [^42] [^20] [^22] [^46]。尽管这些动作专家设计避免了逐 token 的动作生成，但视觉观测和指令在预测动作之前仍需通过拥有数十亿参数的大语言模型处理。这些大型语言模型核心带来了巨大的计算和内存开销，导致推理延迟高，限制了控制频率。这引发了一个更根本的问题：*如何设计一个简洁、优雅且高效的 VLA，直接将视觉和语言映射到动作以实现执行级操作（execution-level manipulation，区别于高层任务规划），而无需以大语言模型为中心？*

![Refer to caption](https://arxiv.org/html/2607.27205v1/x2.png)

图 2：从以大语言模型为中心的 VLA 到 TurboVLA。(a) 以大语言模型为中心的 VLA 从大语言模型表示预测动作，而 TurboVLA 直接融合视觉和指令特征进行连续控制。(b) TurboVLA 以更低的延迟和模型规模在 LIBERO 上达到高度竞争力的性能。

我们的核心观察是：语言对于指令条件化操作必不可少，但执行级控制无需以大语言模型为中心。一旦指令已经指定了预期的操作技能，执行策略就不需要执行开放式语言生成或自主任务分解。相反，它主要需要使用指令来确定当前视觉证据应如何指导动作。在当前以大语言模型为中心的 VLA 模型中，这种交互通过通用语言模型表示来中介，其广泛的推理和生成能力超过了许多执行级任务的需求。轻量级文本编码器（如 BERT [^11]）可以提供执行任务所需的指令语义，而紧凑的跨模态交互允许语言和视觉共同构建面向控制的表示 [^35] [^44] [^16] [^37]。这表明了一种不同的 VLA 范式：与其围绕以大语言模型为中心的潜在空间组织感知和控制，视觉和语言可以直接交互以形成专门用于连续动作预测的表示。

因此，我们提出 TurboVLA，一个简洁而高效的 V+L→A 模型，用于实时语言条件化操作。如图 2(a) 所示，TurboVLA 使用视觉编码器和轻量级文本编码器分别处理视觉观测和任务指令。受先进视觉定位模型（如 Grounding DINO [^32]）中高效跨模态交互的启发，TurboVLA 用直接的视觉-语言交互取代了以大语言模型为中心的执行路径，避免了通过数十亿参数的语言模型处理多模态输入的计算和内存开销。紧凑的交叉注意力模块高效地融合指令和视觉特征，然后在单次前向传播中解码为连续动作块 [^68]，无需自回归的动作 token 生成。这种轻量级设计显著降低了推理延迟和 GPU 内存使用。

广泛的实验表明，TurboVLA 在保持强大操作性能的同时实现了实时执行。在消费级 RTX 4090 上，从接收当前多模态观测到产生动作块，端到端策略延迟仅需 31.2 毫秒，对应于每秒超过 30 次动作块预测（32 Hz）。TurboVLA 仅包含 0.2B 参数，约为 π₀.₅ [^42] 参数量的 6%，推理时使用不到 1 GB 显存。尽管设计轻量，TurboVLA 在 LIBERO [^29] 上达到了 97.7% 的平均成功率，与规模大得多的 VLA 系统性能相当。如图 2(b) 所示，TurboVLA 在操作性能、推理延迟和模型规模之间提供了优异的权衡，降低了在延迟敏感和资源受限的机器人系统中部署语言条件化操作策略的硬件门槛。更广泛地说，这些结果促使 VLA 社区审视执行级控制是否必须保持以大语言模型为中心，并在任务成功率之外评估未来系统。我们的贡献总结如下：

- 我们重新审视了现有 VLA 模型以大语言模型为中心的设计，并将大语言模型核心识别为实时动作执行的主要瓶颈。基于此分析，我们引入了一种实时 VLA 范式，在保留语言条件化的同时从执行级控制中移除了大语言模型。
- 我们提出了 TurboVLA，一个简洁高效的 V+L→A 模型，结合了轻量级指令编码、直接视觉-语言交互、机器人状态条件化和非自回归连续动作块预测。
- LIBERO 上的实验表明，TurboVLA 在消费级 RTX 4090 上以每秒超过 30 次在线策略推理的速度运行，达到 97.7% 的平均成功率，仅用 0.2B 参数和不到 1 GB 推理显存。在 LIBERO 之外，TurboVLA 在具有挑战性的双臂和真实世界环境中仍然有效。

## 2 相关工作

**视觉-语言-动作模型**。视觉-语言-动作（VLA）模型在统一策略中集成视觉观测、任务指令和动作预测，通常利用大规模视觉-语言预训练实现语义泛化。RT-1 [^5] 展示了可扩展的基于 Transformer 的机器人控制，而 RT-2 [^70] 和 OpenVLA [^21] 通过动作 token 接口将预训练的视觉-语言模型适配到机器人轨迹。连续控制模型（如 π₀ [^3] 和 π₀.₅ [^42]）则将专用动作专家附加到预训练的多模态骨干网络。这一通用 VLA 方向还通过跨具身数据集（cross-embodiment datasets，指不同物理形态和配置的机器人）[^39]、可重用策略表示 [^40]、基于扩散的机器人策略 [^33] [^22] 以及针对不同具身的基础模型 [^2] [^6] [^54] 得到推进。近期方法通过视觉预见 [^60]、预测性世界知识 [^67] 和潜在推理 [^1] 增强 VLA 学习。其他方法通过以姿态为中心的预训练或点-动作交互 [^27] [^7] 引入几何感知控制表示。最近的工作进一步通过结合时间运动线索和短期未来预测将 VLA 策略扩展到动态操作 [^13]。这些工作共同展示了大型预训练多模态表示的优势，以及中间表示表达能力不断增强的趋势。TurboVLA 关注不同的架构选择：与其将每个控制步骤路由通过大型生成式多模态骨干网络，它分别编码任务文本，并直接将其与视觉观测和机器人状态集成以进行执行级动作预测。

**VLA 策略中的高效执行**。近期工作通过动作侧重新设计和骨干网络侧优化来提高 VLA 效率。动作即 token 策略继承了语言模型的顺序解码过程，促使研究连续动作专家 [^3] [^42] [^20]、紧凑且结构化的动作分词器 [^41] [^30] 以及带并行解码的动作块 [^33] [^26]。紧凑的 VLA 架构（包括 TinyVLA [^55]、RoboMamba [^31]、SmolVLA [^46] 和 Evo-1 [^28]）在保留预训练多模态表示的同时减少了模型规模或推理成本。一条互补的研究路线通过量化 [^58] [^52]、token 重用或剪枝 [^57] [^18]、动态深度 [^59]、结构剪枝 [^51] [^66] 和蒸馏 [^9] [^17] 来减少冗余的骨干网络计算。其他方法在不改变基础策略的情况下提高响应性，包括异步动作块执行 [^4]、流式推理和水平感知流采样 [^34] 以及推测性推理 [^38]。这些方法加速动作生成或减少计算，同时在很大程度上保留大型多模态骨干网络作为执行表示。相比之下，TurboVLA 从低级控制路径中移除了大型生成式语言骨干网络，直接从紧凑的视觉、文本和本体感觉特征（proprioceptive features，指机器人自身的关节角度、位置等状态信息）构建动作表示。

**机器人控制的语言接口**。文本指令可以作为条件化感知和控制的任务规范，而不是作为生成式语言建模的提示。早期的模仿学习方法表明，共享策略可以将视觉观测和自然语言命令直接映射到不同的操作行为 [^35] [^49]。CLIPort [^44] 将预训练的视觉-语言语义与空间操作路径结合，而 BC-Z [^16] 则基于预训练的文本或人类视频嵌入来条件化多任务策略。CALVIN [^37] 和 HULC [^36] 将文本任务条件化扩展到基于非结构化演示的长期控制。PerAct [^45] 将文本目标整合到基于体素的 Transformer 策略中，而 VIMA [^19] 则通过交错的文本和视觉提示表示任务。在执行级策略之外，具身多模态语言模型结合语言理解、3D 定位和任务调度来生成定位的动作计划 [^24]。这种面向规划的能力与本工作研究的高效执行级控制是互补的。这些工作确立了文本表示作为机器人控制有效任务输入的地位。TurboVLA 在当前 VLA 范式下研究这一接口，考察紧凑文本编码器和直接视觉-文本交互是否足以实现高性能、实时连续控制。

## 3 初步知识

**以大语言模型为中心的视觉-语言-动作模型**。大多数现有 VLA 模型 [^21] [^3] [^42] [^69] [^14] 将大语言模型置于视觉到动作路径的中心。给定视觉观测 $\mathcal{O}_{n}$，视觉编码器首先提取视觉特征并将其投影到语言模型的 token 空间。然后，投影的视觉 token 与分词后的任务指令拼接，由大语言模型联合处理：

$$
\widetilde{Z}_{n}^{v}=P_{v}\!\left(E_{v}(\mathcal{O}_{n})\right),\qquad H_{n}^{L}=F_{L}\left(\left[\widetilde{Z}_{n}^{v};\operatorname{Tok}(x)\right]\right),
$$

其中 $E_{v}$ 表示视觉编码器，$P_{v}$ 将视觉特征映射到语言模型嵌入空间，$\operatorname{Tok}(x)$ 表示指令 token，$F_{L}$ 是大语言模型。重要的是，阶段 $L$ 不仅仅负责编码语言。它充当视觉感知与机器人动作之间的中央表示桥梁：视觉信息与语言模型空间对齐，与任务指令集成，并转换为多模态表示，从中预测动作。因此，我们将这一主流计算路径总结为 V→L→A，其中 $\bm{L}$ 表示以大语言模型为中心的多模态接口。

现有以大语言模型为中心的 VLA 模型主要在如何从 $H_{n}^{L}$ 生成动作方面有所不同。自回归模型离散化机器人动作并从语言模型表示中顺序预测它们 [^70] [^21]，而动作专家模型使用单独的连续解码器，

$$
\hat{\mathbf{A}}_{n}=D_{\mathrm{act}}\left(H_{n}^{L},s_{n}\right),
$$

并行生成动作 [^3] [^42] [^20] [^22]。尽管动作专家模型避免了逐 token 的动作生成，但它们保留了相同的表示依赖性，因为动作解码器在大语言模型产生的特征上操作。因此，尽管使用不同的动作生成机制，两种设计都保留 $\bm{L}$ 作为从视觉感知到动作预测的中央桥梁。

**直接视觉-语言交互**。交叉注意力为视觉和语言特征之间直接交换信息提供了简单高效的机制。给定视觉特征 $Z^{v}$ 和指令特征 $Z^{l}$，语言条件化的视觉特征可以通过以下方式获得：

$$
\widetilde{Z}^{v}=Z^{v}+\operatorname{Attn}\left(Q_{v},K_{l},V_{l}\right),
$$

而视觉感知的指令特征则通过交换查询和上下文模态来产生。这种双向交互允许任务语言塑造视觉处理，同时视觉上下文细化指令表示。视觉-语言定位模型（如 Grounding DINO [^32]）采用这种直接的跨模态交互来建立文本概念与视觉内容之间的细粒度对应关系。虽然这些模型使用生成的特征进行目标定位，但我们使用直接视觉-语言交互来构建面向控制的表示以进行连续动作预测。

![Refer to caption](https://arxiv.org/html/2607.27205v1/x3.png)

图 3：TurboVLA 概览。(a) TurboVLA 通过紧凑的模态编码器、视觉-语言交互和动作块解码器，简洁高效地将视觉观测和语言指令映射到连续动作块。(b) 交互模块设计尽可能简单。它使用堆叠的双向交叉注意力来产生视觉感知的指令特征和指令条件化的视觉特征。

## 4 TurboVLA

我们引入 TurboVLA，一种用于执行级语言条件化操作的直接且简洁的 V+L→A 范式。如图 3(a) 所示，TurboVLA 首先使用视觉编码器和轻量级文本编码器对视觉观测和任务指令进行编码。然后，一个简单紧凑的视觉-语言交互模块直接在两种模态之间交换信息以构建动作就绪特征。最后，动作块解码器将这些特征与当前机器人状态结合，在单次前向传播中预测完整的连续动作序列。与以大语言模型为中心的 VLA 模型不同，我们的方法不会在动作预测之前将视觉和文本输入路由通过大语言模型。

### 4.1 多模态特征编码

为降低以大语言模型为中心的执行路径的开销，同时保留对指令的充分理解，TurboVLA 使用紧凑的模态专用编码器。执行级指令通常通过对象、属性和空间关系指定操作技能，无需开放式生成或任务级规划。因此，我们使用轻量级编码器（如 BERT [^11]）对指令进行编码，并使用视觉编码器处理视觉观测。如图 3(a) 所示，生成的特征投影到共享隐藏维度 $d$ 中，以进行后续的视觉-语言交互和动作预测。给定任务指令 $x$，文本编码器提取 token 级指令特征：

$$
Z^{l}=P_{l}\left(f_{\mathrm{text}}(x)\right)\in\mathbb{R}^{N_{l}\times d},
$$

其中 $P_{l}$ 将编码器输出投影到策略维度，$N_{l}$ 是指令 token 的数量。我们保留完整的 token 序列而不是池化嵌入，以便对象、属性和空间关系保持可用于细粒度视觉条件化。

对于每个相机观测 $I_{n}^{(i)}$，图像编码器提取空间视觉特征，并投影增加位置和相机视角嵌入：

$$
Z_{n}^{v,(i)}=P_{v}\left(f_{\mathrm{img}}\left(I_{n}^{(i)}\right)\right)+E_{\mathrm{pos}}^{(i)}+e_{\mathrm{view}}^{(i)},\qquad Z_{n}^{v}=\left[Z_{n}^{v,(1)};\ldots;Z_{n}^{v,(K)}\right].
$$

这里，$E_{\mathrm{pos}}^{(i)}$ 保留视角内的空间结构，$e_{\mathrm{view}}^{(i)}$ 标识相机来源。拼接 $K$ 个流保留来自多个视角的互补线索。

机器人状态是将任务条件化场景特征转换为可执行动作所必需的，但对于视觉-语言对应关系并非必要。我们将其单独编码为

$$
Z_{n}^{s}=f_{\mathrm{state}}(s_{n})\in\mathbb{R}^{N_{s}\times d},
$$

其中 $f_{\mathrm{state}}$ 是轻量级投影网络。状态特征直接引入动作解码器，使跨模态交互专注于任务条件化场景理解。这些模态专用编码器用针对执行级操作定制的紧凑特征序列替代了高维大语言模型接口，减少了中间激活内存和下游注意力成本，同时保留控制所需的信息。

### 4.2 视觉-语言交互模块

独立编码的视觉和文本特征还无法确定哪些视觉内容与当前指令相关。虽然以大语言模型为中心的 VLA 在大型语言骨干网络内执行这种对齐，TurboVLA 改用图 3(b) 中的简单高效视觉-语言交互模块在两个流之间直接交换信息。

令 $V_{n}^{0}=Z_{n}^{v}$ 和 $L_{n}^{0}=Z^{l}$ 表示初始视觉和指令特征。交互模块通过 $N$ 个双向跨模态层逐步更新两个流：

$$
\left(V_{n}^{\ell},L_{n}^{\ell}\right)=\operatorname{FusionLayer}_{\ell}\left(V_{n}^{\ell-1},L_{n}^{\ell-1}\right),\qquad\ell=1,\ldots,N.
$$

每层由层归一化、双向交叉注意力和带残差连接的模态专用前馈网络组成。视觉到指令的注意力将场景上下文注入指令流，而指令到视觉的注意力根据任务语义条件化视觉特征。在最后一层之后，更新后的流拼接为

$$
Z_{n}^{vl}=\left[V_{n}^{N};L_{n}^{N}\right].
$$

通过这个紧凑的交互模块，目标对象、属性、空间关系等信息可以调制相关视觉特征，同时指令表示也同步适应当前场景。这种简单的交互设计高效地为动作预测提供任务特定的多模态信息，无需依赖大语言模型的广泛生成和推理能力。

### 4.3 连续动作块预测

我们使用 ACT 风格 [^68] 的轻量级 Transformer 解码器将融合的多模态表示和机器人状态特征映射到连续动作序列：

$$
\hat{\mathbf{A}}_{n}=D_{\theta}\!\left(Q_{a},\left[Z_{n}^{\mathrm{vl}};Z_{n}^{s}\right]\right)\in\mathbb{R}^{H\times d_{a}},
$$

其中 $Q_{a}=\left[q_{1},\ldots,q_{H}\right]$ 包含 $H$ 个可学习的动作查询，$D_{\theta}$ 表示动作块解码器。在这一阶段引入机器人状态提供当前具身配置，同时让前面的交互模块专注于任务条件化场景理解。

所有动作查询并行解码，允许策略在单次前向传播中预测完整的 $H$ 步动作块，无需动作分词或顺序生成。我们通过在专家动作块上的行为克隆来训练 TurboVLA。给定目标序列 $\mathbf{A}_{n}^{*}=[a_{n,1}^{*},\ldots,a_{n,H}^{*}]$，训练目标是 $\ell_{1}$ 损失，不需要辅助的语言建模目标。紧凑特征编码、直接视觉-语言交互和并行动作解码共同构成图 3(a) 所示的高效 V+L→A 执行路径。

## 5 实验

我们评估 TurboVLA 是否能在保持强大语言条件化操作性能的同时，显著降低以大语言模型为中心的 VLA 策略的模型规模、推理延迟和内存开销。我们首先描述 LIBERO [^29]、RoboTwin 2.0 [^8] 和真实世界部署的实现细节和评估协议。然后，我们评估单臂操作的性能-效率权衡，检验所提出架构对双臂多任务控制的可扩展性，验证其在真实世界部署中的有效性，并消融直接视觉-语言交互设计的主要组件。

### 5.1 实现细节

我们使用 DINOv3 [^47] 作为视觉骨干网络，BERT [^11] 作为轻量级指令编码器。视觉和文本特征投影到维度为 256 的共享空间中，由 6 层双向视觉-语言交互层处理。这些交互层使用 Grounding DINO [^32] 预训练的特征增强权重进行初始化。ACT 风格的 Transformer 解码器 [^68] 将生成的多模态特征和机器人状态映射到连续动作块。在所有基准测试中，我们通过 $\ell_{1}$ 损失的行为克隆进行训练，在四个 RTX 4090 GPU 上使用 $5\times 10^{-5}$ 的学习率。具体基准设置如下所述。

### 5.2 基准测试和指标

我们在三个互补场景中评估 TurboVLA：LIBERO [^29] 上的单臂操作、RoboTwin 2.0 [^8] 上的双臂操作以及真实机器人平台上的部署。

**LIBERO** 包含四个套件——LIBERO-Object、LIBERO-Spatial、LIBERO-Goal 和 LIBERO-Long——每个套件包含十个语言条件化操作任务。我们使用随 OpenVLA [^21] 发布的修改后的 no\_noops RLDS 数据集，并使用 DINOv3 ViT-B 骨干网络联合训练一个混合套件模型。该模型预测 12 步连续 7-DoF（7 自由度，即 7 维动作空间，如末端执行器的 3D 位置、3D 旋转和夹爪开合）动作块，训练 80k 步，包括 10k 预热步，有效批大小为 256。遵循 VLA-Adapter 的执行协议 [^53]，在不引入任何额外技术的情况下，我们对每个任务进行 50 次执行，并报告 2,000 次试验的套件级和平均成功率。

**RoboTwin 2.0** 包含 50 个需要协调双臂控制的语言条件化双臂操作任务。考虑到我们可用的计算预算，我们仅使用官方的清洁环境演示数据进行训练，不包括随机场景数据。我们使用 DINOv3 ViT-L 骨干网络联合训练一个多任务模型，预测 50 步 14 维绝对关节位置动作块。训练持续 55k 步，包括 1k 预热步，有效批大小为 192。遵循 StarVLA [^48] 训练和评估框架，在不引入任何额外技术的情况下，我们对每个任务进行 100 次清洁设置执行，并报告所有 50 个任务的平均成功率。

**真实世界评估**。我们使用图 4 所示的 AgileX Piper 平台进行真实世界实验。我们考虑四个代表性的语言条件化操作任务：抓取滚筒、移开扑克牌、按订书机和堆叠三个碗。这些任务需要准确的对象定位、视角鲁棒性以及在真实传感器噪声下的稳定闭环执行。我们从 LIBERO 上预训练的 TurboVLA 检查点初始化策略，并在 $4\times 65$ 次遥操作真实世界演示上微调 12.5k 步。每个任务在 40 次试验中评估，我们报告成功率。我们在相同平台、训练数据和评估协议下与 π₀.₅ 进行比较。

**指标和比较**。我们在所有基准测试中使用任务成功率作为主要指标。我们还报告总参数量、推理延迟和推理显存。对于比较中包含的所有其他可运行方法，这些效率指标使用官方架构、实现和检查点在批大小为 1 的 RTX 4090 上测量。延迟测量的是从接收多模态输入到产生动作块（或等效数量的自回归动作 token）的时间，而推理显存表示完整在线策略的峰值 GPU 内存使用量。

### 5.3 主要结果

表 1 和表 2 展示了 TurboVLA 在仿真基准上的互补评估。从这些结果中，我们得出以下观察。

表 1：LIBERO 基准对比。"Emb. PT." 表示在 LIBERO 之外进行了额外的具身预训练（在机器人数据上的预训练）。Params 表示总参数量。Latency 表示从多模态输入到生成一个动作块或等效数量的自回归动作 token 所需的时间。延迟和推理显存均在单张 RTX 4090 上以批大小为 1 测量。对于 TurboVLA，报告的参数量对应于 DINOv3 ViT-B 配置。

<table><tbody><tr><td>方法</td><td>Emb. PT.</td><td colspan="3">部署效率</td><td colspan="5">LIBERO 成功率（%）</td></tr><tr><td></td><td></td><td>参数量 (B) <math><semantics><mo>↓</mo> <annotation>\downarrow</annotation></semantics></math></td><td>显存 (GB) <math><semantics><mo>↓</mo> <annotation>\downarrow</annotation></semantics></math></td><td>延迟 (ms) <math><semantics><mo>↓</mo> <annotation>\downarrow</annotation></semantics></math></td><td>Spa.</td><td>Obj.</td><td>Goal</td><td>Long</td><td>平均<math><semantics><mo>↑</mo> <annotation>\uparrow</annotation></semantics></math></td></tr><tr><td colspan="10">非 VLA 策略基线</td></tr><tr><td>Diffusion Policy <sup><a href="#fn:10">10</a></sup> <sub>(RSS'23)</sub></td><td>✗</td><td>0.3</td><td>1.1</td><td>924.8</td><td>78.3</td><td>92.5</td><td>68.3</td><td>50.5</td><td>72.4</td></tr><tr><td colspan="10">能力导向型 VLA</td></tr><tr><td>OpenVLA <sup><a href="#fn:21">21</a></sup> <sub>(CoRL'24)</sub></td><td>✓</td><td>7.5</td><td>14.9</td><td>202.9</td><td>84.7</td><td>88.4</td><td>79.2</td><td>53.7</td><td>76.5</td></tr><tr><td><math><semantics><msub><mi>π</mi> <mn>0</mn></msub> <annotation>\pi_{0}</annotation></semantics></math> <sup><a href="#fn:3">3</a></sup> <sub>(RSS'25)</sub></td><td>✓</td><td>3.2</td><td>12.3</td><td>84.2</td><td>96.8</td><td>98.8</td><td>95.8</td><td>85.2</td><td>94.2</td></tr><tr><td>UniVLA <sup><a href="#fn:6">6</a></sup> <sub>(RSS'25)</sub></td><td>✓</td><td>7.6</td><td>15.0</td><td>173.8</td><td>96.5</td><td>96.8</td><td>95.6</td><td>92.0</td><td>95.2</td></tr><tr><td><math><semantics><msub><mi>π</mi> <mn>0.5</mn></msub> <annotation>\pi_{0.5}</annotation></semantics></math> <sup><a href="#fn:42">42</a></sup> <sub>(CoRL'25)</sub></td><td>✓</td><td>3.4</td><td>12.8</td><td>93.6</td><td>98.8</td><td>98.2</td><td>98.0</td><td>92.4</td><td>96.9</td></tr><tr><td>CogVLA <sup><a href="#fn:23">23</a></sup> <sub>(NeurIPS'25)</sub></td><td>✓</td><td>8.3</td><td>16.1</td><td>115.5</td><td>98.6</td><td>98.8</td><td>96.6</td><td>95.4</td><td>97.4</td></tr><tr><td>Mantis <sup><a href="#fn:60">60</a></sup> <sub>(CVPR'26)</sub></td><td>✓</td><td>4.9</td><td>7.9</td><td>198.7</td><td>98.8</td><td>99.2</td><td>94.4</td><td>94.2</td><td>96.7</td></tr><tr><td>MM-ACT <sup><a href="#fn:25">25</a></sup> <sub>(CVPR'26)</sub></td><td>✗</td><td>8.2</td><td>16.3</td><td>723.2</td><td>97.8</td><td>99.4</td><td>94.8</td><td>93.0</td><td>96.3</td></tr><tr><td>VLA-JEPA <sup><a href="#fn:50">50</a></sup> <sub>(ECCV'26)</sub></td><td>✓</td><td>2.8</td><td>5.3</td><td>108.7</td><td>96.2</td><td>99.6</td><td>97.2</td><td>95.8</td><td>97.2</td></tr><tr><td>VEGA-3D <sup><a href="#fn:56">56</a></sup> <sub>(ECCV'26)</sub></td><td>✓</td><td>9.0</td><td>16.0</td><td>546.4</td><td>97.4</td><td>99.4</td><td>97.0</td><td>95.2</td><td>97.3</td></tr><tr><td colspan="10">加速导向型 VLA</td></tr><tr><td>OpenVLA-OFT <sup><a href="#fn:20">20</a></sup> <sub>(RSS'25)</sub></td><td>✓</td><td>7.7</td><td>15.7</td><td>112.2</td><td>97.6</td><td>98.4</td><td>97.9</td><td>94.5</td><td>97.1</td></tr><tr><td>DDVLA <sup><a href="#fn:26">26</a></sup> <sub>(ICML'26)</sub></td><td>✓</td><td>7.5</td><td>14.5</td><td>60.8</td><td>97.2</td><td>99.4</td><td>96.8</td><td>92.2</td><td>96.4</td></tr><tr><td colspan="10">轻量级 VLA</td></tr><tr><td>SmolVLA <sup><a href="#fn:46">46</a></sup> <sub>(ArXiv'25)</sub></td><td>✗</td><td>2.3</td><td>7.1</td><td>203.1</td><td>93.0</td><td>94.0</td><td>91.0</td><td>77.0</td><td>88.8</td></tr><tr><td>DreamVLA <sup><a href="#fn:67">67</a></sup> <sub>(NeurIPS'25)</sub></td><td>✗</td><td>0.7</td><td>1.5</td><td>128.0</td><td>97.5</td><td>94.0</td><td>89.5</td><td>89.5</td><td>92.6</td></tr><tr><td>VLA-Adapter <sup><a href="#fn:53">53</a></sup> <sub>(AAAI'26)</sub></td><td>✗</td><td>1.5</td><td>4.3</td><td>87.3</td><td>97.8</td><td>99.2</td><td>97.2</td><td>95.0</td><td>97.3</td></tr><tr><td>Evo-1 <sup><a href="#fn:28">28</a></sup> <sub>(CVPR'26)</sub></td><td>✗</td><td>0.8</td><td>1.7</td><td>137.2</td><td>92.7</td><td>97.7</td><td>96.3</td><td>92.3</td><td>94.8</td></tr><tr><td>TurboVLA（本文）</td><td>✗</td><td>0.2</td><td>0.9</td><td>31.2</td><td>99.2</td><td>99.8</td><td>97.4</td><td>94.2</td><td>97.7</td></tr></tbody></table>

表 2：RoboTwin 2.0 基准对比，所有方法均仅在清洁设置下训练和评估。"Emb. PT." 表示在 RoboTwin 2.0 之外进行了额外的具身预训练，Params 表示总参数量。单任务方法为 50 个任务中的每一个训练独立策略，而多任务方法联合训练单个策略处理所有任务。对于 TurboVLA，报告的参数量对应于 DINOv3 ViT-L 配置。

<table><tbody><tr><th>方法</th><td>Emb. PT.</td><td>参数量 (B) <math><semantics><mo>↓</mo> <annotation>\downarrow</annotation></semantics></math></td><td>延迟 (ms) <math><semantics><mo>↓</mo> <annotation>\downarrow</annotation></semantics></math></td><td>平均成功率 (%) <math><semantics><mo>↑</mo> <annotation>\uparrow</annotation></semantics></math></td></tr><tr><th colspan="5">单任务训练</th></tr><tr><th>Diffusion Policy <sup><a href="#fn:10">10</a></sup> <sub>(RSS'23)</sub></th><td>✗</td><td>0.1</td><td>794.1</td><td>28.0</td></tr><tr><th>ACT <sup><a href="#fn:68">68</a></sup> <sub>(RSS'23)</sub></th><td>✗</td><td>0.1</td><td>20.4</td><td>29.7</td></tr><tr><th>DP3 <sup><a href="#fn:62">62</a></sup> <sub>(RSS'24)</sub></th><td>✗</td><td>0.3</td><td>78.4</td><td>55.2</td></tr><tr><th><math><semantics><msub><mi>π</mi> <mn>0</mn></msub> <annotation>\pi_{0}</annotation></semantics></math> <sup><a href="#fn:3">3</a></sup> <sub>(RSS'25)</sub></th><td>✓</td><td>3.2</td><td>87.6</td><td>46.4</td></tr><tr><th>FlowPolicy <sup><a href="#fn:65">65</a></sup> <sub>(AAAI'25)</sub></th><td>✗</td><td>0.3</td><td>–</td><td>41.0</td></tr><tr><th>RDT <sup><a href="#fn:33">33</a></sup> <sub>(ICLR'25)</sub></th><td>✓</td><td>1.7</td><td>204.8</td><td>34.5</td></tr><tr><th>SeedPolicy <sup><a href="#fn:15">15</a></sup> <sub>(ArXiv'26)</sub></th><td>✗</td><td>0.2</td><td>823.9</td><td>42.8</td></tr><tr><th colspan="5">多任务训练</th></tr><tr><th>UP-VLA <sup><a href="#fn:64">64</a></sup> <sub>(ICML'25)</sub></th><td>✓</td><td>1.6</td><td>74.3</td><td>52.9</td></tr><tr><th><math><semantics><msub><mi>π</mi> <mn>0.5</mn></msub> <annotation>\pi_{0.5}</annotation></semantics></math> <sup><a href="#fn:42">42</a></sup> <sub>(CoRL'25)</sub></th><td>✓</td><td>3.4</td><td>95.6</td><td>57.0</td></tr><tr><th>StarVLA-<math><semantics><mi>α</mi> <annotation>\alpha</annotation></semantics></math> <sup><a href="#fn:61">61</a></sup> <sub>(ECCV'26)</sub></th><td>✗</td><td>3.8</td><td>74.9</td><td>50.3</td></tr><tr><th>TurboVLA（本文）</th><td>✗</td><td>0.4</td><td>43.4</td><td>60.2</td></tr></tbody></table>

**1) 摆脱以大语言模型为中心的执行路径，显著改善了性能与效率的权衡**。如表 1 所示，TurboVLA 以显著更低的成本匹配了大型能力导向型 VLA 的操作能力。它达到了 97.7% 的平均成功率，而 π₀.₅ [^42] 为 96.9%，但仅使用其约 6% 的参数量，并将推理延迟从 93.6 ms 显著降低到 31.2 ms。我们的方法在平均成功率上也优于最新的 VLA-JEPA [^50]，同时速度快 3 倍以上，仅使用其约 7% 的参数量。这一对比表明，强大的执行级控制无需使用数十亿参数的大语言模型作为感知与动作的中心接口。与加速导向型 VLA 相比，这一优势同样明显：OpenVLA-OFT [^20] 和 Discrete Diffusion VLA [^26] 优化了动作生成，推理延迟分别达到 112.2 ms 和 60.8 ms，但两者仍然更慢，平均成功率也低于我们的方法，因为它们的大型语言骨干网络仍保留在执行中心。与轻量级 VLA 相比，TurboVLA 进一步提升了性能与效率的双重平衡。它在平均成功率上优于最新的 Evo-1 [^28] 和 VLA-Adapter [^53]，同时模型规模更小、推理速度更快。这种性能与效率的优势也延伸到 RoboTwin 2.0 基准。如表 2 所示，TurboVLA 在 50 个双臂操作任务中达到 60.2% 的平均成功率，推理延迟为 43.4 ms，优于 π₀.₅ 的 57.0% 和 95.6 ms，以及 StarVLA-α [^61] 的 50.3% 和 74.9 ms。

这些结果表明，单纯加速动作生成或减小模型规模都不足够。通过重新设计多模态执行路径，TurboVLA 验证了简单直接的 V+L→A 范式是一种更有效的方式，可以在单臂和双臂控制设置中同时实现强大的操作性能、低延迟和紧凑的模型规模。

![Refer to caption](https://arxiv.org/html/2607.27205v1/x4.png)

图 4：AgileX Piper 平台上的真实世界评估。左：我们的单臂设置，配备腕部视角 RGB-D 相机和第三人称视角 RGB-D 相机，以及四个任务中使用的物体。右上：TurboVLA 与 π₀.₅ 在四个真实世界操作任务上的成功率对比。右下：TurboVLA 的定性执行示例。

**2) 架构效率转化为实际可部署性**。实际机器人部署受策略准确性、响应延迟和驻留内存的联合约束，而非任何单一效率指标。如表 1 所示，大多数高性能 VLA 策略在数十亿参数规模运行，需要数 GB 的推理显存，而它们的推理延迟通常远高于 TurboVLA。这样的资源需求可能将部署限制在配备高内存 GPU 的平台上，或需要额外的压缩和系统级优化。相比之下，完整的 TurboVLA 策略将 97.7% 的平均成功率与 31.2 ms 的动作块推理和仅 0.9 GB 的推理显存结合在一起。这种优异的效率特性也很好地转化到真实机器人部署中。如图 4 所示，TurboVLA 在四个真实世界 AgileX Piper 任务上分别达到 92.5%、80%、90% 和 87.5% 的成功率，始终优于 π₀.₅。这些结果表明，提出的直接 V+L→A 路径在真实世界执行级操作中是充分且有效的。

### 5.4 消融研究

我们在 LIBERO 上进行消融实验，研究四个问题：语义语言条件化是否必要、指令应如何编码、哪种视觉-语言交互设计最有效，以及该方法对交互深度 $N$ 和动作时域 $H$ 的敏感性如何。

**语义语言条件化与指令编码**。我们首先研究语言本身的作用。表 3 显示，移除语言将平均成功率从 97.7% 降至 70.8%，其中 LIBERO-Goal 上的下降最大（97.4% → 11.6%）。这证实了当多种行为与同一场景兼容时，策略无法仅依赖视觉先验。用学习到的任务 ID 嵌入替换语义指令可以恢复部分性能，但仍比完整模型低 2.3%，表明自然语言指令提供的不仅仅是封闭集合的任务标识。然后，我们检验所提出的架构是否依赖特定的文本骨干网络。如表 3 所示，T5-small [^43] 达到了有竞争力的 97.1% 平均成功率，而 SigLIP [^63] 文本编码器达到 95.5%，表明执行级指令可以由轻量级文本编码器有效处理，无需大型生成式语言模型，且所提出的架构不依赖特定的文本表示。

![Refer to caption](https://arxiv.org/html/2607.27205v1/x5.png)

表 3：语言条件化的影响。

**视觉-语言交互设计**。确立了语义指令特征的重要性后，我们接下来研究在动作解码之前视觉和语言特征应如何交互。如图 5 所示，我们比较了无交互、两种非对称交叉注意力变体以及所提出的双向交互，同时保持所有其他架构和训练设置不变。如表 5 所示，直接拼接达到 95.2% 的平均成功率，而两种单向交叉注意力变体将其提升到 96.1% 和 96.5%。双向交互表现最佳，达到 97.7%，表明场景感知的指令特征和指令条件化的视觉特征为动作预测提供了互补信息。

**交互深度与动作时域**。最后，我们探索策略的两个实际超参数。表 5 显示，将交互层数从 $N=2$ 增加到 $N=6$ 将平均成功率从 93.5% 稳步提升到 97.7%，而更深的 $N=8$ 模型略微下降到 96.6%。因此我们使用 $N=6$ 作为容量与效率之间的良好平衡。我们还在保持架构其余部分不变的情况下改变动作时域 $H$。如图 6 所示，性能从 $H=8$ 时的 96.4% 提升到 $H=12$ 时的 97.7%，然后在 $H=15$ 时下降到 95.6%。这表明时域过短会限制时间表达能力，而时域过长会使块预测更加困难。因此我们在所有主要实验中使用 $H=12$。

总体而言，这些消融实验表明 TurboVLA 在不丢弃语义语言信息或显式跨模态建模的情况下实现了效率。其性能由轻量级指令编码和充分的双向视觉-语言交互实现，支持所提出的直接执行路径作为以大语言模型为中心的 VLA 架构的有效替代方案。

![Refer to caption](https://arxiv.org/html/2607.27205v1/x6.png)

图 6：动作时域 H 对 LIBERO 性能的影响。

## 6 结论

本文提出了 TurboVLA，一种简单而高效的 V+L→A 范式，超越了传统的以大语言模型为中心的视觉-语言-动作学习执行路径。通过结合轻量级指令编码、紧凑的视觉表示、双向视觉-语言交互和动作块解码，TurboVLA 在保持任务条件化操作能力的同时，显著减少了模型规模、推理延迟和内存消耗。我们的结果表明，执行级控制不一定需要通用大语言模型作为感知与动作之间的中心接口，我们希望这一架构为社区进一步审视大语言模型在 VLA 系统中的角色提供新的见解。尽管如此，TurboVLA 主要设计用于具体的执行级指令，可能无法提供高级任务规划所需的复杂语义理解和推理能力。未来的工作将探索将大语言模型的高级规划能力与 TurboVLA 的高效执行路径相结合，构建既智能又高效的分层系统。

[^1]: Latent reasoning vla: latent thinking and prediction for vision-language-action models. In Proc. of Intl. Conf. on Machine Learning, Cited by: §2.

[^2]: Gr00t n1: an open foundation model for generalist humanoid robots. arXiv preprint arXiv:2503.14734. Cited by: §2.

[^3]: $\pi_{0}$: a vision-language-action flow model for general robot control. In Proc. of Robotics: Science and Systems, Cited by: §1, §1, §2, §2, §3, §3, Table 1, Table 2.

[^4]: Real-time execution of action chunking flow policies. In Proc. of Advances in Neural Information Processing Systems, Vol. 38, pp. 33383–33407. Cited by: §2.

[^5]: Rt-1: robotics transformer for real-world control at scale. In Proc. of Robotics: Science and Systems, Cited by: §1, §2.

[^6]: Univla: learning to act anywhere with task-centric latent actions. In Proc. of Robotics: Science and Systems, Cited by: §2, Table 1.

[^7]: Pointact: vision-language-action models with multi-scale point-action interaction. In Proc. of Robotics: Science and Systems, Cited by: §2.

[^8]: Robotwin 2.0: a scalable data generator and benchmark with strong domain randomization for robust bimanual robotic manipulation. In Proc. of Intl. Conf. on Machine Learning, Cited by: §5.2, §5.

[^9]: Rlrc: reinforcement learning-based recovery for compressed vision-language-action models. IEEE Robotics and Automation Letters. Cited by: §2.

[^10]: Diffusion policy: visuomotor policy learning via action diffusion. In Proc. of Robotics: Science and Systems, Cited by: Table 1, Table 2.

[^11]: Bert: pre-training of deep bidirectional transformers for language understanding. In Proceedings of the 2019 conference of the North American chapter of the association for computational linguistics: human language technologies, volume 1 (long and short papers), pp. 4171–4186. Cited by: §1, §4.1, §5.1.

[^12]: Palm-e: an embodied multimodal language model. In Proc. of Intl. Conf. on Machine Learning, Cited by: §1.

[^13]: Towards generalizable robotic manipulation in dynamic environments. In Proc. of European Conference on Computer Vision, Cited by: §2.

[^14]: Orion: a holistic end-to-end autonomous driving framework by vision-language instructed action generation. In Proc. of IEEE Intl. Conf. on Computer Vision, pp. 24823–24834. Cited by: §1, §3.

[^15]: SeedPolicy: horizon scaling via self-evolving diffusion policy for robot manipulation. arXiv preprint arXiv:2603.05117. Cited by: Table 2.

[^16]: Bc-z: zero-shot task generalization with robotic imitation learning. In Proc. of the Conference on Robot Learning, pp. 991–1002. Cited by: §1, §2.

[^17]: Shallow- $\pi$: knowledge distillation for flow-based vlas. In Proc. of the IEEE Int. Conf. on Intelligent Robots and Systems, Cited by: §2.

[^18]: The better you learn, the smarter you prune: towards efficient vision-language-action models via differentiable token pruning. arXiv preprint arXiv:2509.12594. Cited by: §2.

[^19]: VIMA: general robot manipulation with multimodal prompts. In Proc. of Intl. Conf. on Machine Learning, Cited by: §2.

[^20]: Fine-tuning vision-language-action models: optimizing speed and success. In Proc. of Robotics: Science and Systems, Cited by: §1, §2, §3, §5.3, Table 1.

[^21]: Openvla: an open-source vision-language-action model. In Proc. of the Conference on Robot Learning, Cited by: §1, §1, §2, §3, §3, §5.2, Table 1.

[^22]: Cogact: a foundational vision-language-action model for synergizing cognition and action in robotic manipulation. arXiv preprint arXiv:2411.19650. Cited by: §1, §1, §2, §3.

[^23]: CogVLA: cognition-aligned vision-language-action models via instruction-driven routing & sparsification. In Proc. of Advances in Neural Information Processing Systems, Vol. 38, pp. 137646–137675. Cited by: Table 1.

[^24]: Cook and clean together: teaching embodied agents for parallel task execution. In Proceedings of the AAAI Conference on Artificial Intelligence, Vol. 40, pp. 18415–18424. Cited by: §2.

[^25]: Mm-act: learn from multimodal parallel generation to act. In Proc. of IEEE Intl. Conf. on Computer Vision and Pattern Recognition, pp. 35080–35090. Cited by: Table 1.

[^26]: Discrete diffusion vla: bringing discrete diffusion to action decoding in vision-language-action policies. In Proc. of Intl. Conf. on Machine Learning, Cited by: §2, §5.3, Table 1.

[^27]: PoseVLA: universal pose pretraining for generalizable vision-language-action policies. In Proc. of Robotics: Science and Systems, Cited by: §2.

[^28]: Evo-1: lightweight vision-language-action model with preserved semantic alignment. In Proc. of IEEE Intl. Conf. on Computer Vision and Pattern Recognition, pp. 13397–13406. Cited by: §2, §5.3, Table 1.

[^29]: Libero: benchmarking knowledge transfer for lifelong robot learning. In Proc. of Advances in Neural Information Processing Systems, Vol. 36, pp. 44776–44791. Cited by: §1, §5.2, §5.

[^30]: OAT: ordered action tokenization. In Proc. of Robotics: Science and Systems, Cited by: §2.

[^31]: Robomamba: efficient vision-language-action model for robotic reasoning and manipulation. In Proc. of Advances in Neural Information Processing Systems, Vol. 37, pp. 40085–40110. Cited by: §2.

[^32]: Grounding dino: marrying dino with grounded pre-training for open-set object detection. In Proc. of European Conference on Computer Vision, pp. 38–55. Cited by: §1, §3, §5.1.

[^33]: Rdt-1b: a diffusion foundation model for bimanual manipulation. In Proc. of Intl. Conf. on Learning Representations, Cited by: §1, §2, §2, Table 2.

[^34]: Faster: rethinking real-time flow vlas. arXiv preprint arXiv:2603.19199. Cited by: §2.

[^35]: Language conditioned imitation learning over unstructured data. In Proc. of Robotics: Science and Systems, Cited by: §1, §2.

[^36]: What matters in language conditioned robotic imitation learning over unstructured data. IEEE Robotics and Automation Letters 7 (4), pp. 11205–11212. Cited by: §2.

[^37]: Calvin: a benchmark for language-conditioned policy learning for long-horizon robot manipulation tasks. IEEE Robotics and Automation Letters 7 (3), pp. 7327–7334. Cited by: §1, §2.

[^38]: Realtime-vla flash: speculative inference framework for diffusion-based vlas. arXiv preprint arXiv:2605.13778. Cited by: §2.

[^39]: Open x-embodiment: robotic learning datasets and rt-x models. In Proc. of the IEEE Int. Conf. on Robotics and Automation, pp. 6892–6903. Cited by: §2.

[^40]: Octo: an open-source generalist robot policy. In Proc. of Robotics: Science and Systems, Cited by: §1, §2.

[^41]: Fast: efficient action tokenization for vision-language-action models. In Proc. of Robotics: Science and Systems, Cited by: §2.

[^42]: $\pi_{0.5}$: a vision-language-action model with open-world generalization. In Proc. of the Conference on Robot Learning, Cited by: §1, §1, §1, §2, §2, §3, §3, §5.3, Table 1, Table 2.

[^43]: Exploring the limits of transfer learning with a unified text-to-text transformer. Journal of Machine Learning Research 21 (140), pp. 1–67. Cited by: §5.4.

[^44]: Cliport: what and where pathways for robotic manipulation. In Proc. of the Conference on Robot Learning, pp. 894–906. Cited by: §1, §2.

[^45]: Perceiver-actor: a multi-task transformer for robotic manipulation. In Proc. of the Conference on Robot Learning, pp. 785–799. Cited by: §2.

[^46]: Smolvla: a vision-language-action model for affordable and efficient robotics. arXiv preprint arXiv:2506.01844. Cited by: §1, §2, Table 1.

[^47]: Dinov3. arXiv preprint arXiv:2508.10104. Cited by: §5.1.

[^48]: StarVLA: a lego-like codebase for vision-language-action model developing. arXiv preprint arXiv:2604.05014. Cited by: §5.2.

[^49]: Language-conditioned imitation learning for robot manipulation tasks. In Proc. of Advances in Neural Information Processing Systems, Vol. 33, pp. 13139–13150. Cited by: §2.

[^50]: Vla-jepa: enhancing vision-language-action model with latent world model. In Proc. of European Conference on Computer Vision, Cited by: §5.3, Table 1.

[^51]: Specprune-vla: accelerating vision-language-action models via action-aware self-speculative pruning. In Proc. of Intl. Conf. on Machine Learning, Cited by: §2.

[^52]: Bitvla: 1-bit vision-language-action models for robotics manipulation. arXiv preprint arXiv:2506.07530. Cited by: §2.

[^53]: Vla-adapter: an effective paradigm for tiny-scale vision-language-action model. In Proc. of the AAAI Conf. on Artificial Intelligence, pp. 18638–18646. Cited by: §5.2, §5.3, Table 1.

[^54]: Unified vision-language-action model. In Proc. of Intl. Conf. on Learning Representations, Cited by: §2.

[^55]: Tinyvla: towards fast, data-efficient vision-language-action models for robotic manipulation. IEEE Robotics and Automation Letters. Cited by: §2.

[^56]: Generation models know space: unleashing implicit 3d priors for scene understanding. In Proc. of European Conference on Computer Vision, Cited by: Table 1.

[^57]: Vla-cache: efficient vision-language-action manipulation via adaptive token caching. In Proc. of Advances in Neural Information Processing Systems, Vol. 38, pp. 164448–164473. Cited by: §2.

[^58]: QVLA: not all channels are equal in vision-language-action model's quantization. In Proc. of Intl. Conf. on Learning Representations, Cited by: §2.

[^59]: Efficientvla: training-free acceleration and compression for vision-language-action models. In Proc. of Advances in Neural Information Processing Systems, Vol. 38, pp. 40891–40914. Cited by: §2.

[^60]: Mantis: a versatile vision-language-action model with disentangled visual foresight. In Proc. of IEEE Intl. Conf. on Computer Vision and Pattern Recognition, pp. 42505–42515. Cited by: §2, Table 1.

[^61]: StarVLA- $alpha$: reducing complexity in vision-language-action systems. In Proc. of European Conference on Computer Vision, Cited by: §5.3, Table 2.

[^62]: 3d diffusion policy: generalizable visuomotor policy learning via simple 3d representations. In Proc. of Robotics: Science and Systems, Cited by: Table 2.

[^63]: Sigmoid loss for language image pre-training. In Proc. of IEEE Intl. Conf. on Computer Vision, pp. 11975–11986. Cited by: §5.4.

[^64]: Up-vla: a unified understanding and prediction model for embodied agent. In Proc. of Intl. Conf. on Machine Learning, Cited by: Table 2.

[^65]: Flowpolicy: enabling fast and robust 3d flow-based policy via consistency flow matching for robot manipulation. In Proc. of the AAAI Conf. on Artificial Intelligence, Vol. 39, pp. 14754–14762. Cited by: Table 2.

[^66]: Mole-vla: dynamic layer-skipping vision language action model via mixture-of-layers for efficient robot manipulation. In Proc. of the AAAI Conf. on Artificial Intelligence, Vol. 40, pp. 18764–18772. Cited by: §2.

[^67]: Dreamvla: a vision-language-action model dreamed with comprehensive world knowledge. In Proc. of Advances in Neural Information Processing Systems, Vol. 38, pp. 24195–24228. Cited by: §2, Table 1.

[^68]: Learning fine-grained bimanual manipulation with low-cost hardware. In Proc. of Robotics: Science and Systems, Cited by: §1, §4.3, §5.1, Table 2.

[^69]: Hermes: a unified self-driving world model for simultaneous 3d scene understanding and generation. In Proc. of IEEE Intl. Conf. on Computer Vision, pp. 27817–27827. Cited by: §3.

[^70]: Rt-2: vision-language-action models transfer web knowledge to robotic control. In Proc. of the Conference on Robot Learning, pp. 2165–2183. Cited by: §1, §1, §2, §3.
