# 데이터 이해 및 Baseline 구축
## 데이터 이해

흉부 X-ray 데이터
=============== train.csv ===============
        file_name  label
0  train_0001.png      0
1  train_0002.png      0

컬럼:
['file_name', 'label']

크기: (5216, 2)

사이즈 : [((224, 224), 5216)]

흑백 여부 : L

label
1    3875
0    1341
Name: count, dtype: int64

label
1    0.742906
0    0.257094
Name: proportion, dtype: float64

=============== test.csv ===============
       file_name
0  test_0001.png
1  test_0002.png

컬럼:
['file_name']

크기: (624, 1)

결측치 없음

### 요약
Train 이미지: 5,216장
Test 이미지: 624장
이미지 크기: 224 × 224
Train CSV: file_name, label
Test CSV: file_name
흑백, 결측치 x