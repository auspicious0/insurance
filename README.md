# 병원 비용 데이터 분석 프로젝트

이 프로젝트는 병원 비용 데이터를 분석하는 것을 목표로 합니다. 주요 작업은 데이터 전처리, 탐색적 데이터 분석, 선형 회귀 분석, 그래프 시각화 등을 포함합니다.

[1. 패키지 설치 및 그래프 설정](#1-패키지-설치-및-그래프-설정)
[2. 데이터 수집](#2-데이터-수집)
[3. 데이터 전처리](#3-데이터-전처리)
[4. 데이터 탐색 및 시각화](#4-데이터-탐색-및-시각화)
[5. 가설 설정](#5-가설-설정)
[6. 데이터 분할](#6-데이터-분할)
[7. 선형 회귀 분석](#7-선형-회귀-분석)
[8. 3D 시각화](#8-3d-시각화)
[9. 예측](#9-예측)
[10. 문의](#10-문의)

## 1. 패키지 설치 및 그래프 설정

프로젝트를 시작하기 전, 필요한 R 패키지를 설치하고 그래프 설정을 합니다.
 ```
install.packages(c("tidyverse", "data.table", "GGally", "PerformanceAnalytics", "corrr", "scatterplot3d"))
library(tidyverse)
library(data.table)
library(repr)
library(GGally)
library(PerformanceAnalytics)
library(corrr)
library(scatterplot3d)
options(repr.plot.width=7, repr.plot.height=7)
```

## 2. 데이터 수집

병원 비용 데이터는 Kaggle에서 제공되며 다음 링크에서 얻을 수 있습니다:

[Kaggle](https://www.kaggle.com/datasets/mirichoi0218/insurance)

다음 명령어를 사용하여 데이터를 다운로드하고 확인합니다:

```
system("gdown --id 12KK_QTRPvhvHwim_Mgf0tRz7kpKelhaL")
system("ls", TRUE)
```

## 3. 데이터 전처리

데이터를 불러온 후, 결측값 처리와 이상값 처리를 수행합니다. 데이터의 구조를 확인하고 필요한 변수를 팩터(factor)로 변환합니다. 

```
hc <- hc %>% mutate_at(c("sex", "smoker", "region"), factor)
```

## 4. 데이터 탐색 및 시각화

데이터 전처리 이후, 데이터간의 상관관계를 분석하기 위해 시각화합니다. 

<img src="https://github.com/auspicious0/insurance/assets/108572025/8ecccf50-7131-4279-85af-43e0458a54e1" width="500" height="500"/>
<img src="https://github.com/auspicious0/insurance/assets/108572025/afe40cf7-a507-427d-8523-a19089c80f42" width="500" height="500"/>


## 5. 가설 설정

귀무가설: 흡연(smoker)과 나이(age)는 병원비(charges)에 영향을 미치지 않는다.

대립가설: 흡연(smoker)과 나이(age)는 병원비(charges)에 영향을 미친다.

## 6. 데이터 분할

```
train_set <- sample_frac(hc, 0.7, replace = FALSE)
test_set <- setdiff(hc, train_set)
```

## 7. 선형 회귀 분석

나이(age)와 병원비(charges) 사이의 단순 선형 회귀 모델
```
"age_model = charges ~ age"

Call:
lm(formula = charges ~ age, data = train_set)

Residuals:
    Min      1Q  Median      3Q     Max 
-4811.5 -3572.1 -2466.7  -893.3 28825.0 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  1724.94     645.84   2.671  0.00771 ** 
age           204.73      15.55  13.170  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 6291 on 833 degrees of freedom
Multiple R-squared:  0.1723,	Adjusted R-squared:  0.1713 
F-statistic: 173.4 on 1 and 833 DF,  p-value: < 2.2e-16
```

흡연(smoker)와 병원비(charges) 사이의 단순 선형 회귀 모델
```
 "smoker_model = charges ~ smoker"

Call:
lm(formula = charges ~ smoker, data = train_set)

Residuals:
    Min      1Q  Median      3Q     Max 
-7957.4 -4201.1  -931.6  3104.6 23858.3 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)   8250.4      201.0   41.05   <2e-16 ***
smoker       13454.9      605.6   22.22   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 5479 on 833 degrees of freedom
Multiple R-squared:  0.3721,	Adjusted R-squared:  0.3714 
F-statistic: 493.7 on 1 and 833 DF,  p-value: < 2.2e-16
```

나이(age)와 흡연(smoker)과 병원비(charges) 사이의 다중 선형 회귀 모델
```
"hc_models = charges ~ ."

Call:
lm(formula = charges ~ age + smoker, data = train_set)

Residuals:
   Min     1Q Median     3Q    Max 
 -5705  -1778  -1177   -347  24143 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) -1178.37     458.14  -2.572   0.0103 *  
age           237.99      10.84  21.964   <2e-16 ***
smoker      14543.51     484.61  30.011   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 4362 on 832 degrees of freedom
Multiple R-squared:  0.6026,	Adjusted R-squared:  0.6016 
F-statistic: 630.7 on 2 and 832 DF,  p-value: < 2.2e-16
```

## 8. 3D 시각화

scatterplot3d와 plane3d를 사용하여 3D 그래프로 데이터를 시각화합니다.
<img src="https://github.com/auspicious0/insurance/assets/108572025/e10cea56-aac0-42e8-abd2-6a59fb60c695" width="500" height="500"/>
<img src="https://github.com/auspicious0/insurance/assets/108572025/4dfbda2e-c3d2-44e5-9e4c-65c5f3394126" width="500" height="500"/>

## 9. 예측

테스트 데이터를 사용하여 병원 비용을 예측하고 예측 결과를 확인합니다.

```
"test_set을 이용한 predict"
# A tibble: 357 × 7
     age   bmi children charges   sex smoker charges_predict
   <int> <dbl>    <int>   <dbl> <dbl>  <dbl>           <dbl>
 1    46  33.4        1   8241.     1      0           9769.
 2    60  25.8        0  28923.     1      0          13101.
 3    52  30.8        1  10797.     1      0          11197.
 4    23  23.8        0   2395.     0      0           4295.
 5    60  36.0        0  13229.     1      0          13101.
 6    18  34.1        0   1137.     0      0           3106.
 7    63  23.1        0  14452.     1      0          13815.
 8    55  32.8        2  12269.     1      0          11911.
 9    23  17.4        1   2775.     0      0           4295.
10    18  26.3        0   2198.     1      0           3106.
# ℹ 347 more rows
```

## 10. 문의
프로젝트에 관한 문의나 버그 리포트는 [이슈 페이지](https://github.com/auspicious0/insurance/issues)를 통해 제출해주세요.

보다 더 자세한 내용을 원하신다면 [보고서](https://github.com/auspicious0/insurance/blob/main/%EC%84%A0%ED%98%95%ED%9A%8C%EA%B7%80.ipynb) 를 확인해 주시기 바랍니다.
