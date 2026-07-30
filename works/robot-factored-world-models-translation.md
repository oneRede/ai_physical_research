---
sourceTitle: "Robot-Factored World Models via Robot Rendering"
sourceUrl: "https://arxiv.org/html/2607.22535v1"
sourceRequestedUrl: "https://arxiv.org/abs/2607.22535"
sourceAdapter: "generic"
sourceCapturedAt: "2026-07-30T10:33:32.106Z"
sourceConversionMethod: "defuddle"
sourceKind: "generic/article"
sourceLanguage: "en"
title: "通过机器人渲染实现机器人因子化世界模型"
authors: "Byungjun Kim, Taeksoo Kim, Hyunsoo Cha, Hanbyul Joo"
date: "2026-07-24"
arxivId: "2607.22535"
projectPage: "https://bjkim95.github.io/rofacto/"
topics: ["机器人学", "计算机视觉", "世界模型", "视频生成"]
sourceFigureCount: 10
pipelineRunId: "20260730"
pipelineSource: "translate/20260730/works-ready/robot-factored-world-models-translation.md"
---

# 通过机器人渲染实现机器人因子化世界模型

Byungjun Kim <sup>1</sup>  Taeksoo Kim <sup>1</sup>  Hyunsoo Cha <sup>1</sup>  Hanbyul Joo <sup>1,2</sup>
<sup>1</sup> 首尔国立大学   <sup>2</sup> RLWRLD
{byungjun.kim,taeksu98,243stephen,hbjoo}@snu.ac.kr

## 摘要

动作条件视频世界模型根据初始观测和动作信号预测未来观测。在机器人学中，动作通过两个不同的过程影响未来观测：首先由机器人本体和控制器将其实现为机器人运动，然后场景通过接触和物体运动做出响应。直接以动作命令为条件，要求世界模型学习实现过程本身；而以记录的未来状态为条件，则会泄露其本应预测的交互结果。我们提出**机器人因子化世界模型（robot-factored world models）**，将两个机器人特定因子移出世界模型。首先是**动作实现（action realization）**：每个命令通过机器人自身的控制器和运动学展开为部署时可用的**名义轨迹（nominal trajectory）**——这是一个中间信号，既避免了动作实现学习，也避免了未来状态泄露。其次是**机器人渲染**：该名义轨迹通过 URDF（统一机器人描述格式）渲染，将机器人的几何形状、运动学和外观从模型中分解出来，转化为显式渲染的机器人几何形状。为了解决深度歧义，我们将末端执行器深度与场景深度配对，提供超越图像平面重叠的接触和遮挡几何线索。相机感知的静态 RGB/深度上下文与渲染的机器人几何形状共同构成共享的视觉世界模型接口，该接口在不同视角和机器人具身间保持一致。模型将动作视为可见的机器人几何形状，学习物体如何响应。我们的实验表明，渲染接口优于基于向量条件的基线方法，并能在推理时泛化到未见过的机器人具身。我们进一步展示，通过重定向和渲染手部运动为机器人几何形状，我们的模型可以从人类演示生成机器人操作视频。

> 关键词：机器人世界模型，动作条件视频生成

## 1 引言

世界模型预测环境如何响应智能体的动作，从而可以在不实际执行的情况下预见行为结果 [^1]。视频生成模型的最新进展使得能够直接在像素空间预测未来观测的世界模型成为可能 [^2] [^3] [^4] [^5]。对于机器人学，这提示了一个吸引人的应用：以候选动作为条件，视频世界模型可以推演该动作将产生的未来，作为策略改进的学习环境 [^6] 或在实际执行前对行为进行排序 [^7]。这带来了一个核心问题：应该如何将机器人动作呈现给视频世界模型？

对于物理机器人，在发生任何场景交互之前，动作通过机器人本体特定的实现过程影响未来观测。因此，直接以机器人特定控制信号为条件的世界模型必须同时学习这些信号如何转化为机器人运动，以及场景如何响应该运动。现有的机器人视频世界模型在很大程度上将这一负担保留在网络内部：它们通过条件层注入机器人控制信号 [^3] [^4] [^5]，因此模型面向的信号仍然与机器人本体特定的动作表示绑定。视觉动作提示（Visual Action Prompts，VAP）[^8] 建议将动作表示为图像空间的视觉条件。然而，对于机器人世界模型，关键问题是应该渲染哪个机器人信号。VAP 通过渲染记录的机器人状态轨迹来实例化这一想法，这些轨迹与未来视频在视觉上对齐。然而，这些记录的状态是场景交互的实现结果：它们已包含接触、柔顺性、延迟和闭环修正信息。因此渲染它们虽然提供了对齐的提示，却泄露了世界模型本应预测的交互。挑战在于识别一个部署时可用的视觉条件信号，该信号将场景响应预测保留为模型的任务。

为了应对这一挑战，我们引入了一个围绕部署时可用的名义轨迹构建的机器人因子化视觉世界模型接口。给定一个动作序列，我们在观察场景交互之前将其通过机器人自身的控制器和运动学展开，产生名义轨迹：控制器在场景交互前生成的运动序列。这个轨迹在推理时可用，在自由空间中与实现运动一致，并在应该预测接触介导的交互处产生分歧。我们通过机器人 URDF 将名义轨迹渲染为相机对齐的机器人几何形状。相机感知的静态 RGB/深度上下文和渲染的机器人几何形状共同构成了固定和动态视角的共享视觉世界模型接口。模型将动作视为可见的机器人几何形状，并学习场景如何围绕它做出响应。这种渲染的机器人几何形状在图像空间中定位动作，但仅凭 RGB 无法解析机器人与场景的空间关系。因此，我们用末端执行器深度和场景深度来增强接口，提供超越图像平面重叠的接近性、接触和遮挡的几何线索。

由于该接口将动作表示为渲染的机器人几何形状而非机器人本体特定的控制信号，它将世界模型条件与机器人特定的动作表示解耦。这支持对未见机器人的具身泛化，以及从重定向的人类演示生成基于实际情况的机器人交互视频。

总之，我们的贡献如下：

（1）我们引入了一个部署现实的视觉世界模型接口，该接口将动作实现为名义机器人轨迹，渲染为相机对齐的 URDF 网格 RGB 和末端执行器深度，并通过相机感知的静态 RGB/深度上下文分解出场景外观和视角。

（2）我们通过用末端执行器深度和场景深度增强接口，使其具有深度感知能力，提供超越 RGB 图像平面重叠的接近性、遮挡和可能接触的几何线索。

（3）我们表明，渲染接口在机器人具身和运动源之间是共享的，实现了对未见机器人的具身泛化，以及从重定向的人类演示生成基于实际情况的机器人交互视频。

## 2 相关工作

#### 动作条件机器人世界模型

