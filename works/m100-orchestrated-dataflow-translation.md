---
sourceTitle: "M100: An Orchestrated Dataflow Architecture Powering General AI Computing"
sourceUrl: "https://arxiv.org/html/2604.17862v1"
sourceRequestedUrl: "https://arxiv.org/html/2604.17862v1"
sourceAdapter: "generic"
sourceCapturedAt: "2026-08-06T09:09:25.627Z"
sourceConversionMethod: "defuddle"
sourceKind: "generic/article"
sourceLanguage: "en"
sourceFigureCount: 16
title: "M100：驱动通用 AI 计算的编排式数据流架构"
translatedAt: "2026-08-06"
translator: "AI Translation Pipeline"
pipelineRunId: "2026-08-06-batch"
pipelineSource: "translate/2026-08-06-batch/works-ready/m100-orchestrated-dataflow-translation.md"
---

# M100：驱动通用 AI 计算的编排式数据流架构

Yan Xie <sup>∗</sup>, Changkui Mao, Changsong Wu, Chao Lu, Chao Suo, Cheng Qian, Chun Yang, Danyang Zhu, Hengchang Xiong, Hongzhan Lu, Hongzhen Liu, Jiafu Liu, Jie Chen, Jie Dai, Junfeng Tang, Kai Liu, Kun Li, Lipeng Ge, Meng Sun, Min Luo, Peng Chen, Peng Wang, Shaodong Yang, Shibin Tang, Shibo Chen, Weikang Zhang, Xiao Ling, Xiaobo Du, Xin Wu, Yang Liu, Yi Jiang, Yihua Jin, Yin Huang, Yuli Zhang, Zhen Yuan, Zhiyuan Man, Zhongxiao Yao <sup>∗</sup> 项目负责人。其他作者按名字首字母排序。通讯作者：Yan Xie (xieyan@lixiang.com) 和 Danyang Zhu (zhudanyang@lixiang.com)。

## 摘要

随着基于深度学习的 AI 技术在生活各个方面的快速发展，对通用 AI 计算架构的需求持续增长。当前基于 GPGPU 的架构虽然能够灵活应对多样化的 AI 工作负载，但在效率和成本效益方面常常表现不足。相反，各种领域专用架构 (Domain-Specific Architecture, DSA) 虽然在特定 AI 任务上表现出色，但难以将能力扩展到更广泛的应用场景，更不用说适应快速演进的 AI 算法格局。

M100 是理想汽车对这一挑战的回应：一种高性能且成本有效的架构，旨在满足自动驾驶 (Autonomous Driving, AD)、大语言模型 (Large Language Model, LLM) 和智能人机交互的 AI 推理需求。这些领域对于构建当今最具竞争力的汽车平台至关重要。M100 采用数据流并行架构，通过编译器-架构协同设计，不仅编排计算，更关键的是编排数据在时间和空间上的移动。借助数据流计算的固有效率，我们的软硬件集成方案在提升整体系统性能的同时，显著降低了硬件复杂度和成本。遵循数据流范式，M100 在很大程度上消除了缓存。张量计算由编译器和运行时管理的数据流驱动，数据在计算单元与片上/片外存储器之间流动，相比传统的基于缓存的系统，实现了更高的效率和可扩展性。另一个关键设计原则是为调度、发射和执行选择正确的操作粒度——贯穿编译器、固件和硬件。认识到 AI 工作负载的共性，我们选择张量（无论大小）作为 M100 架构中的基本数据元素。M100 已在多种推理应用中展现出通用 AI 计算能力，包括 UniAD（用于自动驾驶）和 LLaMA（用于大语言模型）。基准测试结果表明，M100 在自动驾驶应用中优于 GPGPU 架构，具有更高的硬件利用率。我们相信，M100 代表了通用 AI 计算架构未来融合的一个有前景的方向。

<sup>†</sup>


Yan Xie <sup>∗</sup>, Changkui Mao, Changsong Wu, Chao Lu, Chao Suo, Cheng Qian, Chun Yang, Danyang Zhu, Hengchang Xiong, Hongzhan Lu, Hongzhen Liu, Jiafu Liu, Jie Chen, Jie Dai, Junfeng Tang, Kai Liu, Kun Li, Lipeng Ge, Meng Sun, Min Luo, Peng Chen, Peng Wang, Shaodong Yang, Shibin Tang, Shibo Chen, Weikang Zhang, Xiao Ling, Xiaobo Du, Xin Wu, Yang Liu, Yi Jiang, Yihua Jin, Yin Huang, Yuli Zhang, Zhen Yuan, Zhiyuan Man, Zhongxiao Yao <sup>∗</sup> 项目负责人。其他作者按名字首字母排序。通讯作者：Yan Xie (xieyan@lixiang.com) 和 Danyang Zhu (zhudanyang@lixiang.com)。

## 摘要

随着基于深度学习的 AI 技术在生活各个方面的快速发展，对通用 AI 计算架构的需求持续增长。当前基于 GPGPU 的架构虽然能够灵活应对多样化的 AI 工作负载，但在效率和成本效益方面常常表现不足。相反，各种领域专用架构 (Domain-Specific Architecture, DSA) 虽然在特定 AI 任务上表现出色，但难以将能力扩展到更广泛的应用场景，更不用说适应快速演进的 AI 算法格局。

M100 是理想汽车对这一挑战的回应：一种高性能且成本有效的架构，旨在满足自动驾驶 (Autonomous Driving, AD)、大语言模型 (Large Language Model, LLM) 和智能人机交互的 AI 推理需求。这些领域对于构建当今最具竞争力的汽车平台至关重要。M100 采用数据流并行架构，通过编译器-架构协同设计，不仅编排计算，更关键的是编排数据在时间和空间上的移动。借助数据流计算的固有效率，我们的软硬件集成方案在提升整体系统性能的同时，显著降低了硬件复杂度和成本。遵循数据流范式，M100 在很大程度上消除了缓存。张量计算由编译器和运行时管理的数据流驱动，数据在计算单元与片上/片外存储器之间流动，相比传统的基于缓存的系统，实现了更高的效率和可扩展性。另一个关键设计原则是为调度、发射和执行选择正确的操作粒度——贯穿编译器、固件和硬件。认识到 AI 工作负载的共性，我们选择张量（无论大小）作为 M100 架构中的基本数据元素。M100 已在多种推理应用中展现出通用 AI 计算能力，包括 UniAD（用于自动驾驶）和 LLaMA（用于大语言模型）。基准测试结果表明，M100 在自动驾驶应用中优于 GPGPU 架构，具有更高的硬件利用率。我们相信，M100 代表了通用 AI 计算架构未来融合的一个有前景的方向。

<sup>†</sup>

## I 引言

自动驾驶技术长期以来一直处于 AI 技术演进的前沿。最先进的视觉-语言-动作 (Vision-Language-Action, VLA) 模型 [^6] [^7] [^38] [^37] [^8] 涵盖了自动驾驶任务的诸多方面，如视觉感知、环境理解和动作规划。广泛多样的 AI 推理任务需要一种多功能的软硬件解决方案，不仅性能优异，还能适应多种形式的深度学习推理算法。此外，汽车内部的应用环境（很可能是电动汽车）也要求加速器架构具有小的物理尺寸和功耗占用。理想汽车很早就认识到，自研的自动驾驶加速器芯片对于交付在自动驾驶能力和物料清单 (Bill of Materials, BOM) 成本方面都具有竞争力的汽车产品至关重要。

