---
title: "Swin Transformer와 ConvNeXt"
date: 2026-11-06
weight: 9
draft: false
description: "ViT의 한계를 푼 Swin Transformer와, Transformer 시대에 CNN을 다시 설계한 ConvNeXt를 함께 정리한 공부 기록"
tags: ["Swin Transformer", "ConvNeXt", "ViT", "CNN"]
categories: ["Studies"]
toc:
  enable: true
---

CNN과 ViT의 차이를 공부한 뒤, 두 구조가 서로의 장점을 어떻게 가져왔는지 보기 위해 두 논문을 함께 읽고 발표한 기록입니다.

<!--more-->

## 공부한 시기와 범위

- 시기: 2026년 5월 말
- 논문
  - Ze Liu et al., "Swin Transformer: Hierarchical Vision Transformer using Shifted Windows," ICCV 2021. ([arXiv:2103.14030](https://arxiv.org/abs/2103.14030))
  - Zhuang Liu et al., "A ConvNet for the 2020s," CVPR 2022. ([arXiv:2201.03545](https://arxiv.org/abs/2201.03545))
- 발표 자료: [Swin Transformer & ConvNeXt (PDF, 약 4.7MB)](swin-transformer-convnext-slides.pdf)

## Swin Transformer

### 기존 ViT의 문제

ViT는 이미지를 고정된 크기의 패치로 잘라 처리하는데, 실제 이미지 속 물체는 크기가 제각각이라 객체 탐지 같은 작업에 한계가 있었습니다.
또 모든 패치끼리 attention을 계산하기 때문에 이미지가 커질수록 계산량이 제곱으로 늘어, 고해상도가 필요한 분할 작업에 쓰기 어려웠습니다.

### 핵심 아이디어

- Window attention: 이미지 전체가 아니라 겹치지 않는 작은 창(window) 안에서만 attention을 계산해 계산량을 줄입니다.
- Shifted window: 창 안에서만 보면 이웃한 창끼리 정보를 주고받지 못하기 때문에, 다음 층에서는 창의 위치를 절반만큼 옮겨 창 경계를 넘나드는 연결을 만듭니다. 옮기고 남는 부분은 반대편으로 순환시켜 붙이고, 실제로는 이웃이 아닌 부분은 mask로 가립니다.
- 계층 구조: 이웃한 2×2 토큰을 하나로 합치는 Patch Merging으로 단계마다 해상도는 줄이고 채널은 늘려, CNN처럼 여러 크기의 특징을 얻습니다.
- Relative position bias: 창 안에서 두 패치가 얼마나 떨어져 있는지를 attention 점수에 반영해 공간 관계를 더 잘 학습합니다.

논문의 비교 실험에서도 shifted window를 쓰고 순환 방식으로 구현했을 때 분류, 탐지, 분할 모두에서 가장 좋은 성능을 보였습니다.

## ConvNeXt

### 제안 배경

Transformer가 강세인 상황에서, "CNN도 현대적인 방식으로 다시 설계하면 경쟁력이 있지 않을까?"라는 질문에서 출발한 논문입니다.
ResNet-50을 출발점으로 삼아 Transformer, 특히 Swin Transformer의 설계 요소를 하나씩 순수 CNN에 옮겨 보며 성능 변화를 확인합니다.

### ResNet을 현대화한 과정

- 학습 방법: 구조를 바꾸기 전에, ViT를 학습할 때 쓰는 최신 학습 기법만 적용해도 ResNet-50의 성능이 올라갔습니다.
- 큰 구조: 단계별 블록 수 비율을 Swin과 맞추고, 처음부터 이미지를 4×4 패치 단위로 나누는 patchify stem을 사용합니다.
- 블록 구조: 공간 정보를 섞는 depthwise convolution과 채널 정보를 섞는 1×1 convolution으로 나누어, 각각 self-attention과 MLP의 역할을 맡깁니다. 여기에 Transformer와 같은 inverted bottleneck 구조와 7×7의 큰 커널을 적용합니다.
- 세부 설계: ReLU 대신 GELU, BatchNorm 대신 LayerNorm을 쓰고 활성화 함수와 정규화 층의 수를 줄이며, 단계 사이에 별도의 downsampling 층을 둡니다.

그 결과 ConvNeXt는 ImageNet 분류, COCO 객체 탐지, ADE20K 분할에서 Swin Transformer와 비슷하거나 더 나은 성능을 냈습니다.

## 정리하며

두 논문을 나란히 읽으면서, Swin은 Transformer에 CNN의 지역성과 계층 구조를 들여왔고 ConvNeXt는 CNN에 Transformer의 설계를 들여왔다는 점이 흥미로웠습니다.
결국 성능을 결정하는 것은 "CNN이냐 Transformer냐"라는 이름보다, 어떤 설계 요소를 어떻게 조합하느냐에 가깝다는 것을 배웠습니다.