世界模型预测观测如何在动作下演化，已被用于想象、规划、策略评估和策略改进 [^1] [^9] [^10] [^11] [^12]。最近的视频世界模型将这一想法扩展到游戏、驾驶、导航和机器人操作的像素空间推演 [^13] [^14] [^7] [^6] [^3] [^4] [^5] [^15] [^16] [^17] [^18] [^19] [^20] [^21] [^22] [^2]。这些方法主要在动作如何表示和呈现给模型方面有所不同。一些模型直接以机器人特定的动作信号为条件 [^3] [^4] [^7]，而另一些则从大规模视频中学习潜在或抽象的动作表示 [^5] [^23] [^24] [^25] [^26] [^27]。直接动作接口仍然与机器人本体特定的控制空间绑定，而潜在动作方法则将具身几何形状隐藏在学习的抽象之后。我们的工作转而以场景交互前生成的名义轨迹为条件，暴露具身几何形状同时避免未来交互泄露。

#### 视频生成的视觉条件

视觉条件是一种标准机制，用于通过深度、姿态、边缘、掩码、光流、轨迹和参考帧等空间信号引导图像和视频扩散模型 [^28] [^29] [^30] [^31] [^32] [^33] [^34] [^35] [^36] [^37]。平行的文献将物体运动、相机运动、拖拽编辑和点轨迹视为直接的视频条件 [^38] [^39] [^40] [^41] [^42] [^43]。这些工作共同确立了视觉条件作为控制图像和视频生成的通用机制。

对于具身视频预测，视觉控制可以表示动作主体本身。视觉动作提示 [^8] 将机器人状态渲染为图像空间提示，而灵巧世界模型（Dexterous World Models）[^44] 以静态场景渲染和手部网格轨迹为条件进行视频生成，以建模交互引起的场景动态。我们与这些方法共享视觉条件原则，但在渲染哪个机器人信号方面有所不同：场景交互前计算的名义轨迹，而不是已经反映交互结果的记录状态。

## 3 方法

### 3.1 机器人因子化视觉世界模型接口

动作条件机器人世界模型从当前观测和提议的动作序列 $\bm{a}_{1:F}$ 预测未来视频 $\mathbf{V}_{1:F}$。直接以 $\bm{a}_{1:F}$ 为条件要求视频模型学习两个不同的过程：机器人动作如何转化为机器人运动，以及场景如何响应该运动。前者取决于机器人特定的具身和控制，而后者是世界模型应该解决的共享预测问题。因此，我们将动作实现与场景响应预测分离，并在世界模型条件之前显式建模实现过程。

我们用算子 $\Phi_{R}$ 表示这个实现过程，将动作映射为名义轨迹：

$$
\bm{q}_{1:F}=\Phi_{R}(\bm{a}_{1:F};\bm{q}_{0}),
$$

其中 $\bm{q}_{1:F}$ 是场景交互前由动作生成的名义轨迹。然后我们沿着目标相机轨迹 $\mathcal{C}_{1:F}$ 渲染这个名义轨迹：

$$
(\mathbf{M}^{\mathrm{rgb}}_{1:F},\mathbf{D}^{\mathrm{eef}}_{1:F})=\Pi_{R}(\bm{q}_{1:F};\mathcal{C}_{1:F}),
$$

其中 $\mathbf{M}^{\mathrm{rgb}}_{1:F}$ 是机器人网格 RGB 视频，$\mathbf{D}^{\mathrm{eef}}_{1:F}$ 是仅末端执行器的深度。渲染算子 $\Pi_{R}$ 使用机器人 URDF 和几何形状将名义轨迹投影到目标相机帧中。同时，初始场景状态 $\mathbf{S}_{0}$ 通过沿相同相机轨迹渲染的静态上下文流表示：

$$
(\mathbf{B}^{\mathrm{rgb}}_{1:F},\mathbf{D}^{\mathrm{scene}}_{1:F})=\Pi_{S}(\mathbf{S}_{0};\mathcal{C}_{1:F}),
$$

其中 $\mathbf{B}^{\mathrm{rgb}}_{1:F}$ 和 $\mathbf{D}^{\mathrm{scene}}_{1:F}$ 在目标相机坐标系中提供外观和几何上下文。这些流共同定义了世界模型的条件接口，该模型学习

$$
p_{\theta}\left(\mathbf{V}_{1:F}\mid\mathbf{B}^{\mathrm{rgb}}_{1:F},\mathbf{D}^{\mathrm{scene}}_{1:F},\mathbf{M}^{\mathrm{rgb}}_{1:F},\mathbf{D}^{\mathrm{eef}}_{1:F},\mathcal{T}\right),
$$

其中 $\mathcal{T}$ 是提供给视频模型的文本提示。它仅包含场景上下文，不包括预期动作或未来结果的描述。

这种分解将机器人特定的控制信号转换为模型面向的可见机器人几何形状。实现和渲染算子是固定的预处理步骤，留给学习模型的是预测渲染的机器人运动周围场景响应的共享问题。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x1.png)

图 1：视觉世界模型接口。静态上下文携带场景和视角；渲染的名义机器人几何形状携带动作；扩散模型预测场景响应。

### 3.2 名义轨迹条件

部署时的视觉条件信号必须满足两个要求：它必须在推理时可用，并且对由动作生成的机器人运动保持信息性。**原始动作** $\bm{a}_{1:F}$ 是策略、远程操作者或数据集控制器发出的内容，例如关节空间或任务空间控制命令。它在部署时可用，但其含义与特定机器人和控制器绑定。**名义轨迹** $\bm{q}_{1:F}$ 位于动作和交互之间：它是在观察场景交互之前，由机器人自身控制器和运动学生成的仅机器人运动。它在部署时可用，并且独立于未来的交互结果。**已实现状态** $\bm{x}_{1:F}$ 是在场景中执行动作后记录的机器人状态。它在视觉上与未来视频对齐，但它已包含接触、柔顺性、延迟、失败和其他交互结果。将已实现状态渲染为提示可作为预言诊断；部署时可用的条件信号是名义轨迹。图 2 说明了为什么名义轨迹是世界模型条件的适当中间信号。

这些信号之间的不匹配可以分解为两个差距。**动作实现差距（action-realization gap）**是原始动作与控制器生成的名义运动之间的差异。**名义-已实现差距（nominal-realized gap）**是名义轨迹与在接触丰富的推演中实际观察到的状态之间的差异。我们的因子化将第一个差距分配给机器人特定的实现过程，并将第二个差距连同物体运动和遮挡变化留给世界模型。每个机器人用自己的控制器实例化 $\Phi_{R}$，因此在视频模型接收其视觉条件流之前，机器人特定的动作语义就已解决。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x2.png)

图 2：动作到状态实现差距。（a）机器人特定的控制器和硬件约束在原始动作和名义轨迹之间产生差距。（b）场景交互在名义轨迹和已实现状态之间产生差距。名义轨迹是部署时可用的条件信号。

