---
title: "AXIS：一个可增长的社区驱动数据引擎，用于可扩展的机器人操作"
sourceTitle: "AXIS: A Growable Community-Driven Data Engine for Scalable Robot Manipulation"
sourceUrl: "https://arxiv.org/abs/2607.21588"
sourcePublishDate: "2026-07-23"
translatedDate: "2026-08-03"
pipelineRunId: "batch-20260803"
pipelineSource: "translate/batch-20260803/works-ready/axis-growable-community-data-engine-translation.md"
sourceFigureCount: 3
language: "zh-CN"
---

# AXIS：一个可增长的社区驱动数据引擎，用于可扩展的机器人操作

Mengfei Zhao <sup>∗,‡</sup>, Dihong Huang <sup>1,2∗</sup>, Yikai Tang <sup>2∗</sup>, Peihao Li <sup>2∗</sup>, Mingxuan Yan <sup>3∗</sup>, Ruiqi Zhuang <sup>1∗</sup>, Yanjia Huang <sup>4∗</sup>, Jie Wang <sup>1,5</sup>, Hai Zhai <sup>1</sup>, Tony Zhou <sup>1,6</sup>, Rui Zhang <sup>1,7</sup>, Zhexi Luo <sup>1,8</sup>, Yuchen Huang <sup>1,7</sup>, Jianfei Yang <sup>9‡</sup>, Jiachen Li <sup>3‡</sup>

<sup>1</sup> Axis Robotics   <sup>2</sup> 加州大学伯克利分校   <sup>3</sup> 佐治亚理工学院  
<sup>4</sup> 德克萨斯农工大学   <sup>5</sup> 约翰斯·霍普金斯大学   <sup>6</sup> 宾夕法尼亚大学  
<sup>7</sup> 密歇根大学   <sup>8</sup> 新加坡国立大学   <sup>9</sup> 南洋理工大学  
<sup>∗</sup> 同等贡献   <sup>‡</sup> 联系邮箱：zhaomengfei248@gmail.com, jianfei.yang@ntu.edu.sg, jiachen_li@gatech.edu

## 摘要

学习有效的机器人操作策略需要多样化、高质量的演示数据，但现有数据流水线往往难以扩展，因为它们依赖专用硬件、中心化操作者或固定的任务集。我们提出 AXIS，一个可增长的社区驱动数据引擎和用于可扩展机器人学习的基准，它通过基于浏览器的远程操作实现大规模演示收集，自动生成和验证新的操作任务，并通过自动化成功检查、质量过滤、轨迹平滑以及基于视觉和物理的增强，将社区收集的演示转换为可训练数据。AXIS 数据集目前包含 207 个多样化任务和超过 50,000 条轨迹。同时，AXIS 将数据组织成任务快照，并使用系统化的留出协议评估策略。我们在统一的 AXIS 评估套件下比较视觉-语言-动作（VLA）策略，并分析不同数据量下的扩展行为。在 AXIS 上持续预训练显著将 $\pi_{0.5}$ 的总体成功率提升了 5.8%，优于在 RoboCasa365 上预训练的模型 37.3%，并且随着数据量增加表现出一致的扩展性，在布局、传感器噪声和摄像头扰动下观察到最大增益。

项目网站：https://axisaiorg.github.io/AXIS-V1/

> 关键词：可扩展机器人学习、操作数据集、数据收集

# AXIS：一个可增长的社区驱动数据引擎，用于可扩展的机器人操作

Mengfei Zhao 等 15 位作者
Axis Robotics、加州大学伯克利分校、佐治亚理工学院等机构

## 摘要

学习有效的机器人操作策略需要多样化、高质量的演示数据，但现有数据流水线往往难以扩展，因为它们依赖专用硬件、中心化操作者或固定的任务集。我们提出 AXIS，一个可增长的社区驱动数据引擎和用于可扩展机器人学习的基准，它通过基于浏览器的远程操作实现大规模演示收集，自动生成和验证新的操作任务，并通过自动化成功检查、质量过滤、轨迹平滑以及基于视觉和物理的增强，将社区收集的演示转换为可训练数据。AXIS 数据集目前包含 207 个多样化任务和超过 50,000 条轨迹。同时，AXIS 将数据组织成任务快照，并使用系统化的留出协议评估策略。我们在统一的 AXIS 评估套件下比较视觉-语言-动作（VLA）策略，并分析不同数据量下的扩展行为。在 AXIS 上持续预训练显著将 π₀.₅ 的总体成功率提升了 5.8%，优于在 RoboCasa365 上预训练的模型 37.3%，并且随着数据量增加表现出一致的扩展性，在布局、传感器噪声和摄像头扰动下观察到最大增益。

项目网站：https://axisaiorg.github.io/AXIS-V1/

关键词：可扩展机器人学习、操作数据集、数据收集

## 1 引言

