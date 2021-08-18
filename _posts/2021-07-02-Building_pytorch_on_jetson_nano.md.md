---
title: Jetson nano에서 pytorch build하기
author: Jeonghwan Choi
date: 2021-07-02 14:10:00 +0800
categories: [Embedded, Jetson]
tags: [jetson]
---


Jetson nano에서 cpu만으로 neural network model을 inference를 해야하는 상황이 발생

개발하는 application은 모델이 실시간으로 결과를 출력해주어야 하는데 inference time이 너무 오래걸렸음 

해결하기위한 가장 간단한 방법으로 model compression의 일종인 quantization을 먼저 적용

시도한 방법:
1. PC에서 python으로 quantization 한 model을 저장
2. Jetson nano에서 C++ 에서 libtorch로 불러오려는데 모델 로드가 안됨
3. quantiazation하지 않은 모델은 로드 됨  

원인: 
- 빌드되어 배포되는 jetson 용 pytorch에는 ARM프로세서를 위한 quantization backend인 qnnpack이 없었음

해결방안:
- pytorch를 qnnpack 옵션을 주어 다시 source부터 build
- [1]을 참고했으나 qnnpack off 를 on으로 변경 

Reference
[1] https://qengineering.eu/install-pytorch-on-jetson-nano.html