---
layout: post
title:  " CNN의 유래"
subtitle:  " CNN이 나오게된 배경을 알아보자 "
categories: back-end
tags: server
comments: true
---

1. 일반적인 인공신경망

 ![image](https://user-images.githubusercontent.com/30399521/77925228-94539300-72df-11ea-9605-cb0fd02820b4.png) 

  (1) 구조

   : 일반적인 인공신경망은 그림과 같이 affine으로 명시된 fully-connected 연산과

​     ReLU와 같은 비선형 활성 함수(nonlinear activation function)의 합성으로 정의된 

​     계층을 여러 층 쌓은 구조

​    

  (2) 문제점

​    1) 인접 픽셀 간의 상관관계가 무시됨

 ![image](https://user-images.githubusercontent.com/30399521/77925251-9a497400-72df-11ea-8dc5-7e5bac625446.png) 

​    

: 일반적인 인공신경망에서의 vectorization은 이미지를 1차원 배열로 바꾼뒤 학습 시키는 

방식으로 이미지 데이터는 일반적으로 인접한 픽셀간의 상관관계가 매우 높기 때문에 

이미지를 벡터화하는 과정에서 정보 손실이 발생

​    

​    2) 막대한 양의 parameter

​     : 1024X1024 크기의 컬러 이미지를 처리하고자 한다면, FNN에 입력되는 벡터의 차원은 약         315만(=1024X2 X 3)까지 증가하게됨

​       300만 차원의 입력을 처리하기 위해서는 필연적으로 막대한 양의 뉴런이 필요하고, 

​       이에 따라 인공신경망 전체의 model parameter는 기하급수적으로 증가 



\2. CNN(Convolution Neural Network)

 ![image](https://user-images.githubusercontent.com/30399521/77925264-9f0e2800-72df-11ea-9ab3-52f1d0457c7c.png) 

 (1) 구조

 : 합성곱 계층 (convolutional layer)과 풀링 계층 (pooling layer)이라고 하는 새로운 층을

   fully-connected 계층 이전에 추가함으로써 원본 이미지에 필터링 기법을 적용한 뒤에 필터링된     이미지에 대해 분류 연산이 수행되도록 구성

​    

  1) 합성곱 계층

   : 이미지에 필터링 기법을 적용 후 Activation 함수인 ReLu함수 적용

  2) 풀링 계층

   : 이미지의 국소적인 부분들을 하나의 대표적인 값으로 변환함으로써 이미지의 크기를 

​    줄이는 등의 다양한 기능들을 수행

​    

 (2)기존의 문제점 해결

  1) 인접 픽셀간 정보 유지

 ![image](https://user-images.githubusercontent.com/30399521/77925275-a33a4580-72df-11ea-9c03-a9518ba0f6e7.png) 

   : CNN은 이미지의 형태를 보존하도록 행렬 형태의 데이터(필터)를 입력 받기 때문에 이미지를       벡터화하는 과정에서 발생하는 정보 손실을 방지 가능

  2) 비교적 적은 parameter

   : CNN을 이용한다면 단순히 필터들을 모수화 (parameterization)할 수 있을 정도의 model         parameter만 필요하며, CNN의 마지막 fully-connected layer는 원본 이미지가 아닌 풀링 계       층을 통해 축소된 이미지를 처리하기 때문에 fully-connected layer의 가중치 또한 줄어듬