通用机器人操作需要能够跨越多样化物体、场景和任务目标执行的策略。模仿学习和视觉-语言-动作（VLA）模型的最新进展表明，策略性能在很大程度上取决于机器人演示数据的规模、多样性和质量。然而，收集物理机器人演示仍然成本高昂，远程操作系统通常需要专用硬件或本地仿真器安装，并且大多数公共操作数据集在一次性收集工作后保持固定。因此，机器人数据集的增长速度远远慢于它们旨在训练的模型。

现有操作数据流水线的一个根本限制是数据收集在很大程度上保持封闭和中心化。专家操作者或专门团队在本地硬件上收集演示，离线处理它们，并将生成的数据集作为固定基准发布。虽然这种范式提供了对数据质量的强大控制，但它不能自然地支持持续的任务扩展、广泛的社区参与，或随着机器人能力演进的快速迭代。扩展机器人学习反而需要能够沿着多个维度持续扩展的数据基础设施，包括任务、物体、场景、贡献者、视觉条件和物理变化。

我们认为，下一代机器人操作数据集应该是**可增长的**：它们不应作为静态的演示集合，而应提供持续生成、收集、验证、处理和评估新数据的机制。这种增长还应该是结构化的，将新任务和演示组织成可复现的数据集快照，以支持随时间推移的一致性基准测试。为此，我们引入 AXIS，一个用于可扩展机器人操作的社区驱动数据引擎和基准。AXIS 建立在一个简单的原则之上：使大规模数据收集广泛可及，同时按需扩展高质量的数据处理。该平台目前针对配备平行夹爪的 Franka Research 3 机器人的桌面操作，支持广泛的任务，包括拾取与放置、堆叠、推动、倾倒、铰接物体操作和工具使用行为。每个任务由自然语言指令、参数化场景配置和结构化成功检查器指定。

AXIS 包含三个紧密集成的层次。**基础设施层**结合了自动化任务生成和基于浏览器的 MuJoCo-WASM 远程操作。新任务从高级指令、物体资产、场景布局和任务特定的成功条件生成，然后通过共享的 Web 界面部署，用户使用商品输入设备收集演示。**数据集层**通过统一的轨迹表示、自动化成功验证、质量过滤、静态片段移除、轨迹平滑、重采样以及基于 IsaacSim 的视觉和物理增强，将原始社区演示转换为可训练数据。最后，**模型层**使用共享的任务定义和成功检查器，支持传统视觉运动模仿学习策略和现代 VLA 模型的训练和评估。AXIS 还旨在使数据集增长可衡量。它将演示组织成逐步增大的任务快照，并使用固定的留出协议评估策略，这使得可控的扩展研究成为可能，在这些研究中，策略架构、训练预算、推演协议、评估任务和成功标准保持固定，而只有训练快照在增长。

**贡献。** 首先，我们提出 AXIS，一个基于 Web 的社区驱动基础设施，通过基于浏览器的 MuJoCo-WASM 远程操作和自动化任务生成实现可扩展的操作数据收集。其次，我们构建了一个可增长的操作数据集，包括 207 个任务、50K+ 条轨迹，以及一个统一的数据处理流水线，用于轨迹标准化、成功验证、质量过滤、时间平滑、重采样、基于 IsaacSim 的视觉和物理增强，以及版本化的任务快照。第三，我们引入了一个系统化的评估协议，用于研究策略学习和本体内数据集扩展，包括标准化的基准比较和使用 AXIS-25%/50%/100% 数据集快照在固定留出评估套件下的可控扩展实验。

**关键发现。** 我们通过两个互补的实验协议评估 AXIS。首先，我们在统一的评估套件下比较代表性的模仿学习和 VLA 策略，使用我们的数据集作为一致的基准。其次，我们通过在逐步增大的数据集快照上训练固定的策略配置来研究数据集扩展，同时保持优化预算和留出评估协议不变。结果表明，大规模的社区收集演示与自动化处理和增强相结合，显著改善了下游策略学习。在 AXIS-100% 上对 $\pi_{0.5}$ 进行持续预训练将 LIBERO-Plus 的总体成功率提高了 5.8%，并优于体量匹配的 RoboCasa365 持续预训练基线 37.3%。性能随数据集大小一致扩展（AXIS-25%/50%/100% 分别为 84.7%/85.7%/88.8%），在摄像头（+15.6%）、传感器噪声（+16.6%）、布局（+3.1%）和机器人姿态（+5.1%）扰动下观察到最大改进，表明 AXIS 的可增长设计为可扩展机器人操作提供了一条实用路径。

## 2 相关工作

**大规模和众包机器人操作数据。** 大规模机器人学习越来越依赖于涵盖多机器人迁移、跨环境轨迹、野外演示、多本体数据和机构聚合的广泛操作语料库 [^8] [^10] [^52] [^23] [^57] [^42]。这些数据集支持了可扩展的机器人策略 [^2] [^66]，而最近的基准将覆盖范围扩展到终身学习、家庭任务、组合推理、记忆、抓取和部件级监督 [^31] [^38] [^39] [^4] [^7] [^48] [^28] [^61]。众包系统扩大了收集范围 [^36] [^37]，但许多资源仍然是固定发布或依赖于物理机器人和特定设置。AXIS 转而使用基于浏览器的仿真远程操作进行社区收集，并将数据组织成版本化快照，使数据集增长可衡量，而不是一次性发布。