与许多其他汽车制造商一样，理想汽车最初基于现成的 GPGPU 平台 [^19] [^25] 开发其自动驾驶系统。尽管这些 GPGPU 平台凭借通用可编程性和成熟的软件生态系统优势，能够支持理想汽车早期几代自动驾驶系统的开发和部署，但其局限性也逐渐在峰值性能、效率、定制化和拥有成本等方面显现。主要汽车制造商已选择开发自研的自动驾驶推理芯片 [^4] [^15] [^3]，与其自动驾驶模型和软件栈垂直集成。为了实现为客户提供卓越自动驾驶体验同时保持较低物料清单成本的最终目标，理想汽车也踏上了开发这样一款 AI 推理加速器芯片的旅程，采用创新架构满足所有性能和成本指标。此外，该架构应具备面向未来的特性，使其能够适应快速演进的自动驾驶模型和算法。

这一努力的成果是集成了 M100 NPU 的 M100 SoC，一种编排式数据流架构，提供强大的通用 AI 计算能力，已在自动驾驶任务中得到验证。我们选择数据流架构是因为大多数深度学习 (Deep Learning, DL) 推理算法涉及张量计算和操作任务，其数据移动和转换模式通常是规则和可预测的。数据流架构 [^10] [^23] [^2] [^1] [^29] [^27] [^28] [^21] [^20] [^13] [^36] [^33] [^30] [^26] [^22] [^18] [^17] [^11] [^9] [^5] [^12] [^31] 能够以最小的同步开销有效地并行化这些任务。在数据流编译器的帮助下，M100 通过允许编译器在更高粒度上编排任务执行，成功避免了传统数据流架构相关的设计复杂性和开销，因此我们称 M100 为"编排式数据流架构"。M100 架构的成功还需要理想汽车团队在平衡软硬件复杂度、选择加速操作的粒度、以及选择硬件组件的确定性和非确定性行为程度时做出正确的权衡。我们认为，理想汽车的 M100 架构可能恰好处于应对通用 AI 推理计算挑战的最佳位置。

本文的其余部分介绍 M100 架构及其应用成果。第 II 节概述了理想汽车开发自研 AI 推理芯片的动机。第 III 节解释了指导 M100 架构的设计原则。第 IV 节详细描述了 M100 NPU。第 V 节简要介绍编译器和软件栈。第 VI 节展示评估方法和实际结果。最后，第 VII 节总结了理想汽车的努力，并讨论了 M100 项目的未来方向。

## II 动机

基于深度学习的自动驾驶系统依靠神经网络使用摄像头图像和激光雷达数据进行感知、预测和规划。这些任务计算密集，必须以低延迟执行以确保高速行驶时的安全运行。基于 GPGPU 的平台，如 NVIDIA 的 Orin [^19] 和 Thor [^25]，构建在增强了张量核心 (tensor core) [^16] 的 SIMT 架构 [^24]（单指令多线程）之上。虽然它们因多功能性和强大的并行处理能力而被广泛使用，但也存在权衡。这些现成解决方案并非针对特定的自动驾驶软件栈定制，常常包含未使用的功能，且总拥有成本 (Total Cost of Ownership, TCO) 较高。其基于缓存的内存层次结构也带来了优化挑战和不可预测性。作为回应，一些公司转向了领域专用架构，如 Tesla 的 FSD 芯片 [^4] [^15] [^3]，将神经网络操作硬编码到固定流水线中。虽然这些架构对目标任务高效，但往往难以跟上快速演进的 AI 算法步伐——特别是随着端到端视觉-语言-动作模型的兴起——导致生命周期更短、重新设计成本更高。

认识到需要一种折中方案，理想汽车着手设计一种在效率和灵活性之间取得平衡的 NPU 架构。其成果就是 M100——一种可扩展的数据流驱动架构，旨在支持广泛的边缘 AI 推理任务。其模块化设计和分层互连实现了高硬件利用率，并能跨车辆世代适应，有助于分摊开发成本，同时在快速变化的 AI 需求面前保持性能领先地位。

## III 编排式数据流架构

### III-A 设计哲学

#### III-A1 计算单元

与 CPU 和 GPU 的传统指令序列执行模型不同，M100 NPU 采用了数据驱动的并行执行模型 [^32] [^34]。M100 NPU 不是执行预定义的指令流，而是将张量操作指令分发到大量执行单元，数据在这些单元之间流动并触发指令操作。为了进一步提升 M100 NPU 的能力，同构的计算节点（每个节点都能够运行完整的张量指令集）通过为节点间数据移动和同步优化的可扩展网络互连。在每个节点内，各个执行单元之间的数据和同步路径也可以灵活构建，以支持节点内数据流执行。凭借其模块化和可扩展的设计，M100 NPU 架构致力于提供一个弹性的硬件抽象层，编译器可以在其上映射 AI 推理任务并以最优性能编排其执行。以下各节讨论了如何为 M100 NPU 架构的各个方面做出设计决策。

![](imgs/img-001-x1.png)



M100 NPU 旨在加速自动驾驶中使用的广泛深度学习推理任务，其中许多任务严重依赖张量收缩操作，如卷积和矩阵乘法——需要计算密集的功能单元来实现高吞吐量。此外，向量操作虽然计算密集度较低，但涉及多种多样的操作，需要在灵活性和性能之间取得平衡。标量计算也很常见，需要通用 CPU 核心。如图 1 所示，M100 将张量、向量和标量处理单元集成到具有共享本地内存和同步机制的统一计算块中。该架构通过实例化多个这样的计算块并通过片上通信网络连接来实现扩展，软件在它们之间编排粗粒度的指令分发。

#### III-A2 内存层次结构

并行性仍然是加速 AI 推理工作负载的主要策略，但系统性能在很大程度上取决于如何在并行执行单元之间共享数据。缓存一致性内存系统通过抽象大型共享内存空间简化了编程，在可能的情况下利用时间和空间局部性。然而，这些系统在大规模并行环境中难以扩展，并且常常阻碍流式性能——这是 AI 推理的关键方面。为了解决这个问题，M100 采用了现代化的数据流计算模型。

如图 2 所示，M100 NPU 在很大程度上避免了多级缓存。每个张量处理块 (Tensor Processing Block, TPB) 包含高带宽本地内存，使功能单元能够在计算和操作任务期间并行地流式输入和输出数据。TPB 内存与 NPU 共享 SRAM 之间的数据传输通过可编程的 DMA 单元显式控制。额外的 DMA 引擎管理 SRAM 和 DDR 内存之间的传输。这种软件管理的数据移动——结合高效的数据流同步和充足的缓冲——允许计算和数据传输重叠，最大化吞吐量。

![](imgs/img-002-x2.png)



#### III-A3 操作粒度

由于大多数 AI 推理工作负载涉及基于张量的计算和数据传输，因此在张量级别定义加速器指令是很自然的。这使得流式架构成为可能，其中操作数和结果直接流向和流出内存，无需寄存器堆和显式的加载/存储指令。内存延迟在大型张量上被分摊，流水线执行最大化了吞吐量。虽然一些不规则操作仍然需要在传统 CPU 核心上进行细粒度计算，但这些任务通常不在关键路径上。因此，M100 将大部分硬件资源专用于规则的张量粒度计算，辅以轻量级 CPU 核心来满足细粒度通用计算需求。

#### III-A4 数据流同步

