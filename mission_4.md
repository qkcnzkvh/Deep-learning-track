# ResNet 18 성능 개선

1. 학습률 0.001 -> 0.0001로 가중치 세밀함 증가

결과.
지표	Baseline (0.001) -> 변경 실험 (0.0001)
최고 검증 정확도              98.56% -> 98.66%
마지막 검증 정확도             98.56% -> 98.18%
마지막 Train Accuracy       98.61% -> 99.57%
마지막 Val Loss	            0.0508 -> 0.0578

결론.
효과 미미

2. Epoch 5 -> 10 로 학습 반복량 증가

결과.
Epoch [1/10] Train Loss: 0.0140, Train Acc: 0.9940, Val Loss: 0.1214, Val Acc: 0.9684
Epoch [2/10] Train Loss: 0.0159, Train Acc: 0.9935, Val Loss: 0.0500, Val Acc: 0.9847
Epoch [3/10] Train Loss: 0.0038, Train Acc: 0.9990, Val Loss: 0.0834, Val Acc: 0.9741
Epoch [4/10] Train Loss: 0.0015, Train Acc: 0.9995, Val Loss: 0.0367, Val Acc: 0.9895
Epoch [5/10] Train Loss: 0.0005, Train Acc: 1.0000, Val Loss: 0.0475, Val Acc: 0.9885
Epoch [6/10] Train Loss: 0.0069, Train Acc: 0.9969, Val Loss: 0.2386, Val Acc: 0.9569
Epoch [7/10] Train Loss: 0.0131, Train Acc: 0.9959, Val Loss: 0.0756, Val Acc: 0.9780
Epoch [8/10] Train Loss: 0.0098, Train Acc: 0.9962, Val Loss: 0.0457, Val Acc: 0.9875
Epoch [9/10] Train Loss: 0.0018, Train Acc: 0.9995, Val Loss: 0.0501, Val Acc: 0.9866
Epoch [10/10] Train Loss: 0.0027, Train Acc: 0.9993, Val Loss: 0.0617, Val Acc: 0.9837

결론.
Epoch 6부터 과적합 의심


모델 성능 비교.
실험                    최고 Validation Accuracy  마지막 Validation Accuracy
기본 모델 (LR=0.001, 5 Epoch)	  98.56%  98.56%
LR=0.0001, 5 Epoch              98.66%  98.18%
LR=0.0001, 10 Epoch             98.95%  98.37%

결론.
학습률 0.0001, epoch 4가 가정 성능이 좋음

3. train 데이터 증강
RandomRotation(7): 이미지를 좌우로 최대 7도 회전
RandomHorizontalFlip(p=0.5): 학습할 때 50% 확률로 이미지를 좌우 반전
추가

결과.
Epoch [1/5] Train Loss: 0.0222, Train Acc: 0.9921, Val Loss: 0.0817, Val Acc: 0.9732
Epoch [2/5] Train Loss: 0.0117, Train Acc: 0.9957, Val Loss: 0.0305, Val Acc: 0.9904
Epoch [3/5] Train Loss: 0.0082, Train Acc: 0.9969, Val Loss: 0.0331, Val Acc: 0.9885
Epoch [4/5] Train Loss: 0.0067, Train Acc: 0.9974, Val Loss: 0.0334, Val Acc: 0.9895
Epoch [5/5] Train Loss: 0.0032, Train Acc: 0.9990, Val Loss: 0.0431, Val Acc: 0.9895

결론.
2 Epoch: Validation Accuracy 99.04%, Validation Loss 0.0305로 가장 좋은 결과를 기록

4. DenseNet121 모델과 비교
선정이유.
ResNet18과(잔차연결)는 다른 방식(밀집 연결)사용하는 모델이라서,
DenseNet은 여러 층에서 추출한 다양한 특징을 재사용하도록 설계되었어서 학습에 유리하다고 판단,
ImageNet으로 사전 학습된 가중치가 현재 프로잭트에서 유리한 출발점이 될 수 있어서

결과.
Epoch [1/5] Train Loss: 0.1099, Train Acc: 0.9600, Val Loss: 0.0777, Val Acc: 0.9703
Epoch [2/5] Train Loss: 0.0434, Train Acc: 0.9868, Val Loss: 0.0280, Val Acc: 0.9943
Epoch [3/5] Train Loss: 0.0328, Train Acc: 0.9890, Val Loss: 0.0454, Val Acc: 0.9789
Epoch [4/5] Train Loss: 0.0187, Train Acc: 0.9940, Val Loss: 0.0251, Val Acc: 0.9904
Epoch [5/5] Train Loss: 0.0251, Train Acc: 0.9911, Val Loss: 0.0518, Val Acc: 0.9799

모델 성능 비교.
ResNet18 기본 모델	98.56%	5
ResNet18 + 학습률 변경	98.66%	4
ResNet18 + 데이터 증강	99.04%	2
DenseNet121 + 데이터 증강	99.43%	2

최고 성능 모델의 혼동행렬 확인
혼동행렬:
[[266   2]
 [ 19 757]]

새로 학습시킨 DenseNet121 모델의 가중치를 초기화 한 뒤 최적 모델 선발
판정 기준을 0.5, 0.4, 0.3, 0.2로 각각 적용 폐렴을 놓치는 경우 확인

판정 기준: 0.5
혼동 행렬:
[[266   2]
 [ 12 764]]

분류 보고서:
              precision    recall  f1-score   support

      Normal     0.9568    0.9925    0.9744       268
   Pneumonia     0.9974    0.9845    0.9909       776

    accuracy                         0.9866      1044
   macro avg     0.9771    0.9885    0.9826      1044
weighted avg     0.9870    0.9866    0.9867      1044

판정 기준: 0.4
혼동 행렬:
[[266   2]
 [ 11 765]]

분류 보고서:
              precision    recall  f1-score   support

      Normal     0.9603    0.9925    0.9761       268
   Pneumonia     0.9974    0.9858    0.9916       776

    accuracy                         0.9875      1044
   macro avg     0.9788    0.9892    0.9839      1044
weighted avg     0.9879    0.9875    0.9876      1044


판정 기준: 0.3
혼동 행렬:
[[266   2]
 [ 10 766]]

분류 보고서:
              precision    recall  f1-score   support

      Normal     0.9638    0.9925    0.9779       268
   Pneumonia     0.9974    0.9871    0.9922       776

    accuracy                         0.9885      1044
   macro avg     0.9806    0.9898    0.9851      1044
weighted avg     0.9888    0.9885    0.9886      1044


판정 기준: 0.2
혼동 행렬:
[[263   5]
 [  8 768]]

분류 보고서:
              precision    recall  f1-score   support

      Normal     0.9705    0.9813    0.9759       268
   Pneumonia     0.9935    0.9897    0.9916       776

    accuracy                         0.9875      1044
   macro avg     0.9820    0.9855    0.9837      1044
weighted avg     0.9876    0.9875    0.9876      1044

결론.
판정기준 0.3이 폐렴 미탐지는 10건이지만 가장 높은 정확도를 보임