**机器人数据收集的远程操作接口。** 机器人演示通常通过远程操作收集，范围从动觉教学 [^27] 和主从装置 [^58] [^63] [^67] 到商品设备 [^38] [^20] [^64] [^17] [^33] [^54] 和可穿戴捕捉 [^53] [^60]。这些接口在精度、成本、具身保真度、校准负担和可访问性之间进行权衡。Web 系统降低了入门门槛，但先前的工作通常针对远程物理机器人 [^37] [^13]，使收集受到机器人可用性、维护和安全的限制；而高保真仿真器需要更重的安装和 GPU 资源 [^41] [^59]。AXIS 通过使用 MuJoCo-WASM 进行基于浏览器的远程操作，并将高保真重放和增强保留给后端处理，将收集与昂贵的仿真分离。

**仿真、任务生成和合成增强。** 仿真支持可扩展的任务变化、重放和验证，涵盖用于交互和原型设计的轻量级引擎 [^51] [^26] [^46] [^50] [^6] 以及用于并行仿真、渲染和复杂场景的面向 GPU 的平台 [^34] [^62] [^14] [^15] [^41] [^59]。标准化任务套件定义了通用接口和成功标准 [^18] [^49] [^65] [^39] [^16]；数据生成方法通过轨迹重定向和合成扩展演示 [^35] [^22]；LLM/VLM 驱动的系统扩展任务和资产生成 [^55] [^21] [^56] [^19]。AXIS 通过自动化任务生成、基于浏览器的远程操作和基于 IsaacSim 的增强，将这些组件统一到一个可增长的数据流水线中。

**VLA 模型和评估。** 数据扩展与训练和评估通用操作策略密切相关。扩散式策略和动作分块变换器仍然是强大的基线，ACT 通常用于分块动作预测 [^63]。VLA 模型在机器人轨迹上微调大型视觉-语言主干，并推进了指令跟随操作 [^43] [^25] [^24] [^32] [^29] [^47] [^1] [^44] [^40] [^3]。评估同样转向共享接口和鲁棒性探测，包括终身任务、扰动感知扩展、仿真到现实相关评估和组合场景偏移 [^31] [^12] [^30] [^45]。AXIS 不是冻结单一套件，而是发布版本化的训练快照和固定的留出协议，以便随着数据增长，可以在共享数据、观测和成功检查器约定下比较策略。

## 3 可扩展机器人数据收集

数据集层通过验证、过滤、精炼和增强，将原始社区演示转换为可训练的机器人轨迹。由于演示是从人类操作者而不是脚本控制器收集的，生成的数据包含接近方向、抓取时机、纠正行为和执行风格的行为多样性。

完成的演示被序列化为统一的轨迹格式。每条轨迹包含任务元数据、机器人本体、环境标识符、仿真器版本、时间戳、观测、机器人状态、动作和成功信息。可选字段包括物体状态、摄像头流、分割标记以及可用时的失败标签。这种格式使演示可重放、可过滤，并与下游策略学习兼容。

原始社区演示可能包含抖动、停顿、损坏帧、不连续性或不正确的成功标签。因此，数据集使用一致性检查进行过滤，检查缺失键、异常数值、无效状态转换、物理上不合理的帧间变化以及任务完成失败。尽管前端转储包含成功元数据，但 AXIS 在后端验证期间重用任务特定的成功检查器，而不是仅依赖前端成功标志。保留的轨迹然后通过移除静态或损坏片段、平滑高频远程操作伪影以及重采样到固定控制频率来精炼。这产生了更稳定的动作序列，同时保留了演示操作行为的几何结构。清理后的演示在 IsaacSim 后端重放以进行视觉和物理增强。物理随机化改变物体姿态、杂乱配置、质量、摩擦和相关动力学参数。渲染随机化改变摄像头视点、光照、纹理和视觉外观。这些扰动在保留任务语义的同时应用，扩展训练分布而无需为每个变化提供额外的人类演示。结果是一个可扩展的流水线，将可访问的 Web 收集演示转换为 VLA 和机器人策略学习的更高质量数据。

## 4 AXIS Franka 数据集

![参考说明](../../../works/imgs/axis-growable-community-data-engine/img-001-x2.png)

图 2：三个示例任务的增强多样性可视化。对于每个任务（行），我们展示通过随机化外观、光照、环境和初始物体位置获得的三个增强变体（列）。在增强过程中，我们改变材料和纹理（例如，桌子、地板、物体）、光照条件（例如，亮度和阴影）、背景和场景布局（例如，墙壁、家具、装饰）以及初始状态（例如，物体位置和方向）。这个过程大大增加了数据多样性，并提高了策略对环境变化的鲁棒性。