M100 NPU 设计的另一个关键方面是其高效的同步机制。作为一个大规模并行系统，M100 通过生产者-消费者同步模型协调许多并发处理引擎，如图 3 所示。图的上半部分说明了两个代理之间的生产者-消费者同步。红色箭头线表示内存读写操作。黑色箭头线表示对同步计数器 (Synchronization Counter, SC) 的更新，蓝色箭头线表示对同步计数器的监控行为。虚线箭头表示数据从生产者到消费者的逻辑移动方向。生产者将数据写入预分配的缓冲区，并通过更新同步计数器来通知数据可用。消费者监控此同步计数器，一旦预期的数据可用就开始处理。相反，消费者更新单独的同步计数器，通知生产者缓冲区空间已释放，从而使数据流能够继续。这些同步计数器操作由专用硬件处理，确保最小的同步开销。同步粒度由软件控制，允许在张量操作期间灵活决定生产者和消费者协调的频率。图的下半部分展示了如何将这种基于同步计数器的同步机制扩展到一组代理，其中一些代理可能同时充当生产者和消费者——有效地使 M100 成为一个数据流并行计算系统。

M100 NPU 还支持其他同步模式，如屏障 (barrier)、广播 (broadcast) 和归约 (reduction)。这些同步机制高效且易于编程，不仅在单个 NPU 内工作，还可以在多芯片配置中跨多个 NPU 工作。

![](imgs/img-003-x3.png)



#### III-A5 指令分发

M100 NPU 使用集中式指令分发器，利用指令链总线 (Instruction Chain Bus, ICB) 高效地向多个处理单元广播张量操作指令。为了简化硬件设计，每个处理单元的指令必须按分发顺序执行，而不同单元之间的指令可以乱序完成。当存在依赖关系时，软件负责管理同步。与传统数据流架构不同，这种设计将部分复杂性转移到编译器和运行时，它们可以利用 AI 推理工作负载的规律性来规划和调度执行。这种"编排式数据流架构"在硬件简化和软件控制之间取得了实用的平衡，同时保留了数据流并行的效率。

#### III-A6 总结

总之，M100 NPU 集成了张量/向量计算引擎、DMA 单元和轻量级 CPU 核心。大部分计算以流式方式在张量级别执行，数据直接流向和流出内存。通用任务由轻量级 CPU 处理，可能带有向量扩展。编译器通过分发计算和数据移动指令并管理处理单元之间的同步来编排数据流执行。架构细节将在以下章节中讨论。

### III-B M100 概述

#### III-B1 M100 SoC

M100 是一款旨在支持理想汽车自动驾驶软件栈的 SoC。与其他自动驾驶芯片一样，它包括应用 CPU、多媒体 IP、安全岛和标准 I/O 接口。其关键差异化因素是由理想汽车自主开发的神经处理单元 (Neural Processing Unit, NPU)，用于加速 AI 推理。图 4 显示了高层次框图。

![](imgs/img-004-x4.png)



图 4 突出显示了 M100 SoC 的主要功能模块。它具有 8 个 LPDDR5X 子系统，提供 64 GB 内存和 273 GB/s 峰值带宽。MIPI-CSI 系统支持最多 11 个摄像头输入，图像信号处理器 (Image Signal Processor, ISP) 子系统处理原始图像，供 NPU 的感知模型使用。视频处理单元 (VPU) 处理视频编码/解码，而功能安全岛 (FSI) 和安全引擎确保功能安全 (FuSa) 合规性和安全内容处理。电源管理单元 (PMU) 和时钟与复位单元 (CRU) 协调上电时序和时钟/复位分发。专用的 NPU 调度器分发推理任务并收集结果。调试与跟踪模块支持跨 CPU 和 NPU 子系统的侵入式和非侵入式调试。SoC 还包括用于外部存储的 UFS/QSPI 控制器、用于高速 I/O 的 USB/Ethernet，以及各种低速接口。CPU 集群集成了 24 个 ARM Cortex-A78AE 核心，具有一致性 CMN 互连和共享的末级缓存。

#### III-B2 M100 NPU

M100 NPU 是本文的核心，也是 SoC 中最重要的子系统。它占据了芯片的很大一部分面积，是 AI 推理的主要引擎。其创新的数据流架构使 M100 有别于其他 AI 加速器。图 5 显示了 NPU 的高层次架构。

![](imgs/img-005-x5.png)



NPU 通过三个主要接口与 SoC 的其余部分连接。首先，两个高带宽 AXI 主接口（每个 128 GB/s）能够通过片上网络 (Network-on-Chip, NoC) 系统访问 DDR 内存和其他 SoC 资源，该系统支持足够的未完成事务以维持峰值内存吞吐量。其次，NPU 可以向调度器 CPU 生成中断以通知事件，例如任务完成。第三，调度器 CPU 通过较低带宽的 AXI 从接口与 NPU 通信，以发出命令、检查状态和访问内部资源。

在内部，NPU 由一个中央控制块 (Central Control Block, CCB) 和 14 个张量处理块集群组成，每个集群包含 4 个 TPB。为了支持 AI 推理的数据移动需求，CCB 和 TPB 通过两个互连网络连接：二维网格总线 (2D Mesh Bus) 和数据环形总线 (Data Ring Bus, DRB)。网格总线在 TPB 集群、中央控制块、CPU、DMA 和块 SRAM 之间提供可扩展的高带宽点对点通信。它为每对节点提供高达 256 GB/s 的带宽——在低拥塞条件下扩展性良好。另一方面，数据环形总线提供确定性的高效广播路径，具有高达 256 GB/s 的聚合带宽，非常适合跨 TPB 进行数据多播。软件根据通信需求在网格总线和数据环形总线互连之间动态选择。

指令链总线将 CCB 以菊花链方式连接到 TPB 集群。CCB 中的 RISC-V 核心通过指令链总线向单个或多个 TPB 分发指令。这些 TPB 指令定义张量操作，包括丰富的元数据，如张量形状和通信需求。虽然每条指令可能有数千比特长——以 64 比特/周期传输需要数百个周期——但其执行时间跨越数万个周期，因此指令分发不是瓶颈。

以下章节深入探讨 M100 NPU 构建块的架构细节，重点介绍其数据流执行模型和精心选择的编程粒度如何同时实现高性能和灵活性。

## IV NPU 架构细节

### IV-A 中央控制块

![](imgs/img-006-x6.png)



中央控制块 (CCB)，如图 6 所示，是 NPU 的控制中心。其固件运行在 4 核 SiFive X280 RISC-V CPU 上，每个核心配有一个定制向量引擎，通过指令链总线分发 TPB 指令。这些引擎解析并转发大型复杂的 TPB 指令——通常有数千比特长——以定义诸如矩阵乘法或逐元素加法等张量操作。指令包括操作数访问、计算方法和结果处理。凭借四对 CPU-引擎，CCB 支持最多四个并发推理任务。TPB 指令还可以使用目标掩码广播到多个 TPB，鉴于其较长的执行时间，指令链总线带宽通常足以维持持续吞吐量。CCB 包括 32 MB 片上 SRAM，分为四个 8 MB 块，采用 4 KB 交织以实现高带宽并行访问。两个 DMA 引擎管理 DDR 和 CCB SRAM 之间的数据传输，还可以通过数据环形总线直接向 TPB 广播权重，数据环形总线支持高达 256 GB/s，与 DDR 读取带宽相匹配。CCB 的其他功能包括屏障同步和中断生成。屏障操作确保 TPB 组完成其当前指令后再继续，适用于不频繁的全局同步点。可以通过控制寄存器向 CCB 或主机 CPU 触发中断。所有组件通过 Arteris FlexNoC 互连。

### IV-B 张量处理块集群

![](imgs/img-007-x7.png)



图 7 说明了 TPB 集群的结构。引入集群级层次结构有两个主要原因。首先，四个 TPB 可以共享公共资源——如指令缓冲区、指令链总线和数据环形总线节点，以及一个 RISC-V CPU——允许将更多硅面积分配给张量处理，从而提高计算密度。其次，四个 TPB 之间的紧密距离实现了低延迟、高带宽通信，非常适合跨少量 TPB 的任务——这在我们的自动驾驶推理工作负载中很常见。对于更大的任务，多个集群可以通过网格总线协作，尽管程序员应注意不同集群中 TPB 之间相对较低的通信效率并相应地应用优化。

