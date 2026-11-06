---
title: "UCF-Crime-DVS: 이벤트 카메라로 만든 이상 행동 탐지 데이터셋"
date: 2026-11-06
weight: 6
draft: false
description: "제 연구의 출발점인 UCF-Crime-DVS 논문을 읽고 데이터셋 구성과 SNN 기반 모델 MSF를 정리한 공부 기록"
tags: ["UCF-Crime-DVS", "Event Camera", "DVS", "SNN", "Video Anomaly Detection"]
categories: ["Studies"]
toc:
  enable: true
---

학부연구생으로 연구를 시작하면서, 연구에 사용하는 데이터셋과 기준 모델을 이해하기 위해 UCF-Crime-DVS 논문을 읽고 정리한 기록입니다.

<!--more-->

## 공부한 시기와 논문

- 시기: 2026년 8월 말
- 논문: Yuanbin Qian, Shuhan Ye et al., "UCF-Crime-DVS: A Novel Event-Based Dataset for Video Anomaly Detection with Spiking Neural Networks," AAAI 2025.
- 논문 보기: [arXiv 페이지](https://arxiv.org/abs/2503.12905) · [PDF](https://arxiv.org/pdf/2503.12905)
- 공식 데이터셋과 코드: [GitHub (YBQian-Roy/UCF-Crime-DVS)](https://github.com/YBQian-Roy/UCF-Crime-DVS)

## 왜 이벤트 카메라인가

기존 영상 이상 행동 탐지는 RGB 영상에서 외형 특징이나 광학 흐름(optical flow) 특징을 뽑아 사용했습니다.
이벤트 카메라(DVS)는 사람 망막의 주변부 신경처럼, 픽셀의 밝기 변화가 기준값을 넘을 때만 신호를 만듭니다.
그래서 움직이는 대상은 잘 잡고 정지한 배경은 거의 기록하지 않으며, 시간 해상도가 높고 데이터 중복이 적습니다.
논문은 이런 장점에도 불구하고 이벤트 카메라가 아직 이상 행동 탐지에는 쓰이지 않았다는 점에서 출발합니다.

## 데이터셋을 만든 방법

이벤트 카메라로 범죄 현장을 새로 찍는 대신, 기존 UCF-Crime 영상을 이벤트 형식으로 다시 촬영했습니다.

- 빛이 없는 공간에서 4K 모니터로 UCF-Crime 영상을 재생하고, 1280×720 해상도의 이벤트 카메라로 모니터 화면만 촬영했습니다.
- 클래스별로 영상을 이어 붙여 재생한 뒤, 기록해 둔 프레임 수에 맞춰 긴 이벤트 스트림을 원래 영상 단위로 다시 잘랐습니다.
- 이벤트는 다루기 어려운 형태라서, 원본 16프레임에 해당하는 시간마다 이벤트를 모아 하나의 이벤트 프레임으로 만들었습니다.

그 결과 13개 이상 행동 클래스에 걸친 1,900개의 이벤트 스트림이 만들어졌습니다.
학습용 1,610개에는 영상 단위 라벨만, 테스트용 290개에는 프레임 단위 라벨이 있고, 영상 하나의 평균 길이는 약 242초로 다른 DVS 데이터셋보다 훨씬 길고 해상도도 높습니다.

## 모델: MSF

이벤트 데이터는 0과 1의 신호로 이루어져 있어, 같은 형태의 스파이크 신호로 계산하는 스파이킹 신경망(SNN)과 잘 맞습니다.
논문은 이를 바탕으로 SNN 기반 모델 MSF(Multi-Scale Spiking Fusion)를 제안합니다.

- 특징 추출: 대규모 이벤트 행동 인식 데이터셋으로 사전학습한 Spikingformer로 각 구간의 특징을 뽑습니다.
- 지역 특징: 간격이 다른 팽창 합성곱(dilated convolution)으로 여러 시간 범위의 패턴을 봅니다.
- 전역 특징: SpikingGCN으로 멀리 떨어진 구간 사이의 관계를 특징 유사도와 시간 거리로 학습합니다.
- 시간 상호작용: TIM(Temporal Interaction Module)으로 이전 시점의 정보를 다음 시점에 이어 줍니다.
- 학습: 영상 단위 라벨만 있으므로 MIL(Multiple Instance Learning) 방식으로 이상 점수를 학습합니다.

## 정리하며

이 논문의 공개 데이터셋과 코드가 바로 제 연구의 출발점이었습니다.
논문을 읽고 나서야 연구 코드 속 복잡한 모듈들이 각각 어떤 역할로 들어갔는지 이해할 수 있었고, 이후 연구에서 이 모듈들을 걷어 낸 단순한 기준 모델을 만들 때도 무엇을 빼고 무엇을 남길지 판단하는 근거가 되었습니다.
연구 과정은 [이벤트 카메라 영상 기반 이상 행동 탐지 연구]({{< relref "/posts/dvs-anomaly-detection" >}}) 글에 정리해 두었습니다.