![参考说明](../../../works/imgs/axis-growable-community-data-engine/img-002-x3.png)

图 3：AXIS 数据集概览。

**数据集概览。** AXIS 是一个围绕配备平行夹爪的 Franka Research 3 机器人构建的大规模桌面操作数据集。如图 3 所总结的，当前数据集快照包含 207 个操作任务、超过 50K 条人类演示轨迹以及超过 60K 个任务或场景变体。每个任务由语言指令、参数化仿真场景、任务资产和结构化成功检查器指定。每条演示轨迹包含同步的多模态信息，包括来自两个摄像头视图的 RGB-D 渲染、机器人状态、物体状态、机器人动作、任务元数据和成功标签。这种统一的数据模式使相同的轨迹可用于模仿学习、基于重放的验证、视觉增强和基准评估。任务套件涵盖广泛的常见桌面操作行为，包括拾取与放置、重定位、堆叠、分类、推动、倾倒、插入、对齐以及与铰接或容器类物体的交互，当前任务分布跨越七个场景类别。

**验证和精炼。** 为确保数据质量，所有演示都经过标准化验证流水线，验证轨迹完整性、任务成功和物理一致性。有效的轨迹随后被平滑并重采样到统一的控制频率，提高时间一致性并减少远程操作噪声。如表 1 所示，精炼过程大幅降低了加速度和急动度，同时保持较高的重放成功率。附录提供了额外的实现细节。

**数据增强。** 为了提高多样性和鲁棒性，AXIS 通过空间和视觉随机化纳入增强。这些增强在保留任务语义和成功条件的同时生成额外的有效场景配置。如图 2 所示，生成的变体涵盖物体布局、视点、纹理、光照和背景的变化，将数据集扩展到超过 60K 个场景变体。这种多样性使 AXIS 特别适用于研究环境变化下的鲁棒性和泛化。

**基准平台。** AXIS 被设计为一个可增长的基准，用于研究随着数据增长的策略学习。训练数据被组织成逐步增大的任务快照，包括 AXIS-25%、AXIS-50% 和 AXIS-100%。每个快照保持相同的数据格式、验证流水线、任务定义、推演预算和成功检查器，支持可控的扩展研究，其中仅训练数据的数量和多样性发生变化。数据集还通过社区驱动的任务和轨迹收集持续可扩展。在图 3 所示的活跃收集窗口中，每日验证尝试数在峰值时达到约 15K，活跃窗口累计尝试数接近 100K。这些统计数据表明 AXIS 旨在支持增量数据集增长，而不是一次性静态发布。

该基准支持两种互补的评估模式。首先，AXIS 支持跨代表性策略家族的直接比较，包括传统视觉运动模仿学习方法和视觉-语言-动作策略。其次，AXIS 支持本体内扩展研究，在 AXIS-25%、AXIS-50%、AXIS-100% 和未来更大的快照上训练策略，以衡量额外的任务覆盖是否改善留出操作泛化。这些特性共同使 AXIS 不仅成为大规模操作数据集，而且成为研究可扩展机器人学习和数据驱动泛化的长期基准。

## 5 实验

表 1：数据精炼对远程操作轨迹质量的影响。相对于原始远程操作，最终平滑并重采样的轨迹将平均加速度和平均急动度分别降低了 63.9% 和 80.8%。

| 数据版本 | 采样率 | 平均加速度 | 平均急动度 | 重放成功率 |
| --- | --- | --- | --- | --- |
| 原始远程操作 | 5.0 Hz | 1.3539 | 11.5899 | 100.0% |
| 平滑后 | 5.0 Hz | 0.6382 | 2.9160 | 91.4% |
| 平滑+重采样 | 20 Hz | 0.4885 | 2.2243 | 86.2% |

为了评估 AXIS 作为可增长数据引擎的效果，我们围绕三个问题组织实验。(i) 在 AXIS 上对 $\pi_{0.5}$ [^44] 进行持续预训练是否改善了下游 LIBERO-Plus [^12] 性能，这种改善是 AXIS 特有的还是与从 RoboCasa365 [^39] 抽取的等体量基线相比是通用的？(ii) 改善是否随 AXIS 体量扩展，当下游 LIBERO 微调数据稀缺时改善是否最大？(iii) AXIS 持续预训练在哪些 LIBERO-Plus 扰动维度上帮助最大，这种模式是否与 AXIS IsaacSim 增强流水线显式随机化的扰动相匹配？
[^10]: F. Ebert, Y. Yang, K. Schmeckpeper, B. Bucher, G. Georgakis, K. Daniilidis, C. Finn, and S. Levine (2021) Bridge data: boosting generalization of robotic skills with cross-domain datasets. arXiv preprint arXiv:2109.13396. Cited by: §2.

[^11]: J. Eßer, N. Bach, C. Jestel, O. Urbann, and S. Kerner (2022) Guided reinforcement learning: a review and evaluation for efficient and effective real-world robotics \[survey\]. IEEE Robotics &amp; Automation Magazine 30 (2), pp. 67–85. Cited by: §1.