共享的 RISC-V 向量 CPU (SiFive X280) 提供通用计算。TPB 指令可以通过中断触发基于 CPU 的任务。CPU 检索任务参数，执行预加载的服务例程，并在完成后将 TPB 指令标记为完成。最多四个 TPB 可以同时请求服务，CPU 进行仲裁并按顺序处理请求。这种机制允许基于 CPU 的操作遵循与张量操作相同的指令语义，简化了编译、调度和分发。

每个集群包括一个 TPB 指令队列，从指令链总线下载指令并将其存储在大型缓冲区中。指令在准备就绪时分发到 TPB 功能单元，无需全局执行顺序——仅保留单个 TPB 同一功能单元内的顺序。这反映了我们编排式数据流架构的核心，编译器发出松散排序的指令流，运行时执行由数据就绪性和同步条件驱动。指令队列确保功能单元一旦输入和同步满足就保持忙碌。

与 CCB 类似，每个集群包括一个内部片上网络，连接四个 TPB 和 CPU 内存端口。集群片上网络通过主/从端口链接到 NPU 级网格总线，实现双向数据访问。指令链总线节点处理 TPB 指令传递，而数据环形总线节点管理进出集群的广播数据流量。
### IV-C 张量处理块

![](imgs/img-008-x8.png)



张量处理块 (Tensor Processing Block, TPB) 是负责张量计算和转换的核心单元。如图 8 所示,它由多个专用功能单元组成,每个单元针对特定类型的张量操作进行了优化。以下简要介绍 TPB 内的主要功能单元:

- **高带宽共享内存 (High Bandwidth Shared Memory, HBSM)** 既作为 2 MB 数据存储,也作为 TPB 功能单元的灵活通信枢纽。生产者和消费者通过预定义的地址范围交换数据,并通过计数器进行同步——无需专用数据通路。为减少 SRAM 端口冲突并保持性能,HBSM 采用分组存储器设计。
- **张量计算单元 (Tensor Computing Unit, TCU)** 处理计算密集型操作,如卷积和矩阵乘法,并包含用于激活函数的非线性流水线。
- **可配置向量单元 (Configurable Vector Unit, CVU)** 由模块化向量运算单元组成,可重新配置为定制流水线。它高效处理基本向量操作和常见 AI 任务,如池化、softmax 和层归一化。
- **数据转换 DMA 单元 (Data Transformation DMA Unit, DTDU)** 处理 TPB 内的数据移动或向其他 TPB 广播数据。它还支持张量布局转换,如矩阵转置。
- **CPU 启动单元 (CPU Starter Unit, CSU)** 处理请求集群 CPU 协助的 TPB 指令。它保存指令参数并触发中断。CPU 随后通过向量协处理器指令扩展 (Vector Coprocessor Instruction eXtension, VCIX) 接口访问请求 TPB 的数据和设备。
- **定制引擎 (Custom Engine)** 代表 CPU 通过 VCIX 接口执行 TPB 操作,包括控制寄存器和内存访问。它还包括聚集/分散 DMA 单元 (Gather/Scatter DMA Unit, GSDU),CPU 可使用它移动具有非连续地址模式的数据。
- **同步单元 (Synchronization Unit, SU)** 管理同步计数器,TPB 功能单元可在本地更新或监控这些计数器。它还通过 DRB 和 NPU NoC 支持远程同步。

以下各节将更详细地讨论 TPB 功能单元。

1) 高带宽共享内存单元

![](imgs/img-009-x9.png)



2 MB 的 HBSM SRAM 在所有 TPB 功能单元间均匀共享。如图 9 所示,大多数单元在执行任务时以流式方式从 HBSM 读入和写出张量——通常并行进行。由于一个单元的输出经常作为另一个单元的输入,HBSM 实现了高效的生产者-消费者通信,无需专用数据通路。虽然共享内存引入了延迟(约 20 个周期),但 TPB 操作的流式特性最小化了其影响,前提是保持高带宽——不仅针对单个单元,还包括多个单元的并发访问,这对维持 TPB 吞吐量至关重要。

HBSM 通过分组架构实现高带宽,使用 32 个存储器组,每组支持每周期 32 字节。地址空间以 32 字节粒度交织,支持跨组同时访问。虽然更多的组通过减少冲突来提高带宽,但也会增加布线拥塞——尤其是在高吞吐量设计中。经过广泛的建模和后端评估,选择了 32 个组和 8 个请求端口的配置作为最优平衡。

当多个请求者访问同一组时,HBSM 使用轮询仲裁并保证每个请求者的顺序访问。同步操作——如标记数据已生产或已消费——与内存访问绑定,并在赢得仲裁后触发。从那时起,该访问被视为全局可见,因为没有后续请求可以超越它。通过统一数据移动和同步,HBSM 充当 M100 数据流架构的中央支柱。

![](imgs/img-010-x10.png)



2) 张量遍历单元

TPB 功能单元通过流式输入和输出数据来访问 HBSM 中的张量。这需要生成针对特定计算模式定制的地址序列。对于卷积等操作,地址通常遵循由嵌套循环定义的复杂非线性模式,而非简单的线性增量。为支持这一点,使用张量遍历单元 (Tensor Walker Unit, TWU) 灵活生成所需的地址序列,实现对输入激活和权重的高效访问。

通常,一个 TPB 功能单元有两个或更多输入 TWU 和一个输出 TWU。TWU 可通过该功能单元的 TPB 指令进行配置,指定嵌套循环级别数,以及每个循环级别的初始值 (Initial)、步长 (Step) 和终止值 (Final)。配置后,TWU 在每次迭代时生成一个地址,直到每个循环级别的值计数器 (Value counter) 达到该级别的终止值。图 10 展示了一个 3 级 TWU 的示例。输出地址是所有循环级别的值计数器之和。当内层循环级别的值达到终止值时,某个循环级别的值计数器将被触发,按该循环级别的步长递增。当然,最内层循环的值计数器在每次迭代时无条件递增。每当某个循环级别的值计数器达到终止值时,它在下次递增时从初始值重新开始。TWU 还支持双缓冲的地址生成。通过在外层循环级别指定带缓冲区偏移的步长值,程序员可以无缝地在两个缓冲区域之间交替。

TWU 生成丰富地址模式的能力——结合基于 HBSM 的数据共享——使得 TPB 功能单元间的复杂数据通信得以高效实现,无需专用数据通路或缓冲区。因此,TWU 是 M100 NPU 简洁而强大的数据流架构的关键使能技术。

3) 同步单元

同步是数据流并行计算的关键组件。功能单元必须在数据生产或消费时通知对等单元,以维持流水线阶段间的流动。传统架构依赖原子操作或独占加载/存储指令进行同步,这可能效率低下且与缓存和内存子系统紧密耦合。相比之下,M100 NPU——作为针对数据流执行优化的 AI 加速器——的同步可大大简化,处理方式如下:

- 一个代理在执行特定任务时更新自己的执行状态。
- 另一个代理监控第一个代理的状态,并决定是否可以继续下一步。

这种更新/监控关系可在两个代理间双向操作。例如,生产者在监控消费者的消费状态时更新其数据生产状态,同时消费者在监控生产者的生产状态时更新其消费状态。这使两者能够协同工作形成计算流水线。同样的机制可以扩展到多个代理,使用简单的状态更新和监控操作形成同步网络。

