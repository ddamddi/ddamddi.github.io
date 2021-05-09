---
layout: post
title: "Object Detection & Instance Segmentation 평가 Metric 알아보기"
description: ""
modified: 2021-05-09
categories: mldl
tags: cv
comments: true
---

> 이 글은  Object Detection과 Instance Segmentation Task Metric을 정리한 글 입니다.

<br>

# Intro
오늘은 Object Detection과 Instance Segmentation의 Metric을 알아보도록 하겠습니다. 두 Task의 Metric으로는 mAP를 주로 사용합니다. mAP에 대해 이해하기 위해서는 Semantic Segmentation Metric 포스트 에서 언급했던 IoU와 Precision과 Recall에 대한 이해가 필요합니다.

이번 포스트에서는 Precision과 Recall에 대한 설명만 하도록 하겠습니다. IoU에 대한 설명은 바로 이전 [Semantic Segmentation Metric 포스트](https://ddamddi.github.io/mldl/2021/04/12/semantic_segmentation_metric/)를 참고해 주세요.

<br>

# Precision & Recall
![confusion_matrix](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/confusion_matrix.png)  

- Precision은 Positive(긍정)으로 예측을 했을때, 실제로 Positive인 것의 비율을 나타내고 식으로 나타내면 다음과 같이 나타낼 수 있습니다.

$$ Precision = \frac{TP}{TP+FP}  = \frac{TP}{\# pred} $$

- Recall은 실제로 Positive(긍정)인 것에 대해서, 예측이 Positive인 것의 비율을 나타내고 식은 다음과 같습니다.

$$ Recall = \frac{TP}{TP+FN} = \frac{TP}{\# gt} $$

<br><br>

위의 IoU와 Precsion과 Recall을 바탕으로 AP와 mAP(mean Average Precision)에 대해 알아보도록 하겠습니다.

# mAP (mean Average Precision)
mAP를 얻기 위해서는 각 클래스별 AP를 먼저 구해야합니다. AP는 Average Precision의 약어로써 Precision-Recall Curve (PR Curve)의 아래 영역의 넓이로 정의됩니다. 

<br>

특정 클래스에 대해서 PR-Curve를 통해 AP를 구하는 방법을 알아보도록 하겠습니다. 다음과 같이 GT bbox와 predicted bbox이 있다고 해봅시다.
![map-bboxes](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/map_bboxes.png)

가장 먼저 Confidence Score로 기준으로 내림차순으로 정렬합니다. IoU Threshold (여기서는 0.5)를 기준으로 이상이면 올바르게 예측한 것으로 보아 True-Positive가 되고 임계값 이하이면 False Positive가 됩니다. *P3*와 *P4*와 같이 같은 GT를 예측하는 여러개의 prediction이 있을 경우,  IoU가 가장 높은 prediction만 TP로 간주하고 나머지는 FP로 간주하게 됩니다. (아래 참고)

![map-table](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/map_table.png)

위 테이블과 같이 TP/FP로 구분한 뒤,  누적 값인 Acc(Accumulated) TP / Acc FP을 통해 Precision과 Recall을 구하고 이렇게 얻은 값을 Precison/Recall 값을 통해서 PR-Curve를 그리고 그려진 PR-Curve의 아래 영역의 넓이로 AP가 얻어집니다.
![InterpolateAP](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/interpolateAP.jpeg)

각 점에서 오른쪽의 Max Precision을 취해서 AP를 구하게 됩니다. 위의 그림에서 주황색 그래프의 넓이가 아닌 녹색 그래프의 넓이가 AP가 됩니다. 이렇게 얻은 AP는 한 클래스에 해당하는 값이고 모든 클래스에 대해 구한 값을 평균내어 mAP를 얻어냅니다.

<br><br>

MS COCO, PASCAL VOC 등 데이터셋 마다 평가 metric이 서로 다릅니다. 그 중에서도 오늘은 MS COCO metric에 대해서만 알아보도록 하겠습니다. 

# MS COCO
![ms-coco-metrics](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/ms-coco-metrics.png)
MS COCO 데이터에서는 PR-Curve을 총 101개의 Recall 임계값[0: 1.0: 0.01] 을 사용하고 중간 값은 Interpolation으로 채우게 되며 평가지표로는  AP와 AP50을 주로 사용합니다.

- **AP**
: [.5: .95: .05]의 총 11개의 IoU에서의 mAP의 평균으로 정의되고 MS COCO dataset에서 최우선적으로 사용하는 평가지표입니다. 각 IoU Threshold에서 mAP가 모든 클래스별 AP의 평균이라고 볼 수 있으므로 MS COCO AP는 클래스별 AP의 평균의 평균이라고 볼 수 있습니다. 따라서 Precision 평균의 평균의 평균이라고 볼 수 있겠습니다.

- **AP50**
: IoU Treshold가 0.5일 때의 AP입니다. (IoU가 0.5이상이면 True-Positive로 판단)


# Instance Segmentation
 Object Detection 같은 경우 IoU가 bbox와 bbox가 매칭되어 계산이 되었다면 Instance Segmentation에서는 Pixel to Pixel로 똑같이 계산된다고 생각하면 됩니다. 

# References
[Harshit Kumar's Techincal Fridays Blog](https://kharshit.github.io/blog/2019/09/20/evaluation-metrics-for-object-detection-and-segmentation)