[^12]: S. Fei, S. Wang, J. Shi, Z. Dai, J. Cai, P. Qian, L. Ji, X. He, S. Zhang, Z. Fei, J. Fu, J. Gong, and X. Qiu (2025) LIBERO-Plus: in-depth robustness analysis of vision-language-action models. arXiv preprint arXiv:2510.13626. External Links: 2510.13626 Cited by: §2, §5.2, §5.

[^13]: C. Fok, F. Sun, M. Mangum, A. K. Mok, B. He, and L. Sentis (2016) Web-based teleoperation of a humanoid robot. arXiv preprint arXiv:1607.05402. External Links: 1607.05402 Cited by: §2.

[^14]: C. D. Freeman, E. Frey, A. Raichuk, S. Girgin, I. Mordatch, and O. Bachem (2021) Brax: a differentiable physics engine for large scale rigid body simulation. In Proceedings of the Neural Information Processing Systems Track on Datasets and Benchmarks, Note: NeurIPS 2021 Datasets and Benchmarks Track Cited by: §2.

[^15]: Genesis Authors (2024) Genesis: a universal and generative physics engine for robotics and beyond. Note: Software project, accessed 2026-03-06 Cited by: §2.

[^16]: H. Geng, F. Wang, S. Wei, Y. Li, B. Wang, B. An, C. T. Cheng, H. Lou, P. Li, Y. Wang, et al. (2025) Roboverse: towards a unified platform, dataset and benchmark for scalable and generalizable robot learning. In Robotics: Science and Systems (RSS), Cited by: §1, §2.

[^17]: A. George, A. Bartsch, and A. B. Farimani (2025) OpenVR: teleoperation for manipulation. SoftwareX 29, pp. 102054. Cited by: §2.

[^18]: J. Gu, F. Xiang, X. Li, Z. Ling, X. Liu, T. Mu, Y. Tang, S. Tao, X. Wei, Y. Yao, X. Yuan, P. Xie, Z. Huang, R. Chen, and H. Su (2023) ManiSkill2: a unified benchmark for generalizable manipulation skills. In International Conference on Learning Representations (ICLR), External Links: 2302.04659 Cited by: §2.

[^19]: Y. He, X. Wang, P. Li, Y. Huang, J. Masterjohn, J. Wu, L. Guibas, Y. Yang, Y. Jiang, and C. Jiang (2026) Fishbone: from one 3d asset to a million controllable edits. arXiv preprint arXiv:2605.24805. Cited by: §2.

[^20]: D. Honerkamp, H. Mahesheka, J. O. von Hartz, T. Welschehold, and A. Valada (2025) Zero-cost whole-body teleoperation for mobile manipulation. IEEE Robotics and Automation Letters. Cited by: §2.

[^21]: P. Hua, M. Liu, A. Macaluso, Y. Lin, W. Zhang, H. Xu, and L. Wang (2024) GenSim2: scaling robot data generation with multi-modal and reasoning LLMs. In Conference on Robot Learning (CoRL), External Links: 2410.03645 Cited by: §2.

[^22]: Z. Jiang, Y. Xie, K. Lin, Z. Xu, W. Wan, A. Mandlekar, L. Fan, and Y. Zhu (2024) DexMimicGen: automated data generation for bimanual dexterous manipulation via imitation learning. arXiv preprint arXiv:2410.24185. External Links: 2410.24185 Cited by: §2.

[^23]: A. Khazatsky, K. Pertsch, S. Nair, A. Balakrishna, S. Dasari, S. Karamcheti, S. Nasiriany, M. K. Srirama, L. Y. Chen, K. Ellis, et al. (2024) DROID: a large-scale in-the-wild robot manipulation dataset. In Robotics: Science and Systems, Cited by: Table 4, §2.

[^24]: M. J. Kim, C. Finn, and P. Liang (2025) Fine-tuning vision-language-action models: optimizing speed and success. arXiv preprint arXiv:2502.19645. External Links: 2502.19645 Cited by: §2.

[^25]: M. J. Kim, K. Pertsch, S. Karamcheti, T. Xiao, A. Balakrishna, S. Nair, R. Rafailov, E. Foster, G. Lam, P. Sanketi, Q. Vuong, T. Kollar, B. Burchfiel, R. Tedrake, D. Sadigh, S. Levine, P. Liang, and C. Finn (2024) OpenVLA: an open-source vision-language-action model. In Conference on Robot Learning (CoRL), External Links: 2406.09246 Cited by: §2.

[^26]: N. Koenig and A. Howard (2004-09) Design and use paradigms for gazebo, an open-source multi-robot simulator. In IEEE/RSJ International Conference on Intelligent Robots and Systems, Sendai, Japan, pp. 2149–2154. Cited by: §2.