在每个 TPB 内,同步单元 (Synchronization Unit, SU) 管理跟踪和协调执行状态的硬件计数器。功能单元申领一个计数器来更新自己的进度,并可监控其他计数器以确定依赖关系是否满足。更新和监控操作在 TPB 指令定义的特定执行阶段触发。当发出更新请求时,SU 将分配的计数器加一。监控请求包含预期值,只有当计数器达到或超过该值时,SU 才会响应。在此之前,请求单元暂停执行。软件控制哪些计数器被更新或监控,通过适当的分配,可以在并行功能单元间实现高效的同步执行流水线。

4) 张量计算单元

![](imgs/img-011-x11.png)



张量计算单元 (Tensor Computing Unit, TCU) 使用密集的计算元件阵列加速张量收缩操作。为在有限的内存带宽下维持高吞吐量,数据复用至关重要。如图 11 所示,TCU 将乘累加 (Multiply-Accumulate, MAC) 单元排列为 8×64 阵列。每个 MAC 每周期执行 4 元素点积。激活数据在行间复用,权重数据在列间复用。对于大小为 32×32 × 32×64 的矩阵乘法,计算在 32 个周期内完成——匹配激活和权重缓冲区的 32B/周期和 64B/周期输入带宽(假设 1 字节元素)。通过双缓冲,TCU 可以在矩阵乘法和卷积操作中维持峰值吞吐量。

MAC 之后,部分和存储在输出缓冲区中,并在写入 HBSM 前通过非线性激活流水线。由于张量收缩通常沿大轴归约,输出数据较小,写带宽很少成为瓶颈。对于大张量,TCU 包含外层循环控制逻辑以高效迭代块,使流水线保持活跃,空闲周期最少。

5) 可配置向量单元

![](imgs/img-012-x12.png)



图 12 展示了可配置向量单元 (Configurable Vector Unit, CVU) 的核心组件,它由单功能向量运算算子组成。每个算子接受一个或两个输入向量流并产生单个输出流。TPB 指令可配置 CVU 将输入路由通过单个算子或构建带中间缓冲区的多级流水线。这种灵活性使常见向量操作得以高效执行。图 12 底部展示了用于 Softmax 计算的 CVU 配置——这是基于 Transformer 模型中频繁使用的算子。跨配置的流水线阶段的计算步骤在参与的向量算子处标注,以说明 CVU 如何以最优效率执行 softmax 操作。

对于无法完全流水线化的更复杂向量操作,CVU 可以分多个阶段处理,每个阶段由单独的 TPB 指令处理。虽然这可能降低吞吐量,但性能仍与传统向量核心相当或更好。得益于其广阔的配置空间,CVU 非常适合适应 AI 推理工作负载中的多样化向量计算模式。

6) DMA

除计算单元外,每个 TPB 还配备高性能 DMA 引擎,以支持 TPB 内部和跨多个 TPB 的数据流。TPB 中有两种类型的 DMA:

- **数据转换 DMA 单元 (Data Transformation DMA Unit, DTDU)** 像计算单元一样运行并执行 TPB 指令。它处理 HBSM 内的数据移动,支持矩阵转置等操作,并能通过用预定义值填充指定地址范围来高效初始化内存。
- **聚集/分散 DMA 单元 (Gather/Scatter DMA Unit, GSDU)** 由集群 CPU 管理,不直接执行 TPB 指令。它处理难以编码在标准 TPB 指令中的不规则数据移动模式。相反,TPB 指令触发 CPU 启动单元 (CSU),后者启动 CPU 例程来控制 GSDU。GSDU 在本地 HBSM 和外部内存(如另一个 TPB 的 HBSM、CCB SRAM 或 DDR)之间传输数据。顾名思义,它支持本地和远程内存空间之间的聚集和分散操作。

7) CPU 启动单元

CSU 执行触发中断以请求集群 CPU 协助的 TPB 指令。任务参数存储在 CSU 中,CPU 的中断服务例程检索它们以确定所需操作。任务可能涉及标量或向量处理,或通过 VCIX 接口控制 GSDU 进行运行时确定的数据移动。例程完成后,它通知 CSU,后者随后将 TPB 指令标记为完成。

## V 编译器和运行时软件栈

作为垂直集成解决方案的一部分,M100 编译器和运行时软件栈在确保正确功能和卓越性能方面发挥着关键作用。

### V-A 编译器

如图 13 所示,M100 AI 编译器工具链包括时空调度器、图编译器和后端编译器:

![](imgs/img-013-x13.png)



- **时空调度器 (space-time scheduler)** 将神经网络子图映射到 M100 NPU 硬件上。如有必要,大张量被分割为小张量 (mini-tensor),沿着数据流编译器的时空调度器构建的处理流水线传递。图 14 展示了时空调度的一个示例。包含四个计算算子(OP1、OP2、OP3、OP4)的子图在空间上分布到四个 TPB 中。输入张量沿多个轴进行维度分解,产生若干小张量,随后按照时间调度的阶段流经分配的 TPB。
- **图编译器 (graph compiler)** 执行图优化和动态张量的动态内存分配。图优化包括张量融合、死代码消除、代数简化、布局转换等。
- **后端编译器 (back-end compiler)** 是一个 C 扩展编译器,生成利用 M100 架构硬件能力的内建指令,如张量计算、数据移动和同步。

![](imgs/img-014-x14.png)



### V-B 运行时软件

M100 运行时软件栈包括运行在 SoC 的 ARM Cortex-A78 核心上的 AI 推理运行时和 NPU 驱动,以及运行在 NPU RISC-V 核心上的 NPU 固件。AI 推理运行时负责准备输入数据、加载神经网络模型、用分配的资源启动任务,以及对推理结果进行后处理供下游应用使用。AI 推理运行时还监控 NPU 运行中的任何错误或异常状态,并确保 NPU 满足汽车功能安全 (Functional Safety, FuSa) 要求。NPU 驱动作为高层应用软件的硬件抽象层。NPU 固件采用即时编译 (Just-In-Time, JIT) 技术,基于 M100 编译器工具链生成的二进制代码动态生成优化的 TPB 指令。固件还即时计算张量形状和张量存储的内存地址。TPB 指令由 NPU 固件发送到分配给任务的一组 TPB。

## VI 评估结果

为了评估 M100 NPU 架构的性能，我们对 M100 与 NVIDIA Thor-U 进行了对比研究。Thor-U 是 NVIDIA 为自动驾驶和边缘 AI 推理开发的先进 SoC 平台。我们使用与自动驾驶应用相关的基准测试进行评估。本节首先介绍两个平台的硬件配置，然后分析所选基准测试的特性，最后通过性能数据展示 M100 在关键自动驾驶工作负载中如何实现与 Thor-U 相当甚至更优的 AI 推理效率和硬件利用率。

### VI-A 硬件配置

截至撰写本文时，理想汽车尚未正式公开 M100 的详细性能规格，仅披露了 DDR 带宽和芯片面积等基本指标。表 I 列出了 M100 的可用数据以及 Thor-U 的对应数据。两个平台提供相同的 DDR 带宽，而 Thor-U 的芯片面积略大——这表明两者的原始计算能力相当。为确保公平性，在基准测试期间，两个平台均在相同的功耗预算下采集性能数据。

表 I：NVIDIA Thor-U 与 M100 硬件配置对比

| 指标 | Thor-U | M100 |
| --- | --- | --- |
| DDR 内存带宽 | 273 GB/s | 273 GB/s |
| 芯片面积 | 415 $mm^{2}$ | 399.8 $mm^{2}$ |
| 工艺制程 | TSMC N4 | TSMC N5A |

### VI-B 基准测试

