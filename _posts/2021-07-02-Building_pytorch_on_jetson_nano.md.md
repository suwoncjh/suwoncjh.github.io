---
title: Jetson nano에서 pytorch build하기
author: Jeonghwan Choi
date: 2021-07-02 14:10:00 +0800
categories: [Embedded, Jetson]
tags: [jetson]
---

Jetson nano에서 GPU활용없이 CPU만 사용해서 neural network 모델을 inference를 해야하는 일이 생겼다. 
개발해야하는 application은 32ms 길이의 오디오 신호를 16ms 이내에 처리해야 하는데 모델의 inference time이 16ms가 넘는 문제가 있었다.   
Neural network 모델의 inference time을 줄이는 가장 간단한 방법으로 model compression의 일종인 quantization을 먼저 적용해 보았다.  

시도한 방법 및 문제점:
- PC에서 python으로 quantization 한 model을 저장
- Jetson nano에서 C++ 에서 libtorch로 불러오려는데 모델 로드가 안됨
- quantiazation하지 않은 모델은 로드 됨  

원인: 
- 빌드되어 배포되는 jetson 용 pytorch에는 ARM프로세서 ([Jetson nano](https://developer.nvidia.com/embedded/jetson-nano)는 ARM cortex A57 CPU가 탑재되어있음)를 위한 quantization backend인 qnnpack이 없었음 

해결방안:
- pytorch를 qnnpack 옵션을 주어 다시 source부터 build
- [1]을 참고했으며 빌드 옵션줄 때 qnnpack off 를 on으로 변경

Reference
[1] https://qengineering.eu/install-pytorch-on-jetson-nano.html
