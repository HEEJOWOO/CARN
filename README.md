# CARN : https://arxiv.org/abs/1803.08664 

#### <I studied while referring to various sites, but it is not enough. Thanks anytime for feedback.>
### <heejo5@naver.com>

Fast, Accurate, and Lightweight Super-Resolution with Cascading Residual Network
--------------------------------------------------------------------------

* 성공적인 딥러닝 기반 SR, 그러나 많은 계산량 때문에 실시간 어플리케이션 적용 할 수 없음
* 빠르고 정확하며 경량화된 종속 잔차 네트워크 제안, 더 적은 파라미터로 좋은 성능을 만들어냄

Introduction
------------
  * 실시간성에 맞게 SR을 하기 위해서 방법으로 파라미터의 수를 줄이는 방법이 있음 
  * 파라미터를 줄이는 효과적인 대표적 방법으로 “Recursive”방법이 있음(Ex, DRCN, DRRN)
  * 재귀 단점 1) CNN 모델에 넣기 전 입력 영상을 먼저 업 샘플링 함 
  * 재귀 단점 2) 재귀 네트워크의 손실을 막기 위해 깊이나 넓이를 늘림
  * 이러한 점들은 모델을 재구성할 때 영상의 세부사항을 유지할 수는 있지만 작업수와 추론 시간이 늘어남
  * SR은 파라미터의 수를 줄이며 그 안의 작업횟수 또한 줄이는게 필요함
  * CARN, CARN-M(Mobile) 제안

Contribution
------------
  * 종속 모듈을 가지고 있는 CARN제안
  * CARN-M : 잔차 학습과 재귀를 합친 것
  * 적은 수행과 적은 파라미터의 수를 가지는 네트워크

Related Work
------------
  * SRCNN　: 최초의 딥러닝 기반 SR, 층수 대비 많은 수행, 사전 업 샘플링 구조
  * FSRCNN, MDSR : 사후 업 샘플링 구조, 수행의 수를 줄임, 업샘플링 후 충분하지 않은 층 수로 퍼포먼스 하락 
  * VDSR : 잔차 학습 적용 
  * LapSRN : 적은 파라미터로 VDSR보다 더 좋은 성능 만듦
  * DRCN : 재귀 구조 사용, 파라미터의 수 감소 
  * DRRN : DRCN 개선, 하지만 DRCN과 DRRN은 많은 수행이 필요함

![image](https://user-images.githubusercontent.com/61686244/108476768-c6922880-72d5-11eb-8eaf-c345a88fa44b.png)
![image](https://user-images.githubusercontent.com/61686244/108476796-cf82fa00-72d5-11eb-84e8-88ea857016a8.png)


Proposed Method
---------------
  * CARN : SOTA와 비교시 훨씬 더 적은 추론수로 좋은 성능을 만들어냄
  * CARN-M : CARN을 기반으로 하여 만든 모델로 훨씬 더 효과적인 초해상도 모델, 더 적은 파라미터와 더 적은 추론 수를 가지고 있음
  * CARN은 ResNet기반으로 반들어짐, ResNet과의 차이점 : 지역, 전역 종속 모듈 존재의 차이
  * i 번째 잔여 블록 를 공식화 하면 식 1과 같이 나옴 (conv activation, conv activation..)
 
![image](https://user-images.githubusercontent.com/61686244/108476932-f2ada980-72d5-11eb-8d3c-a660ae03ad4e.png)

  * Resnet은 N개의 잔여 블록으로 구성되어 있고, 그중 I번째 잔여블록은 위의 식처럼 표현 할 수 있고 최종 출력은 다음 식 2처럼 표현 할 수 있음 는 최종 ResNet의 출력을 의미 

![image](https://user-images.githubusercontent.com/61686244/108477000-078a3d00-72d6-11eb-9597-b7f37f36cd9d.png)

  * i 번째 cascading block 중 j번째 residual block의 출력은 식 3과 같음 

![image](https://user-images.githubusercontent.com/61686244/108477068-196be000-72d6-11eb-9f6c-5b7b1d2fa715.png)

  * 재귀 횟수는 3으로 고정
  * 지역, 전역 종속 모듈의 장점
  * 1) 여러 레벨의 특징들을 통합 시킬 수 있음
  * 2) 여러 레벨의 종속 연결은 낮은 층에서 높은 층으로 빠르게 정보를 propagation 할 수 있음 
  * 종속 모듈을 사용한 또다른 이유
  * 1) 정보를 여러 갈래로 뿌릴 수 있음
  * 2) 합성 곱을 추가함으로써, 모델은 주어진 입력 정보의 흐름에서 올바른 정보를  선택 할 수있음 
  * 지역 종속 또는 전역 종속 중 하나만 사용하면 효과는 떨어짐 특히, 지역 종속만 사용할 때 성능 하락이 더 됨 
  * 효과적인 종속 잔여 네트워크
  * 연산량을 줄이기위한 노력
  * 1) efficient residual(residual-E) 사용 / depthwise conv 대신 group conv 사용 / 병렬 처리에 유리 
  * 2) 두 개의 group conv와 한 개의 pointwise conv로 구성(되어 있는 residual-E)
  * group conv 는 각 그룹마다 독립적인 필터의 학습을 할 수 있고, 그룹 수를 조정해 독립적인 필터 그룹을 조어할 수 있음, 그룹의 수를 늘리면 파라미터의 수는 줄면서, 성능 향상이 일어나는 경우가 잇지만, 그룹의 수는 Hyper Parameter라는 단점이 있기 때문에 너무 많은 그룹으로 분할하는 것은 오히려 성능이 더 하락 될 수 있음 

![image](https://user-images.githubusercontent.com/61686244/108477216-49b37e80-72d6-11eb-9087-8de654193ffd.png)

  * 파라미터의 수를 줄이기 위한 노력 
  * 재귀블록을 이용하여 파라미터의 수를 줄임 
  * SRDenseNet과의 차이점
  * 1) 전역 종속을 사용
  * 2) 1x1 conv를 이용하여 지역 정보를 모와둠
  * MemNet과의 차이점
  * 1) 지역 특징들을 마지막 1x1 conv에서 융합하는 반면 CARN은 가능한 모든 지역 블록 내 포인트에 융합시킴
  * 2) 효과적인 계산을 위해 네트워크의 마지막에 업 샘플 진행 반면 MemNet은 입력으로 업 샘플된 영상을 사용

Experiment
----------
  * DIv2K, Set5, Set14, BSD100, Urban100이용
  * RGB영상, 패치 : 64x64
  * agumentation, Adam opt, 1e-4

![image](https://user-images.githubusercontent.com/61686244/108477354-75ceff80-72d6-11eb-90ce-4c67f9fb6fc8.png)

  * L1 Loss 사용 
  * L1 Loss의 단점은 수렴 속도인데, 이 점은 ResNet 스타일로 변형 시켜 완화 시켰음
  * 전역 종속 모듈이 지역 종속 모듈보다 성능에 많은 영향을 끼침

![image](https://user-images.githubusercontent.com/61686244/108477436-8da68380-72d6-11eb-877d-4a615e246ab6.png)
![image](https://user-images.githubusercontent.com/61686244/108477449-926b3780-72d6-11eb-8f46-882bcc907ba4.png)

  * 정보는 전역 종속을 이용해 전파되고, 지역 종속을 이용해 정보를 흘려줌

![image](https://user-images.githubusercontent.com/61686244/108477485-9f882680-72d6-11eb-8107-912c8bffa2c6.png)
![image](https://user-images.githubusercontent.com/61686244/108477503-a4e57100-72d6-11eb-96cd-4db0c2e7f367.png)


