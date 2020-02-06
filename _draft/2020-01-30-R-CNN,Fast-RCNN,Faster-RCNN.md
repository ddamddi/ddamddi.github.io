---
layout: post
title: "[Paper] R-CNN / Fast RCNN / Faster RCNN / Mask RCNN 정리"
description: ""
modified: 2020-02-05
categories: study
tags: paper
comments: true
---

### Backgrounds

(Vision Task 사진)

- Classification : Input에 대한 Class 예측
- Semantic Segentation : Input 내의 모든 Pixel단위 Class 예측
- Object Detection : Input의 Class + Localization(물체의 위치) 예측
- Instance Segmentation : 같은 Class의 객체를 Pixel단위로 서로 구별

### Region-based CNN (R-CNN)
(RCNN 사진)

 1. Input 이미지를 넣는다.
 2. [Selective Search]() 알고리즘을 통해 2000개의 영역(Bounding Box)를 추출  *+*  CNN 모델에 넣기위해 227x227 사이즈로 찌그러트림(Warping) 
 3. 2000개의 Warped Bounding Boxes를 CNN 연산을 수행한다.
 4. 각각 Classification을 수행하여 결과를 도출한다.


### Fast R-CNN
(Fast RCNN 사진)

- Selective Search를 통해 제안된 2000개의 Bounding Box마다 CNN+SVM을 사용하여 분류하는 것은 많은 계산을 요구하기 때문에
- 전체 이미지에 대해 CNN 수행
- CNN feature 결과를 사용해 RoI Pooling

### Faster R-CNN
(Faster RCNN 사진)

- Resion Proposal에 사용하는 Selective Search 알고리즘이 느림
- Selective Search 대신에 Resion Proposal Network(RPN) 사용
- Resion Proposal Network(RPN)
  

### Mask R-CNN
(Mask RCNN 사진)

- ~~RoI Pooling~~ -> RoI Align
- Masking을 위한 Branch 추가
- Binary Masking : 클래스 상관없이 마스킹만 수행.


### 참고자료