自动驾驶和智能座舱是现代智能汽车的两个重要特性。我们选择了 UniAD [^14]（最先进的端到端自动驾驶算法）作为自动驾驶基准应用。对于智能座舱场景，LLaMA2-7B [^35] 等大语言模型是支持车辆与驾驶员/乘客智能交互的关键组件。因此，我们选择 LLaMA2-7B 作为性能评估的另一个重要基准。此外，为了全面评估自动驾驶场景中集成的视觉-语言-动作 (VLA) 能力的性能，我们选择了理想汽车自研的 MindVLA 模型的一个关键组件作为第三个基准。

在将 UniAD、LLaMA2-7B 和 MindVLA 移植到 Thor-U 和 M100 平台时，我们确保了两个系统在基准测试执行期间保持相当的计算资源和功耗。本节其余部分提供了这三个基准测试的详细信息。

#### VI-B1 模型架构

- 为了更好地代表理想汽车当前部署的自动驾驶算法，UniAD 基准测试用 RegNet 替换了 ResNet-101。如图 15 所示，UniAD 算法提供了一个统一框架，无缝集成了自动驾驶的两个核心任务：感知和预测。感知涵盖目标检测和跟踪，预测则处理运动预测和占用率预测。感知模块（BevFormer、TrackFormer、MapFormer）和预测模块（MotionFormer、OccFormer）均基于 Transformer 架构。这些组件通过大量查询令牌（例如 TrackFormer 中的 900 个）连接，为推理期间的并行处理提供了充足的机会。在进入第一个感知阶段（BEVFormer）之前，使用基于 CNN 的骨干网络从输入图像中提取特征。

  ![](imgs/img-015-x15.png)

  

- LLaMA2-7B 是一个基于 Transformer 的大语言模型，拥有约 70 亿参数。它采用标准的仅解码器 Transformer 架构，具有多头自注意力和前馈网络。推理过程包括两个阶段：预填充 (prefill) 阶段并行处理输入序列，解码 (decode) 阶段顺序生成令牌。

- MindVLA 是理想汽车的下一代自动驾驶算法，它将大语言模型组件与专家混合 (Mixture-of-Experts, MoE) Transformer 架构集成，以提高模型容量和推理效率。

#### VI-B2 计算复杂度分析

表 II 概述了 UniAD 中每个网络模型的参数数量和 MAC 操作数。基于 CNN 的骨干网络占用了大部分计算资源，主要是因为对高分辨率图像进行密集的卷积操作。在实际驾驶场景中，感知任务（BEVFormer、TrackFormer、MapFormer）通常以高于预测任务（MotionFormer 和 OccFormer）的帧率运行，因此需要更多的计算资源。因此，我们的分析重点关注 UniAD 中基于 CNN 的骨干网络和基于 Transformer 的感知模型。

表 II：UniAD 中网络模型的参数规模和 MAC 操作数

<table><thead><tr><th>模块</th><th>网络架构</th><th>网络模型</th><th>参数量 (M)</th><th>MAC 操作数 (GFLOPS)</th></tr></thead><tbody><tr><td rowspan="3">骨干网络</td><td>基于 CNN</td><td>RegNet + FPN</td><td>30</td><td>2381.6</td></tr><tr><td rowspan="6">基于 Transformer</td><td>BEVFormer</td><td>85.6</td><td>1492.9</td></tr><tr><td>TempFusion</td><td>0.2</td><td>49.0</td></tr><tr><td rowspan="2">感知</td><td>TrackFormer</td><td>8.5</td><td>97.17</td></tr><tr><td>MapFormer</td><td>6</td><td>105.94</td></tr><tr><td rowspan="2">预测</td><td>MotionFormer</td><td>22.6</td><td>266.55</td></tr><tr><td>OccFormer</td><td>46.2</td><td>687.62</td></tr><tr><td colspan="3">规划器</td><td>3.5</td><td>220.75</td></tr></tbody></table>

LLaMA 推理包括两个阶段：预填充和解码。在预填充阶段，输入序列中的所有令牌并行处理，大量并发令牌——类似于 UniAD 中的并行查询——提供了高度的计算并行性。相比之下，解码阶段每步生成一个令牌，并行性有限，使其成为内存受限的操作。

与 LLaMA2-7B 不同，MindVLA 的大语言模型组件采用专家混合 (MoE) 策略，具有 8 个专家。在评估中，我们使用参数量为 4.31 亿的配置作为基准。

### VI-C 实验结果

值得注意的是，我们的实验使用了 M100 NPU 14 个可用集群中的 12 个，代表了其总计算能力的 86%。这种配置旨在通过允许最多两个集群存在缺陷来确保更高的芯片良率。对于缺陷集群少于两个的芯片，可以利用额外的硬件资源实现更高的性能。

#### VI-C1 UniAD

表 III 对比了在 M100 和 Thor-U 平台上运行的六个 UniAD 基准测试的结果。对于 M100 平台，我们将 14 个可用计算集群中的 8 个用于 UniAD 任务，同时保留其余 6 个集群用于其他座舱域功能。这种分配策略展示了 M100 在保持性能隔离的同时处理多个特定领域工作负载的能力。

结果显示，M100 在不同的网络组件上实现了 1.2× 至 6.3× 的加速，大多数模块显示出 3.8× 至 4.4× 的性能提升。即使只有 8 个集群专用于自动驾驶任务，M100 仍能为感知任务维持 30 FPS，满足自动驾驶的实时性要求。相比之下，Thor-U 仅提供 7.9 FPS，这不足以在高速驾驶场景中部署导航自动驾驶功能。

在相同的功耗预算下，M100 提供了比 Thor-U 高 3.8× 的帧率。这一性能增益归功于 M100 为并行化 AI 推理任务而紧密集成的硬件-软件解决方案。具体而言，其编译器生成的精心编排的数据流执行，在计算和数据移动单元之间实现了极高的并行度，同时产生的同步开销极小。

表 III：M100 与 Thor-U 上 UniAD 不同网络的性能对比

|  | M100（8 个集群激活） | Thor-U | M100 加速比 |
| --- | --- | --- | --- |
| RegNet | 13.1 ms | 57.4 ms | 4.4x |
| FPN | 4.23 ms | 5.1 ms | 1.2x |
| BEVFormer | 7.92 ms | 32.83 ms | 4.1x |
| TempFusion | 4.47 ms | 17 ms | 3.8x |
| TrackFormer | 1.27 ms | 7.95 ms | 6.3x |
| MapFormer | 1.46 ms | 6.14 ms | 4.2x |
| 帧率 | 30 FPS | 7.9 FPS | 3.8x |

我们使用自研的性能分析软件追踪 M100 的行为，收集了详细的执行时间线数据。生成的执行时间线如图 16 所示。不同颜色的块代表各种处理单元随时间的活动：连续的段表示持续活动，而间隙表示空闲或等待期。在这个追踪中，在大部分采样窗口内，CCB 中的 DMA——以及其中一个 TPB 中的 TCU、CVU、CSU 和 GSDU——保持持续活动状态，任务执行存在大量重叠。这表明硬件利用率高，突出了 M100 架构强大的并行执行能力和整体效率。

![](imgs/img-016-trace.png)



#### VI-C2 LLaMA2-7B

在 LLaMA2-7B 基准测试设置中，输入序列长度设置为 1,024 个令牌。表 IV 总结了 M100 和 Thor-U 平台在推理任务的解码和预填充阶段的性能对比。对于解码阶段，我们采用 W4A16 量化，其中权重表示为 4 位整数，激活特征表示为 16 位浮点值。M100 实现了 21.34ms 的延迟，与 Thor-U 的 20ms 性能相当。尽管 Thor-U 在此指标上略有优势，但这主要归功于 NVIDIA 平台针对 LLaMA2-7B 等开源模型进行的大量优化。另一方面，由于 M100 和 Thor-U 平台共享相同的 DDR 内存带宽——这主要限制了解码阶段的性能——两个平台之间性能相当是预期的。对于预填充阶段，我们应用 W8A8 量化，将权重和激活都表示为 8 位整数。M100 展现了显著优势，在 79ms 内完成推理，而 Thor-U 需要 154ms——实现了 1.95× 的加速。这一改进归功于 M100 高效的张量处理单元以及数据流驱动的同步机制，该机制能够实现处理单元之间的无缝协调。