[^27]: P. Kormushev, S. Calinon, and D. G. Caldwell (2011) Imitation learning of positional and force skills demonstrated via kinesthetic teaching and haptic input. Advanced Robotics 25 (5), pp. 581–603. Cited by: §2.

[^28]: P. Li, H. Geng, J. Crate, Y. Han, J. Zhang, F. Wang, C. T. Cheng, R. Dong, Y. Wang, H. Lou, et al. (2025) ROSE: reconstructing objects, scenes, and trajectories from casual videos for robotic manipulation. In NeurIPS 2025 Workshop on Bridging Language, Agent, and World Models for Reasoning and Planning, Cited by: §2.

[^29]: Q. Li, Y. Liang, Z. Wang, L. Luo, X. Chen, M. Liao, F. Wei, Y. Deng, S. Xu, Y. Zhang, X. Wang, B. Liu, J. Fu, J. Bao, D. Chen, Y. Shi, J. Yang, and B. Guo (2024) CogACT: a foundational vision-language-action model for synergizing cognition and action in robotic manipulation. arXiv preprint arXiv:2411.19650. External Links: 2411.19650 Cited by: §2.

[^30]: X. Li, K. Hsu, J. Gu, K. Pertsch, O. Mees, H. R. Walke, C. Fu, I. Lunawat, I. Sieh, S. Kirmani, S. Levine, J. Wu, C. Finn, H. Su, Q. Vuong, and T. Xiao (2024) Evaluating real-world robot manipulation policies in simulation. arXiv preprint arXiv:2405.05941. External Links: 2405.05941 Cited by: §2.

[^31]: B. Liu, Y. Zhu, C. Gao, Y. Feng, Q. Liu, Y. Zhu, and P. Stone (2023) LIBERO: benchmarking knowledge transfer for lifelong robot learning. In Advances in Neural Information Processing Systems, A. Oh, T. Naumann, A. Globerson, K. Saenko, M. Hardt, and S. Levine (Eds.), Vol. 36, pp. 44776–44791. Cited by: Table 4, §2, §2.

[^32]: S. Liu, L. Wu, B. Li, H. Tan, H. Chen, Z. Wang, K. Xu, H. Su, and J. Zhu (2024) RDT-1B: a diffusion foundation model for bimanual manipulation. arXiv preprint arXiv:2410.07864. External Links: 2410.07864 Cited by: §2.

[^33]: K. Lu, Y. He, C. Lu, and P. Li (2025) I know kung fu: synthetic dexterous hand demonstration collection via vr teleoperation. In NeurIPS 2025 Workshop on Space in Vision, Language, and Embodied AI, Cited by: §2.

[^34]: V. Makoviychuk, L. Wawrzyniak, Y. Guo, M. Lu, K. Storey, M. Macklin, D. Hoeller, N. Rudin, A. Allshire, A. Handa, and G. State (2021) Isaac gym: high performance gpu-based physics simulation for robot learning. In Proceedings of the Neural Information Processing Systems Track on Datasets and Benchmarks, Note: NeurIPS 2021 Datasets and Benchmarks Track Cited by: §2.

[^35]: A. Mandlekar, S. Nasiriany, B. Wen, I. Akinola, Y. Narang, L. Fan, Y. Zhu, and D. Fox (2023) MimicGen: a data generation system for scalable robot learning using human demonstrations. In Conference on Robot Learning (CoRL), External Links: 2310.17596 Cited by: §2.

[^36]: A. Mandlekar, Y. Zhu, A. Garg, J. Booher, M. Spero, A. Tung, J. Gao, J. Emmons, A. Gupta, E. Orbay, S. Savarese, and L. Fei-Fei (2018) RoboTurk: a crowdsourcing platform for robotic skill learning through imitation. In Conference on Robot Learning (CoRL), External Links: 1811.02790 Cited by: §2.

[^37]: S. Mirchandani, M. Tang, J. Duan, J. I. Hamid, M. Cho, and D. Sadigh (2025) RoboCade: gamifying robot data collection. arXiv preprint arXiv:2512.21235. External Links: 2512.21235 Cited by: §2, §2.

[^38]: S. Nasiriany, A. Maddukuri, L. Zhang, A. Parikh, A. Lo, A. Joshi, A. Mandlekar, and Y. Zhu (2024) RoboCasa: large-scale simulation of everyday tasks for generalist robots. In Robotics: Science and Systems (RSS), Cited by: §2, §2.

[^39]: S. Nasiriany, S. Nasiriany, A. Maddukuri, and Y. Zhu (2026) RoboCasa365: a large-scale simulation framework for training and benchmarking generalist robots. In International Conference on Learning Representations (ICLR), Cited by: §2, §2, §5.2, §5.