### 3.3 相机感知静态上下文

方程 3 中定义的静态上下文流为世界模型提供了初始场景状态的相机对齐表示。它提供独立于机器人交互的场景外观、场景深度和视角信息，因此模型可以专注于交互引起的变化，而静态流保留场景内容 [^44]。

当静态场景表示和相机轨迹可用时，$\Pi_{S}$ 沿着与目标视频使用的相同 $\mathcal{C}_{1:F}$ 产生静态场景 RGB 和深度。对于固定相机，我们通过在预测范围内重复初始帧来实例化相同的流。这种形式为固定和动态视图片段提供了一个通用的相机对齐条件接口，而动作信息则通过渲染的名义机器人几何形状单独输入。

### 3.4 渲染的机器人动作提示

方程 2 中的渲染器 $\Pi_{R}$ 将名义轨迹转换为目标相机帧中渲染的机器人几何形状。我们将机器人渲染为 URDF 网格 RGB，保留连杆几何形状、腕部偏移、夹持器形状和末端执行器结构。这使得视频模型在与目标视频相同的视觉坐标中看到动作。

RGB 网格渲染在图像空间中定位机器人，而深度解决了与物体重叠的末端执行器是在物体前面、后面还是处于接触深度。因此，我们在静态场景深度 $\mathbf{D}^{\mathrm{scene}}_{1:F}$ 旁边包含仅末端执行器深度 $\mathbf{D}^{\mathrm{eef}}_{1:F}$。我们将深度渲染集中在最有可能发生交互的末端执行器上。这些深度信号共同有助于消除超越表观 2D 重叠的接近性、可能的接触和遮挡顺序的歧义。

### 3.5 学习场景响应模型

我们用潜在视频扩散模型实例化方程 4。遵循灵巧世界模型 [^44] 的残差动力学形式，我们使用视频修复主干网络 [^45]，其中静态上下文视频作为条件输入，并在预测范围内应用全掩码。因此，模型学习在静态场景上下文和渲染的机器人几何形状条件下生成交互引起的场景变化。

所有条件流都由主干模型 [^47] 的预训练视频 VAE [^46] 编码。静态 RGB 上下文、渲染的机器人 RGB、场景深度和末端执行器深度流被编码并与噪声视频潜在变量连接：

$$
\bm{c}=\left[\mathcal{E}(\mathbf{B}^{\mathrm{rgb}}_{1:F}),\mathcal{E}(\mathbf{M}^{\mathrm{rgb}}_{1:F}),\mathcal{E}(\mathbf{D}^{\mathrm{scene}}_{1:F}),\mathcal{E}(\mathbf{D}^{\mathrm{eef}}_{1:F})\right].
$$

这里 $\mathcal{E}$ 表示应用于每个条件流的预训练视频 VAE 编码器。

训练最小化潜在流匹配目标 [^48] [^49]。对于采样的噪声潜在变量 $\bm{\epsilon}$ 和噪声水平 $\sigma$，我们形成 $\bm{z}_{\sigma}=(1-\sigma)\bm{z}_{0}+\sigma\bm{\epsilon}$ 并使用目标速度 $\bm{u}=\bm{\epsilon}-\bm{z}_{0}$：

$$
\mathcal{L}=\mathbb{E}_{\bm{z}_{0},\sigma,\bm{\epsilon}}\left[\left\|\bm{u}-v_{\theta}\left(\bm{z}_{\sigma},\sigma\mid\bm{c},\mathcal{T}\right)\right\|_{2}^{2}\right].
$$

文本提示 $\mathcal{T}$ 仅包含场景上下文，不包括预期动作或未来结果的描述。

## 4 实验

### 4.1 实验设置

#### 数据集

我们使用 DROID [^50] 的增强外参子集和通过在 RoboCasa [^51] 中执行类人 VLA DiT4DiT [^52] 在 24 个任务上收集的 RoboCasa-GR1 推演数据，每个任务 50 个回合。两个数据集都被处理成 81 帧、$480{\times}832$、16 fps 的片段，产生 41,642 个 DROID 片段和 9,380 个 RoboCasa-GR1 片段。评估使用 256 个保留的 DROID 片段和 128 个保留的 RoboCasa-GR1 片段。HRDexDB [^53] 和 DexYCB [^54] 提供定性研究。

#### 名义化

对于 DROID，原始远程操作关节和夹持器目标在无场景、仅机器人的 Isaac Lab [^55] 环境中重放，因此 Panda 控制器和执行限制产生物理上可跟踪的名义轨迹。对于 RoboCasa-GR1，DiT4DiT [^52] 控制器动作从片段起始状态在无碰撞影子推演中重放，以获得 Fourier GR-1 名义状态。在这两种情况下，名义轨迹在推理时可用，并被渲染为机器人条件信号。

#### 基线

我们与 **Ctrl-World** [^4]（一个基于 Stable Video Diffusion（SVD）[^56] 构建的 7-DoF 笛卡尔姿态条件世界模型）和 **AdaLN** [^57] **状态向量**基线（馈送相同的部署时可用名义轨迹作为数值状态值）进行比较。对于基于 SVD 的比较，我们用姿态条件或渲染接口重新训练相同的主干网络，并在 DROID 保留集上评估。对于 Wan [^47] /Wan2.1-Fun InP [^45]，我们比较数值状态向量条件与我们的带有网格和深度的渲染接口。

### 4.2 主要比较

表 1：主要动作接口比较。每个主干单元格列出模型、其训练分辨率和训练数据（D+R：DROID+RoboCasa-GR1）。SVD 1.5B 仅在 DROID 上训练，因此不在 RoboCasa-GR1 上评估（短划线）；由于较低分辨率会提高重建指标，数字仅在主干内可比。DROID 和 RoboCasa-GR1 分别报告而非平均；每个主干的最佳结果加粗。

<table><tbody><tr><td rowspan="2">主干</td><td rowspan="2">方法</td><td colspan="3">DROID</td><td colspan="3">RoboCasa-GR1</td></tr><tr><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td></tr><tr><td rowspan="2">SVD 1.5B $192{\times}320$，DROID</td><td>Ctrl-World 姿态</td><td>23.15</td><td>0.884</td><td>0.104</td><td>–</td><td>–</td><td>–</td></tr><tr><td>渲染网格（我们的）</td><td>25.05</td><td>0.899</td><td>0.091</td><td>–</td><td>–</td><td>–</td></tr><tr><td rowspan="2">Wan 2.1 14B $480{\times}832$，D+R</td><td>AdaLN 状态向量</td><td>18.57</td><td>0.824</td><td>0.224</td><td>17.67</td><td>0.835</td><td>0.194</td></tr><tr><td>渲染网格 + 末端执行器/场景深度（我们的）</td><td>21.87</td><td>0.859</td><td>0.178</td><td>24.61</td><td>0.889</td><td>0.131</td></tr></tbody></table>

