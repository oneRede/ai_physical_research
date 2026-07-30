# works/ — 作品输出

可展示的成果：技术论文翻译、机器人系统分析、技术综述等。

## 文件约定

- 每个作品一个独立文件或子目录
- 作品应该是**可独立理解的**，不依赖仓库其他部分的上下文
- 适合放到技术博客、GitHub、个人知识库中展示

## 已有作品

### 翻译

**元信息头约定**：每篇 `*-translation.md` 以 YAML frontmatter 开头，必备字段：

```yaml
title:             # 中文标题
sourceTitle:       # 原文标题
sourceUrl:         # 原文链接
sourceAuthor:      # 原作者（可含所属机构）
sourcePublishedAt: # 原文日期（未知可为 null）
translationMethod: # 翻译方式
language: "zh-CN"
sourceFigureCount: # 原文插图数（数字；null = 原文不可得、未审计）
pipelineRunId:     # 策展批次 ID；正式收录必须存在
pipelineSource:    # 对应 translate/<batch>/works-ready/<file>；存量手工条目写 legacy/...
```

**插图与外链约定**：

- 新收录译文的原文插图应下载到 `works/imgs/<slug>/`，以本地相对路径嵌入
- 译文正文保留原文中的超链接，不得在翻译时丢弃
- `scripts/check-consistency.sh` C10 会校验 `sourceFigureCount` 与正文嵌图数

| 文件 | 原文 | 来源 | 领域 |
|------|------|------|------|
| [nvidia-isaac-groot-reference-robot-translation.md](nvidia-isaac-groot-reference-robot-translation.md) | [NVIDIA 发布 Isaac GR00T 人形机器人参考设计](https://nvidianews.nvidia.com/news/nvidia-open-humanoid-robot-reference-design) | NVIDIA | 机器人硬件平台 |
| [end-to-end-driving-survey-translation.md](end-to-end-driving-survey-translation.md) | [端到端自动驾驶综述](https://arxiv.org/html/2603.16050v1) | arXiv | 自动驾驶 |
| [gemini-robotics-er-1-6-translation.md](gemini-robotics-er-1-6-translation.md) | [Gemini Robotics ER 1.6](https://deepmind.google/blog/gemini-robotics-er-1-6/) | Google DeepMind | 具身推理 |
| [pndbotics-adam-waic-2026-translation.md](pndbotics-adam-waic-2026-translation.md) | [PNDbotics Adam 人形机器人](https://embodiedglobal.com/en/article/pndbotics-adam-humanoid-stair-climbing-waic-2026) | Embodied Global | 人形机器人 |
| [robot-factored-world-models-translation.md](robot-factored-world-models-translation.md) | [机器人因子化世界模型](https://arxiv.org/abs/2607.22535) | arXiv | 世界模型 |
| [embodied-gpt-5.1-world-model-translation.md](embodied-gpt-5.1-world-model-translation.md) | [具身 GPT-5.1：世界模型的证据？](https://arxiv.org/abs/2607.23899) | arXiv | 大语言模型具身控制 |

## 作品方向参考

- 机器人技术论文翻译
- 自动驾驶系统架构解析
- 具身智能基础研究分析
- 物理仿真与 sim-to-real 方法综述
- 视觉-语言-动作模型研究
- AI for Physics 应用案例

## 下一步

作品发出后，把外部读者的反馈（评论、转发、质疑）回流到 [feedback/](../feedback/)；
新出现的洞见、被挑战的论点回到 [thinking/](../thinking/) 继续打磨。