[^40]: NVIDIA, J. Bjorck, F. Castañeda, N. Cherniadev, X. Da, R. Ding, L. Fan, Y. Fang, D. Fox, F. Hu, S. Huang, J. Jang, Z. Jiang, J. Kautz, K. Kundalia, L. Lao, Z. Li, Z. Lin, K. Lin, G. Liu, E. Llontop, L. Magne, A. Mandlekar, A. Narayan, S. Nasiriany, S. Reed, Y. L. Tan, G. Wang, Z. Wang, J. Wang, Q. Wang, J. Xiang, Y. Xie, Y. Xu, Z. Xu, S. Ye, Z. Yu, A. Zhang, H. Zhang, Y. Zhang, Y. Zhang, L. Fan, and Y. Zhu (2025) GR00T N1: an open foundation model for generalist humanoid robots. arXiv preprint arXiv:2503.14734. External Links: 2503.14734 Cited by: §2.

[^41]: NVIDIA (2025) Isaac sim. Note: Robotics simulator, accessed 2026-03-06 Cited by: §2, §2.

[^42]: A. O'Neill, A. Rehman, A. Maddukuri, A. Gupta, A. Padalkar, et al. (2024) Open X-Embodiment: robotic learning datasets and RT-X models. In 2024 IEEE International Conference on Robotics and Automation (ICRA), Vol., pp. 6892–6903. Cited by: Table 4, §2.

[^43]: Octo Model Team, D. Ghosh, H. Walke, K. Pertsch, K. Black, O. Mees, S. Dasari, J. Hejna, T. Kreiman, C. Xu, J. Luo, Y. L. Tan, P. Sanketi, Q. Vuong, T. Xiao, D. Sadigh, C. Finn, and S. Levine (2024) Octo: an open-source generalist robot policy. In Robotics: Science and Systems (RSS), External Links: 2405.12213 Cited by: §2.

[^44]: Physical Intelligence, K. Black, N. Brown, J. Darpinian, K. Dhabalia, D. Driess, A. Esmail, M. Equi, C. Finn, N. Fusai, M. Y. Galliker, D. Ghosh, L. Groom, K. Hausman, B. Ichter, S. Jakubczak, T. Jones, L. Ke, D. LeBlanc, S. Levine, A. Li-Bell, M. Mothukuri, S. Nair, K. Pertsch, A. Z. Ren, L. X. Shi, L. Smith, J. T. Springenberg, K. Stachowicz, J. Tanner, Q. Vuong, H. Walke, A. Walling, H. Wang, L. Yu, and U. Zhilinsky (2025) $\pi_{0.5}$: a vision-language-action model with open-world generalization. arXiv preprint arXiv:2504.16054. External Links: 2504.16054 Cited by: §2, §5.2, §5.

[^45]: W. Pumacay, I. Singh, J. Duan, R. Krishna, J. Thomason, and D. Fox (2024) THE COLOSSEUM: a benchmark for evaluating generalization for robotic manipulation. In Robotics: Science and Systems (RSS), External Links: 2402.08191 Cited by: §2.

[^46]: E. Rohmer, S. P. N. Singh, and M. Freese (2013) CoppeliaSim (formerly v-rep): a versatile and scalable robot simulation framework. In 2013 IEEE/RSJ International Conference on Intelligent Robots and Systems, Cited by: §2.

[^47]: H. Shi, B. Xie, Y. Liu, L. Sun, F. Liu, T. Wang, E. Zhou, H. Fan, X. Zhang, and G. Huang (2025) Memoryvla: perceptual-cognitive memory in vision-language-action models for robotic manipulation. arXiv preprint arXiv:2508.19236. Cited by: §2.

[^48]: S. K. Srinivas, Y. Shukla, A. Arnold, and S. Chitta (2025) GraspFactory: a large object-centric grasping dataset. arXiv preprint arXiv:2509.20550. Cited by: §2.

[^49]: S. Tao, F. Xiang, A. Shukla, Y. Qin, X. Hinrichsen, X. Yuan, C. Bao, X. Lin, Y. Liu, T. Chan, Y. Gao, X. Li, T. Mu, N. Xiao, A. Gurha, Z. Huang, R. Calandra, R. Chen, S. Luo, and H. Su (2024) ManiSkill3: gpu parallelized robotics simulation and rendering for generalizable embodied AI. arXiv preprint arXiv:2410.00425. External Links: 2410.00425 Cited by: §2.

[^50]: R. Tedrake and the Drake Development Team (2019) Drake: model-based design and verification for robotics. Cited by: §2.

[^51]: E. Todorov, T. Erez, and Y. Tassa (2012) MuJoCo: a physics engine for model-based control. In 2012 IEEE/RSJ International Conference on Intelligent Robots and Systems, pp. 5026–5033. Cited by: §2.

[^52]: H. R. Walke, K. Black, T. Z. Zhao, Q. Vuong, C. Zheng, P. Hansen-Estruch, et al. (2023-06–09 Nov) BridgeData v2: a dataset for robot learning at scale. In Proceedings of The 7th Conference on Robot Learning, J. Tan, M. Toussaint, and K. Darvish (Eds.), Proceedings of Machine Learning Research, Vol. 229, pp. 1723–1736. Cited by: Table 4, §2.

