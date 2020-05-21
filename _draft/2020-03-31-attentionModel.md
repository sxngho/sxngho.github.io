---
layout: post
title:  " attention에 대해 알아본다"
subtitle:  " 성능개선 모델 중 attention을 사용한 모델에 대해 알아본다. "
categories: back-end
tags: server
comments: true
---

**1.SE block** : convolution을 통해 생성된 특성을 채널당 중요도를 고려해서 재보정

 ![image](https://user-images.githubusercontent.com/30399521/77928386-4d679c80-72e3-11ea-9e9c-96af088d34d4.png)   

\- Squeeze와 Excitation을 통해 channel 별로 attention 수행

  (1) 스퀴즈(squeeze) 작업 

   : C개 채널의 2차원(HxW)의 특성맵들을 1x1 사이즈의 특성맵으로 변환하는 작업

​    global average pooling(GAP)을 통해 각 2차원의 특성맵을 평균내어 하나의 값 도출

​    convolution이 local 정보를 다룬다면, squeeze는 global 정보를 다루게 됨

​    

  (2) 활성화(excitation)작업 : 두 개의 FC층을 더해줘서 각 채널의 상대적 중요도 파악

 ![image](https://user-images.githubusercontent.com/30399521/77928399-50628d00-72e3-11ea-8e8c-fc70f7e2a8cd.png) 

​    1) 스퀴즈를 통해 얻은 z를 인풋으로 삼아서  W1  가중치들과 fully-connected하게 곱함

​    2) 결과값을 즉 ReLU 함수(δ)로 활성화해준 후에  W2  가중치들과 fully-connected하게 곱함

​    3) 시그모이드 함수(σ)로 활성화해주어 0과 1사이의 값을 가지게함

​    => 따라서 각 채널의 상대적 중요도를 0과 1의 값으로 파악할 수 있게 됨

​    

 ![image](https://user-images.githubusercontent.com/30399521/77928406-5193ba00-72e3-11ea-87c4-e2d516d935af.png)   

결과적으로 특성맵은 X에서 convolution을 통해 U로, U에서 SE block을 통해 X~ 로 변환

  

  (3) 성능 분석

 ![image](https://user-images.githubusercontent.com/30399521/77928409-535d7d80-72e3-11ea-9b2e-781b15e43a1a.png)   

 **2. CBAM**: SE Block의 channel 정보에 더해 spatial 정보까지 추가적으로 고려한 모듈인 

   BAM의 후속연구로 input – channel – spatial – refined의 순차적용방식을 채택한 모듈

 ![image](https://user-images.githubusercontent.com/30399521/77928414-55274100-72e3-11ea-8917-31c49a10cce7.png)   

   

 (1) Channel Attention Module

   1) channel은 GAP와 GMP를 통해 각 채널의 global context를 수집

   2) 2-layer MLP를 통과하여 입력 채널과 같은 사이즈로 출력

​    

  ![image](https://user-images.githubusercontent.com/30399521/77928416-55bfd780-72e3-11ea-8aee-cbf5b70b54d0.png) 

 (2) Spatial Attention Module

   1) spatial은 채널이 갖는 의미를 유지하기 위해서 convolution만으로 

​      최종 2D attention을 계산

   ![image](https://user-images.githubusercontent.com/30399521/77928421-58223180-72e3-11ea-9a83-974498681643.png) 

   

 (3) 성능 분석

​     ![image](https://user-images.githubusercontent.com/30399521/77928428-59535e80-72e3-11ea-9ad2-0dea64d97212.png) 

매우 적은 overhead로 유의미한 성능 향상

​    