#### 定量比较

我们首先询问渲染接口是否是比基于向量或姿态条件的替代方案更好的模型面向动作表示。我们报告 PSNR、SSIM 和 LPIPS [^58]，按片段计算并在评估集上平均。在主干控制的 SVD 比较中，渲染接口优于 Ctrl-World 风格的笛卡尔姿态条件。在联合训练的 Wan 设置中，渲染接口在 DROID 和 RoboCasa-GR1 上都优于 AdaLN 状态向量基线。这些结果支持核心接口设计：渲染的机器人几何形状为视频模型提供了机器人运动的直接像素空间证据，而数值动作或状态条件需要模型学习额外的定位映射。由于主干单元格在分辨率和训练数据上有所不同，我们仅在每个主干内进行比较。

#### 定性比较

![Refer to caption](https://arxiv.org/html/2607.22535v1/x3.png)

图 3：定性比较。渲染的机器人几何形状定位机器人驱动的场景变化，而深度有助于解决与接触相关的接近性和遮挡。

图 3 强调了重建指标最不具诊断性的情况：小物体运动、短暂接触、遮挡顺序、错过的抓取和无接触结果。AdaLN 状态向量基线通常保留场景外观，但预期动作较弱或位置错误。相比之下，渲染的机器人几何形状直接在目标相机帧中暴露动作，因此预测的场景变化更好地定位在机器人运动和接触区域周围。

### 4.3 动作实现和深度消融

表 2 隔离了第 3.2 节和第 3.4 节中引入的两个因子：名义轨迹条件和深度感知渲染接口。所有变体都在 DROID 上训练和评估，保持数据集和主干固定。

表 2：DROID 上的消融研究。

| 变体 | PSNR $\uparrow$ | SSIM $\uparrow$ | LPIPS $\downarrow$ |
| --- | --- | --- | --- |
| 原始动作网格 | 21.57 | 0.860 | 0.175 |
| 名义网格 | 22.44 | 0.872 | 0.164 |
| 名义网格 $+$ 末端执行器/场景深度 | 23.08 | 0.874 | 0.161 |

#### 名义轨迹条件

前两行比较原始动作网格与名义轨迹渲染，两种情况下都仅以网格 RGB 为条件。这隔离了动作实现差距：原始动作网格在机器人局部实现之前暴露命令，而名义轨迹渲染显示部署时可用的控制器生成的机器人运动。名义轨迹渲染在所有指标上都优于原始动作网格（表 2），表明将动作实现分配给机器人局部堆栈会从部署时可用的运动产生更好对齐的渲染机器人几何形状。

#### 深度感知渲染接口

最后一行在名义轨迹渲染之上添加末端执行器和场景深度。这评估了第 3.4 节的深度感知接口：RGB 网格渲染在图像平面中定位机器人运动，而深度暴露与接触相关的 3D 接近性和遮挡顺序。添加深度进一步改善了所有三个指标（表 2）。图 4 说明了定性效果：当省略深度时，即使物体更远，模型也可以将图像平面重叠视为接触。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x4.png)

图 4：深度消融。末端执行器和场景深度有助于区分与接触相关的接近性和图像平面重叠。

### 4.4 定性接口分析

我们研究渲染接口的两个定性属性：提示跟随和具身泛化。图 5 通过改变渲染的机器人运动来探测提示跟随，图 6 通过改变机器人几何形状来探测具身泛化。

#### 反事实轨迹编辑

在真实的 DROID 回合中，我们保持初始场景固定，并将原始名义机器人渲染与在相同 Panda 运动学下由 cuRobo [^59] 重新求解的编辑轨迹进行比较。图 5 显示，仅改变渲染的名义轨迹就会改变预测的场景响应。这表明模型使用渲染的机器人几何形状作为模型面向的动作信号。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x5.png)

图 5：提示跟随探测。仅改变渲染的名义轨迹就会改变预测的场景响应。

#### 未见具身组合

图 6 使用 HRDexDB [^53]，它提供了一个未见的 xArm 6 手臂和 Inspire F1 手的配对。我们将保留的机器人运动渲染为相机对齐的 URDF 几何形状，并通过相同的训练模型传递。条件机制保持固定，只有渲染的机器人几何形状改变。这展示了接口级别的具身泛化：一旦表示为渲染几何形状，未见的机器人几何形状可以由相同的视觉条件路径消费。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x6.png)

图 6：零样本具身组合。HRDexDB 包含未见的 xArm 6–Inspire F1 配对；渲染的 URDF 运动仍然驱动预测的场景响应。

### 4.5 应用：人类演示到机器人视频

#### 人类演示到机器人视频

作为共享渲染接口的下游应用，图 7 采用 DexYCB [^54] 人类操作视频，将手部运动重定向到机器人 [^60] [^61]，并将结果渲染为用于机器人数据的相同网格和深度接口。由于运动源自重定向的人类演示，这个应用说明渲染接口与控制器特定的动作表示解耦：无论运动源如何，世界模型都消费渲染的机器人几何形状。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x7.png)

图 7：应用：人类演示到机器人视频。重定向的人类运动被渲染为机器人几何形状，并转换为机器人交互推演。

## 5 结论与局限性

我们提出机器人因子化世界模型，将动作实现和机器人渲染从视频世界模型中分解出来。动作被实现为部署时可用的名义轨迹，并渲染为相机对齐的机器人几何形状，因此模型将动作接收为可见的机器人几何形状，并学习场景如何围绕它做出响应。在 DROID 和 RoboCasa-GR1 上的实验表明，渲染接口优于基于向量条件的基线，并且相同的接口扩展到未见的机器人具身和重定向的人类演示。

虽然我们基于渲染的动作接口改善了动作跟随和泛化，但仍存在几个局限性。我们的接口需要已知的机器人 URDF 和相机到机器人的标定，这在标定设置中是标准的，但对每个新具身都需要。当相机移动时，静态上下文流假设静态场景表示，这在仿真中是完全可用的，但在真实世界中只是部分观察到的；前馈 3D 重建可以提供它，尽管缩小外观差距可能需要额外的训练。最后，DROID 回合主要是成功的，因此模型很少看到抓取失败、滑动和错误预测的接触；收集失败数据将在这些情况下提高鲁棒性。

## 参考文献

## 附录 A 接口构建细节

#### 数据集特定的名义化

正文区分了原始动作、名义仅机器人轨迹和已实现机器人状态。对于 DROID [^50]，演示通过人类远程操作收集，数据集提供标准化的机器人动作表示，包括关节空间和末端执行器空间动作。在我们的预处理中，我们使用记录的关节和夹持器目标序列作为名义重放的原始动作。这些目标可以比手臂物理跟踪的速度更快地指定；因此直接渲染它们通常会产生领先于记录视频的运动。我们在无场景、仅机器人的 Isaac Lab [^55] 环境中重放动作，其中 Panda 控制器和执行限制将原始动作序列塑造成物理上可跟踪的名义轨迹。