[^53]: C. Wang, H. Shi, W. Wang, R. Zhang, L. Fei-Fei, and C. K. Liu (2024) DexCap: scalable and portable mocap data collection system for dexterous manipulation. arXiv preprint arXiv:2403.07788. External Links: 2403.07788 Cited by: §2.

[^54]: J. Wang, C. Chang, J. Duan, D. Fox, and R. Krishna (2024) EVE: enabling anyone to train robot using augmented reality. arXiv preprint arXiv:2404.06089. External Links: 2404.06089 Cited by: §2.

[^55]: L. Wang, Y. Ling, Z. Yuan, M. Shridhar, C. Bao, Y. Qin, B. Wang, H. Xu, and X. Wang (2024) GenSim: generating robotic simulation tasks via large language models. In International Conference on Learning Representations (ICLR), External Links: 2310.01361 Cited by: §2.

[^56]: Y. Wang, Z. Xian, F. Chen, T. Wang, Y. Wang, K. Fragkiadaki, Z. Erickson, D. Held, and C. Gan (2024) RoboGen: towards unleashing infinite data for automated robot learning via generative simulation. In International Conference on Machine Learning (ICML), External Links: 2311.01455 Cited by: §2.

[^57]: K. Wu, C. Hou, J. Liu, Z. Che, X. Ju, Z. Yang, M. Li, Y. Zhao, Z. Xu, G. Yang, et al. (2025) Robomind: benchmark on multi-embodiment intelligence normative data for robot manipulation. In Robotics: Science and Systems (RSS) 2025, Cited by: Table 4, §2.

[^58]: P. Wu, Y. Shentu, Z. Yi, X. Lin, and P. Abbeel (2023) GELLO: a general, low-cost, and intuitive teleoperation framework for robot manipulators. arXiv preprint arXiv:2309.13037. External Links: 2309.13037 Cited by: §2.

[^59]: F. Xiang, Y. Qin, K. Mo, Y. Xia, H. Zhu, F. Liu, M. Liu, H. Jiang, Y. Yuan, H. Wang, L. Yi, A. X. Chang, L. J. Guibas, and H. Su (2020) SAPIEN: a simulated part-based interactive environment. arXiv preprint arXiv:2003.08515. External Links: 2003.08515 Cited by: §2, §2.

[^60]: M. Xu, H. Zhang, Y. Hou, Z. Xu, L. Fan, M. Veloso, and S. Song (2025) DexUMI: using human hand as the universal manipulation interface for dexterous manipulation. arXiv preprint arXiv:2505.21864. External Links: 2505.21864 Cited by: §2.

[^61]: Y. Yin, Z. Han, S. Aarya, S. Xu, J. Wang, J. Peng, A. Wang, A. Yuille, and T. Shu (2025) PartInstruct: part-level instruction following for fine-grained robot manipulation. In Proceedings of Robotics: Science and Systems (RSS), Cited by: Table 4, §2.

[^62]: K. Zakka, B. Tabanpour, Q. Liao, M. Haiderbhai, S. Holt, J. Y. Luo, A. Allshire, E. Frey, K. Sreenath, L. A. Kahrs, C. Sferrazza, Y. Tassa, and P. Abbeel (2025) MuJoCo playground: an open-source framework for gpu-accelerated robot learning and sim-to-real transfer. arXiv preprint arXiv:2502.08844. External Links: 2502.08844 Cited by: §2.

[^63]: T. Z. Zhao, V. Kumar, S. Levine, and C. Finn (2023) Learning fine-grained bimanual manipulation with low-cost hardware. In Robotics: Science and Systems, Cited by: §2, §2.

[^64]: C. Zhou, C. Peers, Y. Wan, R. Richardson, and D. Kanoulas (2022) TeLeMan: teleoperation for legged robot loco-manipulation using wearable imu-based motion capture. arXiv preprint arXiv:2209.10314. External Links: 2209.10314 Cited by: §2.

[^65]: Y. Zhu, J. Wong, A. Mandlekar, R. Martín-Martín, A. Joshi, S. Nasiriany, and Y. Zhu (2020) Robosuite: a modular simulation framework and benchmark for robot learning. arXiv preprint arXiv:2009.12293. External Links: 2009.12293 Cited by: §2.

[^66]: B. Zitkovich, T. Yu, S. Xu, P. Xu, T. Xiao, F. Xia, J. Wu, et al. (2023-06–09 Nov) RT-2: vision-language-action models transfer web knowledge to robotic control. In Proceedings of The 7th Conference on Robot Learning, J. Tan, M. Toussaint, and K. Darvish (Eds.), Proceedings of Machine Learning Research, Vol. 229, pp. 2165–2183. Cited by: §2.

[^67]: Y. Zou, Z. Zhou, C. Shi, Z. Ye, J. Huang, Y. Ding, and B. Zhao (2025) U-arm: ultra low-cost general teleoperation interface for robot manipulation. arXiv preprint arXiv:2509.02437. External Links: 2509.02437 Cited by: §2.