表 IV：M100 与 Thor-U 上 LLaMA2-7B 推理阶段性能对比

|  | M100（12 个集群激活） | Thor-U | M100 加速比 |
| --- | --- | --- | --- |
| decode | 21.34 ms (W4A16) | 20 ms (W4A16) | 0.94x |
| prefill | 79 ms (W8A8) | 154 ms (W8A8) | 1.95x |

#### VI-C3 MindVLA（大语言模型部分）

除了评估开源模型外，我们还测试了理想汽车自研的下一代自动驾驶模型 MindVLA。该评估展示了 M100 平台支持生产级自动驾驶应用的能力。表 V 展示了 M100 和 Thor-U 平台在 MindVLA 的大语言模型组件上的性能对比。

表 V：M100 与 Thor-U 上 MindVLA（大语言模型组件）性能对比

|  | M100（12 个集群激活） | Thor-U | M100 加速比 |
| --- | --- | --- | --- |
| decode | 0.1 ms | 0.3 ms | 3x |
| prefill | 0.84 ms | 1.74 ms | 2.1x |

在解码阶段，M100 实现了 0.1ms 的延迟，而 Thor-U 为 0.3ms，产生了 3× 的加速。在预填充阶段，M100 在 0.84ms 内完成推理，而 Thor-U 为 1.74ms，实现了 2.1× 的加速。虽然这里仅展示了大语言模型组件的性能，但这些结果突出了 M100 在支持更先进的自动驾驶工作负载方面的优势。

## VII 结论

我们介绍了 M100 SoC 及其 NPU——理想汽车针对通用 AI 推理工作负载的解决方案——其构建于数据流架构之上，通过让编译器和运行时软件来编排处理元件间的计算与数据移动，从而降低了设计复杂度。我们详细阐述了关键功能模块的架构，并解释了主要设计决策背后的理论依据。对比评估结果表明，M100 NPU 在性能上显著超越主流 GPGPU 平台，同时并未牺牲灵活性。我们相信，通过在软件与硬件设计复杂度之间取得有效平衡，经典的数据流架构能够重焕生机，满足现代 AI 计算快速演进的需求。

[^1]: D. Abts, G. Kimmell, A. Ling, J. Kim, M. Boyd, A. Bitar, S. Parmar, I. Ahmed, R. DiCecco, D. Han, J. Thompson, M. Bye, J. Hwang, J. Fowers, P. Lillian, A. Murthy, E. Mehtabuddin, C. Tekur, T. Sohmers, K. Kang, S. Maresh, and J. Ross (2022) A software-defined tensor streaming multiprocessor for large-scale machine learning. In Proceedings of the 49th Annual International Symposium on Computer Architecture, ISCA '22, New York, NY, USA, pp. 567–580. External Links: ISBN 9781450386104, [Link](https://doi.org/10.1145/3470496.3527405), [Document](https://dx.doi.org/10.1145/3470496.3527405) Cited by: §I.