对于 RoboCasa-GR1，DiT4DiT [^52] 策略输出 29D 控制器动作，而渲染器消费 39D Fourier GR-1 关节/手/腰部状态。我们从片段起始状态在无碰撞影子推演中重放每个动作序列，并渲染生成的 Fourier GR-1 名义状态。RoboCasa-GR1 语料库由通过在 RoboCasa [^51] 模拟器之上使用 GR00T [^62] 项目发布的 Fourier GR-1 类人桌面设置执行 DiT4DiT VLA 策略生成的回合轨迹组成。在两个机器人数据集中，世界模型都接收名义机器人几何形状作为其部署兼容的条件信号。

#### 静态上下文和场景描述

静态上下文流遵循每个目标片段的相机轨迹，同时移除机器人交互。对于固定视图的 DROID 片段，RGB 上下文是在预测范围内重复的初始观测。当启用场景深度时，我们使用 FoundationStereo [^63] 从 ZED 立体观测估计的度量深度，并将其对齐到相同的固定相机帧。对于 RoboCasa-GR1 动态视图片段，模拟器提供相机轨迹和无机器人场景，因此我们沿着该轨迹渲染静态 RGB 和静态深度，不包含机器人。这种构造提供相机感知的场景上下文，同时将动作信息留在名义机器人网格和末端执行器深度流中。

每个片段与 Qwen3-VL [^64] 生成的场景描述 $\mathcal{T}$ 配对。这个语言条件仅描述可见的场景外观和上下文。它提供场景级语义上下文，而名义机器人几何形状携带动作信息。

## 附录 B 模型、基线和评估细节

#### Wan 适配

Wan [^47] 2.1 14B 变体基于来自 VideoX-Fun [^45] 的预训练 Wan2.1-Fun-V1.1-14B-InP 图像到视频模型构建。我们通过两个可训练的添加来适配它，同时冻结所有其他预训练权重：（i）扩展补丁嵌入投影以摄取额外的条件通道，（ii）将秩为 64、$\alpha{=}64$ 的 LoRA [^65] 权重附加到变换器。基础模型有 36 个输入潜在通道。每个渲染流被 VAE 编码为 16 通道潜在变量，并沿通道轴连接，因此补丁嵌入增长到 $36+C$ 个输入通道，其中 $C$ 是变体中的条件通道数。扩展的补丁嵌入权重与 LoRA 权重一起训练。训练使用潜在流匹配目标 [^48] [^49]：噪声潜在变量在数据和噪声之间线性插值，DiT 预测相应的速度。

#### 条件通道

每个渲染流贡献 16 个潜在通道：

- **仅网格 RGB**（原始动作网格或名义轨迹渲染）：$C=16$。
- **网格 RGB $+$ 末端执行器深度 $+$ 场景深度**：$C=48$。

静态 RGB 上下文遵循 Wan2.1-Fun InP 图像条件路径，额外的渲染流路径携带机器人网格和深度条件。静态视频贡献 16 个潜在通道，修复掩码贡献四个时间压缩的掩码通道。我们将掩码设置为所有已知像素（在我们的约定中 $m{=}1$），因此静态流保留场景外观，而渲染的机器人流解释动作条件的变化。受灵巧世界模型 [^44] 启发，这将静态场景条件与动作条件交互动力学分离。

#### AdaLN 状态向量基线

我们使用 AdaLN [^57] 条件将数值状态向量注入扩散变换器。对于 DROID，AdaLN 基线使用每帧 7D 名义末端执行器状态 $[x,y,z,\mathrm{roll},\mathrm{pitch},\mathrm{yaw},g]$，由 Panda 正向运动学从仅机器人的 Isaac Lab 名义关节推演和名义夹持器标量 $g$ 计算。对于 RoboCasa-GR1，DiT4DiT [^52] 策略动作是 29D 控制器动作，排序为左臂 7、右臂 7、左手 6、右手 6 和腰部 3。从片段起始状态重放后，AdaLN 基线使用生成的 39D 名义 Fourier GR-1 状态，排序为左臂 7、右臂 7、左手 11、右手 11 和腰部 3。对于联合 DROID+RoboCasa 训练，DROID 填充前 7 个条目，RoboCasa 填充零填充 46D 联合向量的后 39 个条目。为每个 Wan 潜在帧堆叠四个原始帧状态，给出 184D AdaLN 输入。

表 3：Wan 2.1 14B 世界模型的训练超参数。

| 超参数 | 值 |
| --- | --- |
| 基础模型 | Wan2.1-Fun-V1.1-14B-InP |
| LoRA 秩 / $\alpha$ | 64 / 64 |
| 可训练参数 | 扩展补丁嵌入 $+$ LoRA |
| 优化器 | AdamW [^66]（$\beta_{1}{=}0.9$，$\beta_{2}{=}0.95$）|
| 权重衰减 | 0.01 |
| 学习率 | $1\times 10^{-4}$ |
| 学习率调度 | 余弦重启，100 预热步 |
| 梯度裁剪 | 1.0 |
| 批量大小 / 梯度累积 | 1 / 4 每 GPU |
| 精度 | bf16 |
| 梯度检查点 | 启用 |
| 分辨率 | $480\times 832$ |
| 片段长度 / fps | 81 帧 / 16 |

#### 推理

Wan 2.1 14B 变体使用 LightX2V [^67] CFG 和步骤蒸馏的 LoRA 在 4 个去噪步骤和引导尺度 1.0 下采样。基于 SVD 的变体在 $192{\times}320$ 下使用 25 个 Euler 步。由于我们通过补丁嵌入投影和任务特定 LoRA 权重扩展预训练的 Wan 2.1 14B 变换器，LightX2V 蒸馏 LoRA 直接应用并将去噪步骤减少 $10{\times}$。

## 附录 C 预言状态诊断

记录的已实现机器人状态在离线数据集中可用，因此可以用从这些状态渲染的条件提示训练视频扩散模型。这些状态仅在动作与场景交互后才能观察到，使其成为预言诊断。表 4 将此问题分为三种设置；所有设置都仅以渲染的机器人网格 RGB 为条件，不包含深度。名义/名义是我们的部署兼容设计：训练和推理提示都从场景交互前计算的名义机器人状态渲染。记录/记录是预言诊断，因为训练和推理都使用已实现的未来状态。记录/名义使用从记录的已实现状态渲染的提示训练视频模型，但在推理时使用从名义机器人状态渲染的提示，匹配部署时可用性约束。名义/名义行给出最强的部署兼容设置，显示了使用场景交互前可用的名义轨迹提示进行训练和推理的价值。

