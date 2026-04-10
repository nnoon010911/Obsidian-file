---
title: Ao Wang 2024 YOLOv10 端到端目标检测
type: 论文
tags:
  - 论文
  - 目标检测
  - YOLO
authors: Ao Wang et al.
year: 2024
source: https://arxiv.org/abs/2405.14458
status: 在读
date: 2026-04-10T23:10:00+08:00
modify: 2026-04-10T23:10:00+08:00
share: false
cdate: 2026-04-10
mdate: 2026-04-10
---

# Ao Wang 2024 YOLOv10 端到端目标检测

## 研究问题
- 传统 YOLO 推理阶段依赖 NMS，增加后处理延迟，也让真正的端到端部署受到限制。
- 现有 YOLO 结构中还存在不少计算冗余，性能和效率之间还有提升空间。

## 核心方法
- 提出一致性双分配机制，用于 NMS-free 训练。
- 从效率和精度两方面重新审视 YOLO 的各个组件，做整体式设计，而不是只改某一个模块。

## 关键结果
- 论文报告 YOLOv10 在多种尺度上取得了较好的性能效率平衡。
- 论文摘要给出的代表性结果包括：
  - YOLOv10-S 在相近 AP 下比 RT-DETR-R18 更快。
  - YOLOv10-B 在同等性能下，相比 YOLOv9-C 延迟更低、参数更少。

## 我的判断
- 对 YOLO11 改进来说，这篇论文的价值不在“直接复现 YOLOv10”，而在两个思路：
  - 后处理路径是否还能继续压缩，特别是部署端延迟。
  - 改进不能只盯单个注意力模块，要从 backbone、neck、head、label assignment、loss、推理链路一起看。

## 可复用知识
- [[YOLO11 改进方向]]

## 关联项目
- [[YOLO11 改进主线]]

## 引用摘录
- 来源：arXiv 摘要 `https://arxiv.org/abs/2405.14458`
- 关键词：NMS-free、dual assignments、efficiency-accuracy co-design