[^2]: D. Abts, J. Ross, J. Sparling, M. Wong-VanHaren, M. Baker, T. Hawkins, A. Bell, J. Thompson, T. Kahsai, G. Kimmell, J. Hwang, R. Leslie-Hurd, M. Bye, E.R. Creswick, M. Boyd, M. Venigalla, E. Laforge, J. Purdy, P. Kamath, D. Maheshwari, M. Beidler, G. Rosseel, O. Ahmad, G. Gagarin, R. Czekalski, A. Rane, S. Parmar, J. Werner, J. Sproch, A. Macias, and B. Kurtz (2020) Think fast: a tensor streaming processor (tsp) for accelerating deep learning workloads. In 2020 ACM/IEEE 47th Annual International Symposium on Computer Architecture (ISCA), Vol., pp. 145–158. External Links: [Document](https://dx.doi.org/10.1109/ISCA45697.2020.00023) Cited by: §I.

[^3]: C. Agatie (2024) Elon musk reveals the first details about hardware 5 autopilot computer and sensors. autoevolution. Note: [https://www.autoevolution.com/news/elon-musk-reveals-the-first-details-about-hardware-5-autopilot-computer-and-sensors-235405.html](https://www.autoevolution.com/news/elon-musk-reveals-the-first-details-about-hardware-5-autopilot-computer-and-sensors-235405.html) Accessed: 2025-08-21 Cited by: §I, §II.

[^4]: P. Bannon, G. Venkataramanan, D. D. Sarma, and E. Talpes (2019) Computer and redundancy solution for the full self-driving computer. In 2019 IEEE Hot Chips 31 Symposium (HCS), Vol., pp. 1–22. External Links: [Document](https://dx.doi.org/10.1109/HOTCHIPS.2019.8875645) Cited by: §I, §II.

[^5]: V. Baumgarte, G. Ehlers, F. May, A. Nückel, M. Vorbach, and M. Weinhardt (2003) PACT xpp—a self-reconfigurable data processing architecture. the Journal of Supercomputing 26 (2), pp. 167–184. Cited by: §I.

[^6]: K. Black, N. Brown, D. Driess, A. Esmail, M. Equi, C. Finn, N. Fusai, L. Groom, K. Hausman, B. Ichter, et al. (2024) $\pi_{0}$: A vision-language-action flow model for general robot control. arXiv preprint arXiv:2410.24164. Cited by: §I.

[^7]: A. Brohan, N. Brown, J. Carbajal, Y. Chebotar, J. Dabis, C. Finn, K. Gopalakrishnan, K. Hausman, A. Herzog, J. Hsu, et al. (2022) Rt-1: robotics transformer for real-world control at scale. arXiv preprint arXiv:2212.06817. Cited by: §I.

[^8]: C. Cheang, G. Chen, Y. Jing, T. Kong, H. Li, Y. Li, Y. Liu, H. Wu, J. Xu, Y. Yang, et al. (2024) Gr-2: a generative video-language-action model with web-scale knowledge for robot manipulation. arXiv preprint arXiv:2410.06158. Cited by: §I.

[^9]: J. Coburn, C. Tang, S. A. Asal, N. Agrawal, R. Chinta, H. Dixit, B. Dodds, S. Dwarakapuram, A. Firoozshahian, C. Gao, et al. (2025) Meta's second generation ai chip: model-chip co-design and productionization experiences. In Proceedings of the 52nd Annual International Symposium on Computer Architecture, pp. 1689–1702. Cited by: §I.

[^10]: J. B. Dennis (1980) Data flow supercomputers.. Computer 13 (11), pp. 48–56. Cited by: §I.

[^11]: A. Firoozshahian, J. Coburn, R. Levenstein, R. Nattoji, A. Kamath, O. Wu, G. Grewal, H. Aepala, B. Jakka, B. Dreyer, et al. (2023) Mtia: first generation silicon targeting meta's recommendation systems. In Proceedings of the 50th Annual International Symposium on Computer Architecture, pp. 1–13. Cited by: §I.

[^12]: V. Govindaraju, C. Ho, and K. Sankaralingam (2011) Dynamically specialized datapaths for energy efficient computing. In 2011 IEEE 17th International Symposium on High Performance Computer Architecture, pp. 503–514. Cited by: §I.

[^13]: L. Gwennap (2020) Tenstorrent scales ai performance: architecture leads in data-center power efficiency. Microprocessor Report, Tech. Rep., apr. Cited by: §I.

[^14]: Y. Hu, J. Yang, L. Chen, K. Li, C. Sima, X. Zhu, S. Chai, S. Du, T. Lin, W. Wang, et al. (2023) Planning-oriented autonomous driving. In Proceedings of the IEEE/CVF conference on computer vision and pattern recognition, pp. 17853–17862. Cited by: §VI-B.

[^15]: J. Hwang (2023) Samsung to make tesla's hw 4.0 self-driving auto chip. The Korea Economic Daily. Note: [https://www.kedglobal.com/semiconductors/newsView/ked202109230009](https://www.kedglobal.com/semiconductors/newsView/ked202109230009) Accessed: 2025-08-21 Cited by: §I, §II.

[^16]: Z. Jia, M. Maggioni, B. Staiger, and D. P. Scarpazza (2018) Dissecting the nvidia volta gpu architecture via microbenchmarking. arXiv preprint arXiv:1804.06826. Cited by: §II.

[^17]: N. Jouppi, G. Kurian, S. Li, P. Ma, R. Nagarajan, L. Nai, N. Patil, S. Subramanian, A. Swing, B. Towles, et al. (2023) Tpu v4: an optically reconfigurable supercomputer for machine learning with hardware support for embeddings. In Proceedings of the 50th annual international symposium on computer architecture, pp. 1–14. Cited by: §I.

[^18]: N. P. Jouppi, C. Young, N. Patil, D. Patterson, G. Agrawal, R. Bajwa, S. Bates, S. Bhatia, N. Boden, A. Borchers, et al. (2017) In-datacenter performance analysis of a tensor processing unit. In Proceedings of the 44th annual international symposium on computer architecture, pp. 1–12. Cited by: §I.

[^19]: L. S. Karumbunathan (2022-07) NVIDIA Jetson AGX Orin Series, A Giant Leap Forward for Robotics and Edge AI Applications, Technical Brief. NVIDIA. Note: [https://www.nvidia.com/content/dam/en-zz/Solutions/gtcf21/jetson-orin/nvidia-jetson-agx-orin-technical-brief.pdf](https://www.nvidia.com/content/dam/en-zz/Solutions/gtcf21/jetson-orin/nvidia-jetson-agx-orin-technical-brief.pdf) Accessed: 2025-08-21 Cited by: §I, §II.

[^20]: S. Lie (2023) Cerebras architecture deep dive: first look inside the hardware/software co-design for deep learning. In IEEE Micro, Vol. 43, pp. 18–30. Cited by: §I.

[^21]: S. Lie (2024-08) Wafer-Scale AI: GPU Impossible Performance. In 2024 IEEE Hot Chips 36 Symposium (HCS), Vol., Los Alamitos, CA, USA, pp. 1–71. External Links: ISSN, [Document](https://dx.doi.org/10.1109/HCS61935.2024.10664673), [Link](https://doi.ieeecomputersociety.org/10.1109/HCS61935.2024.10664673) Cited by: §I.

[^22]: O. Moreira, A. Yousefzadeh, F. Chersi, A. Kapoor, R. Zwartenkot, P. Qiao, G. Cinserin, M. A. Khoei, M. Lindwer, and J. Tapson (2020) Neuronflow: a hybrid neuromorphic–dataflow processor architecture for ai workloads. In 2020 2nd IEEE International Conference on Artificial Intelligence Circuits and Systems (AICAS), pp. 01–05. Cited by: §I.

[^23]: W. A. Najjar, E. A. Lee, and G. R. Gao (1999) Advances in the dataflow computational model. Parallel computing 25 (13-14), pp. 1907–1929. Cited by: §I.

[^24]: J. Nickolls and W. J. Dally (2010) The gpu computing era. IEEE micro 30 (2), pp. 56–69. Cited by: §II.

[^25]: NVIDIA Corporation (2025) NVIDIA Jetson Thor. Note: Accessed: 2025-08-21 External Links: [Link](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-thor/) Cited by: §I, §II.

[^26]: N. Perryman, C. Wilson, and A. George (2023) Evaluation of xilinx versal architecture for next-gen edge computing in space. In 2023 IEEE aerospace conference, pp. 1–11. Cited by: §I.

[^27]: R. Prabhakar and S. Jairath (2021) SambaNova sn10 rdu: accelerating software 2.0 with dataflow. In 2021 IEEE Hot Chips 33 Symposium (HCS), pp. 1–37. Cited by: §I.

[^28]: R. Prabhakar, R. Sivaramakrishnan, D. Gandhi, Y. Du, M. Wang, X. Song, K. Zhang, T. Gao, A. Wang, X. Li, et al. (2024) Sambanova sn40l: scaling the ai memory wall with dataflow and composition of experts. In 2024 57th IEEE/ACM International Symposium on Microarchitecture (MICRO), pp. 1353–1366. Cited by: §I.

[^29]: R. Prabhakar, Y. Zhang, D. Koeplinger, M. Feldman, T. Zhao, S. Hadjis, A. Pedram, C. Kozyrakis, and K. Olukotun (2017) Plasticine: a reconfigurable architecture for parallel paterns. ACM SIGARCH Computer Architecture News 45 (2), pp. 389–402. Cited by: §I.

[^30]: A. Rico, S. Pareek, J. Cabezas, D. Clarke, B. Ozgul, F. Barat, Y. Fu, S. Münz, D. Stuart, P. Schlangen, et al. (2024) Amd xdna™ npu in ryzen™ ai processors. IEEE Micro. Cited by: §I.

[^31]: H. Singh, M. Lee, G. Lu, F. J. Kurdahi, N. Bagherzadeh, and E. M. Chaves Filho (2000) MorphoSys: an integrated reconfigurable system for data-parallel and computation-intensive applications. IEEE transactions on computers 49 (5), pp. 465–481. Cited by: §I.

[^32]: J. Suettlerlein, S. Zuckerman, and G. R. Gao (2013) An implementation of the codelet model. In European Conference on Parallel Processing, pp. 633–644. Cited by: §III-A.

[^33]: E. Talpes, D. D. Sarma, D. Williams, S. Arora, T. Kunjan, B. Floering, A. Jalote, C. Hsiong, C. Poorna, V. Samant, et al. (2023) The microarchitecture of dojo, tesla's exa-scale computer. IEEE Micro 43 (3), pp. 31–39. Cited by: §I.

[^34]: K. B. Theobald (1999) EARTH: an efficient architecture for running threads. thesis. Cited by: §III-A.

[^35]: H. Touvron, L. Martin, K. Stone, P. Albert, A. Almahairi, Y. Babaei, N. Bashlykov, S. Batra, P. Bhargava, S. Bhosale, et al. (2023) Llama 2: open foundation and fine-tuned chat models. arXiv preprint arXiv:2307.09288. Cited by: §VI-B.

[^36]: J. Vasiljevic and D. Capalija (2024) Blackhole & tt-metalium: the standalone ai computer and its programming model. In 2024 IEEE Hot Chips 36 Symposium (HCS), pp. 1–30. Cited by: §I.

[^37]: H. Wu, Y. Jing, C. Cheang, G. Chen, J. Xu, X. Li, M. Liu, H. Li, and T. Kong (2023) Unleashing large-scale video generative pre-training for visual robot manipulation. arXiv preprint arXiv:2312.13139. Cited by: §I.

[^38]: B. Zitkovich, T. Yu, S. Xu, P. Xu, T. Xiao, F. Xia, J. Wu, P. Wohlhart, S. Welker, A. Wahid, et al. (2023) Rt-2: vision-language-action models transfer web knowledge to robotic control. In Conference on Robot Learning, pp. 2165–2183. Cited by: §I.