表 4：预言状态条件诊断。记录/记录使用未来已实现的机器人状态作为预言诊断；记录/名义测量记录状态训练提示和部署时名义提示之间的兼容性。

| 数据集 | 训练状态 | 推理状态 | PSNR | SSIM | LPIPS |
| --- | --- | --- | --- | --- | --- |
| DROID | 名义 | 名义 | 21.74 | 0.856 | 0.179 |
| DROID | 记录 | 记录 | 22.37 | 0.861 | 0.174 |
| DROID | 记录 | 名义 | 21.12 | 0.846 | 0.187 |
| RoboCasa-GR1 | 名义 | 名义 | 25.70 | 0.904 | 0.120 |
| RoboCasa-GR1 | 记录 | 记录 | 28.26 | 0.918 | 0.112 |
| RoboCasa-GR1 | 记录 | 名义 | 24.69 | 0.897 | 0.126 |

## 附录 D 人类演示到机器人视频管道

图 8 说明了如何将带有每帧 21 点 3D 手部关键点的 DexYCB [^54] RGB-D 片段转换为用于机器人演示的相同渲染机器人接口。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x8.png)

图 8：人类演示到机器人视频管道。人类运动在传递给世界模型之前被转换为用于机器人数据的相同渲染网格和深度接口。

**场景修复。** 当初始帧包含可见的人手时，我们可选择用视频修复模型 [^68] 移除它。这为静态 RGB 上下文产生无机器人场景图像；初始帧中没有可见手的片段使用原始场景观测。

**手部重定向。** 我们使用 AnyTeleop [^60] 重定向设置将人类指尖运动映射到机器人手。

**手臂重定向。** 腕部轨迹和手部方向定义目标末端执行器运动，该运动通过 PyRoki [^61] 逆运动学映射到机器人手臂。

**网格渲染和条件组装。** 组合的 Panda 手臂和 Inspire 手轨迹使用标定的机器人几何形状在 DexYCB 相机帧中渲染。然后我们组装机器人世界模型使用的相同条件流：静态 RGB、静态深度、机器人网格视频和末端执行器深度。相同的训练世界模型接收这些流并从人类源运动生成机器人操作推演。

## 附录 E 额外定性结果

#### 零样本多具身推理

