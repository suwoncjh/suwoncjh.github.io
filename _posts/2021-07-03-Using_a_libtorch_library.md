---
title: Libtorch 라이브러리 사용 하기
author: Jeonghwan Choi
date: 2021-07-02 14:10:00 +0800
categories: [Pytorch, Libtorch]
tags: [C++, pytorch, libtorch]
---

# Libtorch 라이브러리 사용 하기
최근 Endpoint AI, TinyML등 임베디드 기기에서 neural network 모델을 활용하는 시도가 많이 소개되고있다.  
Neural network 모델을 개발할 때에는 주로 Python 환경에서 Pytorch나 Tensorflow와 같은 패키지를 사용하는데 임베디드 기기에서는 일반적으로 C나 C++을 주로 활용하기 때문에 모델 학습할 때와 inference 할 때 각각 사용되는 언어가 다르다는 문제가 있다.    
Libtorch는 Pytorch의 C++ 버전으로 Pytorch를 이용해 학습한 모델을 C++에서도 laod하여 infernce할 수 있게 해줄 뿐만아니라 Pytorch에서처럼 C++로 layer를 쌓아 새로운 모델 구성 및 학습도 가능하다.(하지만 굳이 C++환경에서 모델을 학습시키는 사람이 있을까 싶으며 주로 inference를 위한 목적으로만 활용될 것 같다.)

# 1. Libtorch 다운로드

Libtorch 라이브러리는 [Pytorch 홈페이지](https://pytorch.org/){:target="_blank"} 메인에서 다운 받을 수 있다. 

![img1](/assets/img/favicons/libtorch.png)

간단하게 용량이 적은 CPU버전을 받았다. 

다운이 완료되면 압축을 풀어주고 폴더에 들어가면 아래와 같은 파일목록을 확인 할 수 있다. 

여기서 lib과 share 폴더들의 경로를 code를 빌드 할 때 사용할 것이다.  

![./2021-07-03-HW/libtorch2.png](/assets/img/favicons/libtorch2.png)

# 2) C++코드 작성 및 빌드

## 2.1 C++sample code 작성

torch library를 사용하는 c++ 예제 코드를 아래와 같이 작성한다. 

간단하게 node가 2개인 linear layer 하나를 선언하고 선언된 layer의 weights값과 bias값을 확인한 후 1을 입력으로 주어 결과 값을 확인하는 코드이다. 

```cpp
#include <torch/torch.h>
#include <iostream>

int main() {
    // 샘플 data
    torch::Tensor input = torch::ones({1, 2});

    // Linear layer 선언
    torch::nn::Linear linear_layer(2, 1);

    // weight 및 bias 값 확인
    std::cout<<"weights"<<std::endl;
    std::cout<<linear_layer->weight<<std::endl;

    std::cout<<"biases"<<std::endl;
    std::cout<<linear_layer->bias<<std::endl;

    // 샘플 data를 linear layer에 통과
    auto output = linear_layer(input);

    // 모델 출력 확인
    std::cout<<"model output:"<<std::endl;
    std::cout<<output<<std::endl;
}
```

C++로 작성된 torch예제들이 잘 정리되어 있는 [github 저장소](https://github.com/prabhuomkar/pytorch-cpp)가 있으니 참고하면 좋을 것 같다.

## 2.2 CMakeLists.txt 작성

build하기 위해 아래와 같이 CMakeLists를 작성해주자. 

이 때 Torch_DIR은 다운 받았던 libtorch폴더의 /share/cmake/Torch로 경로를 설정해주는 것이 중요하다. 

---

```cpp
cmake_minimum_required(VERSION 3.15)
project(libtorch_ex)     # 프로젝트 이름 
set(CMAKE_CXX_STANDARD 14)

# Torch library path 설정
set(Torch_DIR /home/jeonghwan/Downloads/libtorch/share/cmake/Torch)
find_package(Torch REQUIRED)

add_executable(libtorch_ex main.cpp)
target_link_libraries(libtorch_ex "${TORCH_LIBRARIES}")
set_property(TARGET libtorch_ex PROPERTY CXX_STANDARD 14)
```

## 2.3 Build하기

아래의 명령어들을 터미널에서 실행해주자 

이 때 -DCMAKE_PREFIX_PATH에는llibtorch폴더의 /lib로 경로를 설정해주는 것이 중요하다. 

```bash
mkdir build
cd build

cmake -DCMAKE_PREFIX_PATH=/home/jeonghwan/Downloads/libtorch/lib ..
cmake --build . --config Debug -- -j 4

```

빌드 후 생성된 실행파일을 실행하면 아래와 같이 결과를 확인 할 수 있다. 

![./2021-07-03-HW/libtorch3.png](/assets/img/favicons/libtorch3.png)