![Refer to caption](https://arxiv.org/html/2607.22535v1/x9.png)

图 9：零样本多具身推理。来自 DexMimicGen 的双臂 Franka Panda 设置被渲染为机器人几何形状并传递给相同的视频模型。

图 9 探测 DexMimicGen [^69] 场景上的零样本推理，该场景带有两个 Franka Panda 手臂和平行夹爪。由于条件信号是相机帧中渲染的机器人几何形状，相同的视频模型可以通过渲染接口消费新的多臂提示。这个定性结果说明了渲染接口如何通过相同的条件路径支持新的具身和末端执行器配置。

#### DROID 和 RoboCasa-GR1 比较

图 10 提供了额外的定性比较，左侧是 DROID 示例，右侧是 RoboCasa-GR1 示例。在这些示例中，AdaLN 状态向量条件可以保留场景外观，但通常弱实现或错误放置预期的机器人运动。渲染的机器人几何形状使动作在目标相机帧中可见，因此生成的视频更一致地遵循命令的运动，并将场景变化定位在机器人和接触区域周围。

![Refer to caption](https://arxiv.org/html/2607.22535v1/x10.png)

图 10：DROID 和 RoboCasa-GR1 上的额外定性比较。渲染的机器人几何形状在目标相机帧中暴露动作，并且比 AdaLN 状态向量条件更一致地遵循命令的机器人运动。

[^1]: D. Ha and J. Schmidhuber. Recurrent world models facilitate policy evolution. In *NeurIPS*, 2018.

[^2]: A. Bar, G. Zhou, D. Tran, T. Darrell, and Y. LeCun. Navigation world models. In *CVPR*, 2025.

[^3]: F. Zhu, H. Wu, S. Guo, Y. Liu, C. Cheang, and T. Kong. Irasim: A fine-grained world model for robot manipulation. In *ICCV*, 2025.

[^4]: Y. Guo, L. X. Shi, J. Chen, and C. Finn. Ctrl-world: A controllable generative world model for robot manipulation. In *ICLR*, 2026.

[^5]: S. Gao, W. Liang, K. Zheng, A. Malik, S. Ye, S. Yu, W.-C. Tseng, Y. Dong, K. Mo, C.-H. Lin, Q. Ma, S. Nah, L. Magne, J. Xiang, Y. Xie, R. Zheng, D. Niu, Y. L. Tan, K. Zentner, G. Kurian, S. Indupuru, P. Jannaty, J. Gu, J. Zhang, J. Malik, P. Abbeel, M.-Y. Liu, Y. Zhu, J. Jang, and L. Fan. Dreamdojo: A generalist robot world model from large-scale human videos. *arXiv preprint arXiv:2602.06949*, 2026.

[^6]: A. K. Sharma, Y. Sun, N. Lu, Y. Zhang, J. Liu, and S. Yang. World-gymnast: Training robots with reinforcement learning in a world model. *arXiv preprint arXiv:2602.02454*, 2026.

[^7]: J. Quevedo, A. K. Sharma, Y. Sun, V. Suryavanshi, P. Liang, and S. Yang. Worldgym: World model as an environment for policy evaluation. *arXiv preprint arXiv:2506.00613*, 2025.

[^8]: Y. Wang, C. Wen, H. Guo, S. Peng, M. Qin, H. Bao, X. Zhou, and R. Hu. Precise action-to-video generation through visual action prompts. In *ICCV*, 2025.

[^9]: D. Hafner, T. Lillicrap, I. Fischer, R. Villegas, D. Ha, H. Lee, and J. Davidson. Learning latent dynamics for planning from pixels. In *ICML*, 2019.

[^10]: D. Hafner, J. Pasukonis, J. Ba, and T. Lillicrap. Mastering diverse domains through world models. *arXiv preprint arXiv:2301.04104*, 2023.

[^11]: P. Wu, A. Escontrela, D. Hafner, P. Abbeel, and K. Goldberg. Daydreamer: World models for physical robot learning. In *CoRL*, 2023.

[^12]: N. Hansen, H. Su, and X. Wang. Td-mpc2: Scalable, robust world models for continuous control. In *ICLR*, 2024.

[^13]: N. Agarwal, A. Ali, M. Bala, Y. Balaji, E. Barker, T. Cai, P. Chattopadhyay, Y. Chen, Y. Cui, Y. Ding, et al. Cosmos world foundation model platform for physical ai. *arXiv preprint arXiv:2501.03575*, 2025.

[^14]: J. Jang, S. Ye, Z. Lin, J. Xiang, J. Bjorck, Y. Fang, F. Hu, S. Huang, K. Kundalia, Y.-C. Lin, et al. Dreamgen: Unlocking generalization in robot learning through video world models. *arXiv preprint arXiv:2505.12705*, 2025.

[^15]: J. Bruce, M. D. Dennis, A. Edwards, J. Parker-Holder, Y. Shi, E. Hughes, M. Lai, A. Mavalankar, R. Steigerwald, C. Apps, et al. Genie: Generative interactive environments. In *ICML*, 2024.

[^16]: E. Alonso, A. Jelley, V. Micheli, A. Kanervisto, A. Storkey, T. Pearce, and F. Fleuret. Diffusion for world modeling: Visual details matter in atari. *NeurIPS*, 2024.

[^17]: D. Valevski, Y. Leviathan, M. Arar, and S. Fruchter. Diffusion models are real-time game engines. In *ICLR*, 2025.

[^18]: Z. Yang, Y. Chen, J. Wang, S. Manivasagam, W.-C. Ma, A. J. Yang, and R. Urtasun. Unisim: A neural closed-loop sensor simulator. In *CVPR*, 2023.

[^19]: J. Wu, S. Yin, N. Feng, X. He, D. Li, J. Hao, and M. Long. ivideogpt: Interactive videogpts are scalable world models. *NeurIPS*, 2024.

[^20]: H. Zhu, Y. Wang, J. Zhou, W. Chang, Y. Zhou, Z. Li, J. Chen, C. Shen, J. Pang, and T. He. Aether: Geometric-aware unified world modeling. In *ICCV*, 2025.

[^21]: J. Zhou, H. Gao, V. Voleti, A. Vasishta, C.-H. Yao, M. Boss, P. Torr, C. Rupprecht, and V. Jampani. Stable virtual camera: Generative view synthesis with diffusion models. In *ICCV*, 2025.

[^22]: L. Russell, A. Hu, L. Bertoni, G. Fedoseev, J. Shotton, E. Arani, and G. Corrado. Gaia-2: A controllable multi-view generative world model for autonomous driving. *arXiv preprint arXiv:2503.20523*, 2025.

[^23]: S. Gao, S. Zhou, Y. Du, J. Zhang, and C. Gan. Adaworld: Learning adaptable world models with latent actions. *arXiv preprint arXiv:2503.18938*, 2025.

[^24]: M. Huang, Y. Xiang, Z. Liang, J. Huang, J. Wang, Z. Xu, F. Tan, H. Zhou, M. Yang, and G. Che. Coworld-vla: Thinking in a multi-expert world model for autonomous driving. *arXiv preprint arXiv:2605.10426*, 2026.

[^25]: A. Liang, P. Czempin, M. Hong, Y. Zhou, E. Biyik, and S. Tu. Clam: Continuous latent action models for robot learning from unlabeled demonstrations. *arXiv preprint arXiv:2505.04999*, 2025.

[^26]: Q. Bu, Y. Yang, J. Cai, S. Gao, G. Ren, M. Yao, P. Luo, and H. Li. Univla: Learning to act anywhere with task-centric latent actions. *arXiv preprint arXiv:2505.06111*, 2025.

[^27]: Q. Garrido, T. Nagarajan, B. Terver, N. Ballas, Y. LeCun, and M. Rabbat. Learning latent action world models in the wild. *arXiv preprint arXiv:2601.05230*, 2026.

[^28]: L. Zhang, A. Rao, and M. Agrawala. Adding conditional control to text-to-image diffusion models. In *ICCV*, 2023.

[^29]: Y. Zhang, Y. Wei, X. ZHANG, W. Zuo, Q. Tian, et al. Controlvideo: Training-free controllable text-to-video generation. In *ICLR*, 2024.

[^30]: X. Wang, H. Yuan, S. Zhang, D. Chen, J. Wang, Y. Zhang, Y. Shen, D. Zhao, and J. Zhou. Videocomposer: Compositional video synthesis with motion controllability. *NeurIPS*, 2023.

[^31]: Y. Guo, C. Yang, A. Rao, Z. Liang, Y. Wang, Y. Qiao, M. Agrawala, D. Lin, and B. Dai. Animatediff: Animate your personalized text-to-image diffusion models without specific tuning. *arXiv preprint arXiv:2307.04725*, 2023.

[^32]: Z. Wang, Z. Yuan, X. Wang, Y. Li, T. Chen, M. Xia, P. Luo, and Y. Shan. Motionctrl: A unified and flexible motion controller for video generation. In *ACM SIGGRAPH*, 2024.

[^33]: S. Yin, C. Wu, J. Liang, J. Shi, H. Li, G. Ming, and N. Duan. Dragnuwa: Fine-grained control in video generation by integrating text, image, and trajectory. *arXiv preprint arXiv:2308.08089*, 2023.

[^34]: Z. Zhang, J. Liao, M. Li, Z. Dai, B. Qiu, S. Zhu, L. Qin, and W. Wang. Tora: Trajectory-oriented diffusion transformer for video generation. In *CVPR*, 2025.

[^35]: M. Niu, X. Cun, X. Wang, Y. Zhang, Y. Shan, and Y. Zheng. Mofa-video: Controllable image animation via generative motion field adaptions in frozen image-to-video diffusion model. In *ECCV*, 2024.

[^36]: H. Zhou, C. Wang, R. Nie, J. Liu, D. Yu, Q. Yu, and C. Wang. Trackgo: A flexible and efficient method for controllable video generation. In *AAAI*, 2025.

[^37]: Z. Jiang, Z. Han, C. Mao, J. Zhang, Y. Pan, and Y. Liu. Vace: All-in-one video creation and editing. In *ICCV*, 2025.

[^38]: D. Geng, C. Herrmann, J. Hur, F. Cole, S. Zhang, T. Pfaff, T. Lopez-Guevara, Y. Aytar, M. Rubinstein, C. Sun, et al. Motion prompting: Controlling video generation with motion trajectories. In *CVPR*, 2025.

[^39]: J. Shin, Z. Li, R. Zhang, J.-Y. Zhu, J. Park, E. Shechtman, and X. Huang. Motionstream: Real-time video generation with interactive motion controls. *arXiv preprint arXiv:2511.01266*, 2025.

[^40]: H. Qiu, Z. Chen, Z. Wang, Y. He, M. Xia, and Z. Liu. Freetraj: Tuning-free trajectory control in video diffusion models. *arXiv preprint arXiv:2406.16863*, 2024.

[^41]: W.-D. K. Ma, J. P. Lewis, and W. B. Kleijn. Trailblazer: Trajectory control for diffusion-based video generation. In *ACM SIGGRAPH Asia*, 2024.

[^42]: Y. Jain, A. Nasery, V. Vineet, and H. Behl. Peekaboo: Interactive video generation via masked-diffusion. In *CVPR*, 2024.

[^43]: Y. Li, X. Wang, Z. Zhang, Z. Wang, Z. Yuan, L. Xie, Y. Shan, and Y. Zou. Image conductor: Precision control for interactive video synthesis. In *AAAI*, 2025.

[^44]: B. Kim, T. Kim, J. Lee, and H. Joo. Dexterous world models. *arXiv preprint arXiv:2512.17907*, 2025.

[^45]: aigc apps. Videox-fun: A video generation pipeline for diffusion transformer, 2026. URL [https://github.com/aigc-apps/VideoX-Fun](https://github.com/aigc-apps/VideoX-Fun).

[^46]: D. P. Kingma and M. Welling. Auto-encoding variational bayes. In *ICLR*, 2014.

[^47]: Team Wan, A. Wang, B. Ai, B. Wen, C. Mao, C.-W. Xie, D. Chen, F. Yu, H. Zhao, J. Yang, et al. Wan: Open and advanced large-scale video generative models. *arXiv preprint arXiv:2503.20314*, 2025.

[^48]: Y. Lipman, R. T. Q. Chen, H. Ben-Hamu, M. Nickel, and M. Le. Flow matching for generative modeling. In *ICLR*, 2023.

[^49]: X. Liu, C. Gong, and Q. Liu. Flow straight and fast: Learning to generate and transfer data with rectified flow. In *ICLR*, 2023.

[^50]: A. Khazatsky, K. Pertsch, S. Nair, A. Balakrishna, S. Dasari, S. Karamcheti, S. Nasiriany, M. K. Srirama, L. Y. Chen, K. Ellis, et al. Droid: A large-scale in-the-wild robot manipulation dataset. In *RSS*, 2024.

[^51]: S. Nasiriany, A. Maddukuri, L. Zhang, A. Parikh, A. Lo, A. Joshi, A. Mandlekar, and Y. Zhu. Robocasa: Large-scale simulation of everyday tasks for generalist robots. In *RSS*, 2024.

[^52]: T. Ma, J. Zheng, Z. Wang, C. Jiang, A. Cui, J. Liang, and S. Yang. Dit4dit: Jointly modeling video dynamics and actions for generalizable robot control. *arXiv preprint arXiv:2603.10448*, 2026.

[^53]: J. Lim, T. Ha, M. Choi, J. Kim, B. Kim, S. Jeon, and H. Joo. Hrdexdb: A large-scale dataset of dexterous human and robotic hand grasps. *arXiv preprint arXiv:2604.14944*, 2026.

[^54]: Y.-W. Chao, W. Yang, Y. Xiang, P. Molchanov, A. Handa, J. Tremblay, Y. S. Narang, K. Van Wyk, U. Iqbal, S. Birchfield, J. Kautz, and D. Fox. DexYCB: A benchmark for capturing hand grasping of objects. In *CVPR*, 2021.

[^55]: M. Mittal, P. Roth, J. Tigue, A. Richard, O. Zhang, P. Du, A. Serrano-Muñoz, X. Yao, R. Zürbrügg, N. Rudin, et al. Isaac lab: A gpu-accelerated simulation framework for multi-modal robot learning. *arXiv preprint arXiv:2511.04831*, 2025.

[^56]: A. Blattmann, T. Dockhorn, S. Kulal, D. Mendelevitch, M. Kilian, D. Lorenz, Y. Levi, Z. English, V. Voleti, A. Letts, et al. Stable video diffusion: Scaling latent video diffusion models to large datasets. *arXiv preprint arXiv:2311.15127*, 2023.

[^57]: W. Peebles and S. Xie. Scalable diffusion models with transformers. In *ICCV*, 2023.

[^58]: R. Zhang, P. Isola, A. A. Efros, E. Shechtman, and O. Wang. The unreasonable effectiveness of deep features as a perceptual metric. In *CVPR*, 2018.

[^59]: B. Sundaralingam, S. K. S. Hari, A. Fishman, C. Garrett, K. Van Wyk, V. Blukis, A. Millane, H. Oleynikova, A. Handa, F. Ramos, N. Ratliff, and D. Fox. curobo: Parallelized collision-free minimum-jerk robot motion generation. *arXiv preprint arXiv:2310.17274*, 2023.

[^60]: Y. Qin, W. Yang, B. Huang, K. Van Wyk, H. Su, X. Wang, Y.-W. Chao, and D. Fox. Anyteleop: A general vision-based dexterous robot arm-hand teleoperation system. In *RSS*, 2023.

[^61]: C. M. Kim, B. Yi, H. Choi, Y. Ma, K. Goldberg, and A. Kanazawa. Pyroki: A modular toolkit for robot kinematic optimization. In *IROS*, 2025.

[^62]: NVIDIA, J. Bjorck, F. Castañeda, N. Cherniadev, X. Da, R. Ding, L. Fan, Y. Fang, D. Fox, F. Hu, S. Huang, J. Jang, Z. Jiang, J. Kautz, K. Kundalia, L. Lao, Z. Li, Z. Lin, K. Lin, G. Liu, E. Llontop, L. Magne, A. Mandlekar, A. Narayan, S. Nasiriany, S. Reed, Y. L. Tan, G. Wang, Z. Wang, J. Wang, Q. Wang, J. Xiang, Y. Xie, Y. Xu, Z. Xu, S. Ye, Z. Yu, A. Zhang, H. Zhang, Y. Zhao, R. Zheng, and Y. Zhu. Gr00t n1: An open foundation model for generalist humanoid robots. *arXiv preprint arXiv:2503.14734*, 2025.

[^63]: B. Wen, M. Trepte, J. Aribido, J. Kautz, O. Gallo, and S. Birchfield. Foundationstereo: Zero-shot stereo matching. In *CVPR*, 2025.

[^64]: S. Bai, Y. Cai, R. Chen, K. Chen, X. Chen, et al. Qwen3-vl technical report. *arXiv preprint arXiv:2511.21631*, 2025.

[^65]: E. J. Hu, Y. Shen, P. Wallis, Z. Allen-Zhu, Y. Li, S. Wang, L. Wang, and W. Chen. LoRA: Low-rank adaptation of large language models. In *ICLR*, 2022.

[^66]: I. Loshchilov and F. Hutter. Decoupled weight decay regularization. In *ICLR*, 2019.

[^67]: L. Contributors. Lightx2v: Light video generation inference framework, 2025. URL [https://github.com/ModelTC/lightx2v](https://github.com/ModelTC/lightx2v).

[^68]: B. Zi, W. Peng, X. Qi, J. Wang, S. Zhao, R. Xiao, and K.-F. Wong. Minimax-remover: Taming bad noise helps video object removal. *Advances in Neural Information Processing Systems*, 38:75518–75547, 2026.

[^69]: Z. Jiang, Y. Xie, K. Lin, Z. Xu, W. Wan, A. Mandlekar, L. J. Fan, and Y. Zhu. DexMimicGen: Automated data generation for bimanual dexterous manipulation via imitation learning. In *ICRA*, pages 16923–16930, 2025.
