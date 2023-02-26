# A/B tests with Statistics and Machine Learning

## 프로젝트 목차

- [문제 설정](#문제-설정)
- [AB 테스트 목적](#AB-테스트-목적)
- [가설 설정](#가설-설정)
- [데이터 소개 및 평가 지표 설정](#데이터-소개-및-평가-지표-설정)
- [샘플 사이즈 설정](#샘플-사이즈-설정)
- [결과 분석](#결과-분석)
- [결론](#결론)
- [보완 지점](#보완-지점)
- [레퍼런스](#레퍼런스)

## 문제 설정

- 학습에 충분한 시간을 쏟을 수 없는 사람이 무료 평가판 교육에 등록함
- 평가판 등록자가 늘어나면서 유료 등록 가능성이 높은 사람에게 주력 서비스인 '코칭'을 집중할 수 없음

## AB 테스트 목적

- 실험군에게 등록 시 교육에 할애할 수 있는 시간을 입력하도록 함
- 통상적으로 소요되는 시간보다 적은 시간을 입력 시 해당 교육에 더 많은 시간이 필요하다는 경고 메시지를 출력함
- 새로운 기능을 추가했을 때 교육에 충분한 시간을 쏟을 수 없는 사람이 무료 평가판을 등록하지 않고 떠날 것인지 확인

## 가설 설정
- 귀무가설 : 새로운 기능을 추가하더라도 무료 평가판을 등록하는 사람의 수는 변하지 않는다.
- 대립가설 : 기능을 추가하면 무료 평가판을 등록하는 사람의 수가 유의미하게 변화한다.

## 데이터 소개 및 평가 지표 설정

### 1. 데이터 소개
![스크린샷 2022-06-14 오후 2 34 03](https://user-images.githubusercontent.com/93904398/173500893-b5c2dc85-3aae-4f4c-b6d1-415e02473af5.png)
- Date : 데이터 발생 일자
- Pageviews : Course overview 페이지 view cookies
- Clicks : 무료 평가판 등록 버튼 click cookies
- Enrollments : 무료 평가판 등록자 수
- Payments : 무료 평가판 종료 후 유료 과정 등록자 수 

### 2. 불변 지표
- 두 집단을 편향없이 비교하기 위해 테스트 진행 과정에서 변하지 않고 유지되어야 하는 지표 설정
  - Pageviews : Course overview 페이지 view cookies
  - Clicks : 무료 평가판 등록 버튼 click cookies

### 3. 평가 지표
- 새로운 기능의 효과를 확인하기 위해 필요한 지표
  - Gross Conversion : 무료 평가판 등록 버튼 클릭 수 대비 무료 과정 등록 확률
  - Retention : 무료 평가 과정 등록 대비 유료 결제 확률
  - Net Conversion : 무료 평가판 등록 버튼 클릭 수 대비 유료 결제 등록 확률

### 4. 평가 지표에 대해 baseline 설정
- 평가 지표 상의 유의미한 변화가 있더라도 그 변화가 비즈니스 차원에서 유의미한 수익률을 보장해야 함
- 기능을 변경하는 비용 대비 평가 지표의 향상 정도가 높지 않다면 기능을 변경할 이유가 없음
  - Gross Conversion : -0.01
  - Retention : 0.01
  - Net Conversion : 0.0075 


## 샘플 사이즈 설정

### 1. 지표 당 검정을 위해 필요한 샘플 사이즈 계산

- Gross Conversion : 645,875 Cookies
- Retention : 4,737,454 Cookies
- Net Conversion : 685,675 Cookies

### 2. 최종 샘플 사이즈

- 하루에 약 40,000 건의 페이지 조회가 발생하고 있음. 각 지표에 필요한 샘플 사이즈를 얻기 위해 필요한 데이터 수집 기간은 다음과 같음
  - GC : 17일(645875 / 40000)
  - R : 119일(4737454 / 40000)
  - NC : 18일(685675 / 40000)
- 현실적으로 119일동안 A/B 테스트를 진행하기 어려움
- NC의 샘플 사이즈를 선택하면 GC의 샘플 사이즈 수도 포괄할 수 있음(Retention에 대한 테스트는 진행할 수 없음)
- **A/B 테스트를 위해 수집할 샘플의 사이즈는 685,675개로 선택**

## 결과 분석

### 1. 불변 지표 평가

- 전체 데이터에 대해 control과 experiment 데이터의 비율은 0.5이여야 샘플 사이즈에 편향이 없다고 볼 수 있음
- 코스 개요 페이지의 쿠키 수 : 통제군과 실험군의 비율이 95% 신뢰도에서 0.5 +-0.012 구간 안에 위치하는 것을 확인 -> 귀무가설 채택
- 무료 평가판 등록 버튼 클릭 수 : 통제군과 실험군의 비율이 95% 신뢰도에서 0.5 +-0.041 구간 안에 위치하는 것을 확인 -> 귀무가설 채택

### 2. Effect size 방법을 활용한 지표 평가

#### Gross Conversion

- 통제군과 실험군의 차이 : -0.0206
- 두 집단의 차이는 95% 확률로 [-0.0292 ~ -0.012] 안에서 형성될 가능성이 높음
- 신뢰 구간 안에 0이 포함되어 있지 않으므로 통게적으로 두 집단의 GC 차이는 통계적으로 유의미함
- 또한, 신뢰 구간의 범위가 baseline인 -0.01보다 작으므로 **실질적으로도 유의미한 차이라고 볼 수 있음**

#### Net Conversion

- 통제군과 실험군의 차이 : -0.0049
- 두 집단의 차이는 95% 확률로 [-0.0116 ~ 0.0018] 안에서 형성될 가능성이 높음
- 신뢰 구간 안에 0이 포함되어 있으므로 두 집단의 차이는 통계적으로 유의하지 않을 가능성이 있음
- 또한, 신뢰 구간의 범위가 baseline인 0.075보다 작기 때문에 실질적으로도 유의미한 차이라고 볼 수 없음

### 3. 부호 검정(sign test)를 활용한 지표 평가

평균값을 기반으로 한 효과 크기 방식의 검정은 이상치에 취약하기 때문에 이상치의 영향을 덜 받는 sign test로 분석 결과를 cross check 함

#### Gross Conversion

- A/B 집단 간에 차이가 없다고 가정한다면 A와 B 집단의 GC 지표의 크기가 유사해야함
- 실험군의 GC 지표가 더 큰 경우의 수 : 4
- 특정 집단의 GC 지표가 4번만 클 수 있는 확률(유의확률) : 0.0026
- 유의확률이 0.05보다 작으므로 귀무가설을 기각함 -> A/B 집단 간에 유의미한 차이가 있음

#### Net Conversion 

- NC 지표에서 실험군이 더 큰 케이스 수 : 10
- 특정 집단의 NC 지표가 10번만 클 수 있는 확률(유의확률) : 0.6774
- 유의확률이 0.05보다 크기 때문에 귀무가설을 채택함 -> A/B 집단 간에 유의미한 차이가 없음

### 4. 머신러닝을 활용한 지표 평가

#### 특성 중요도 분석

![스크린샷 2022-06-07 오전 11 44 21](https://user-images.githubusercontent.com/93904398/172284693-70665d52-2234-434f-8251-6662c7368690.png)

- 페이지 뷰와 클릭 수가 타겟값(전환율)에 영향을 미치는 특성으로 보임
- 영향력의 방향은 알 수 없으므로 회귀계수와 함께 특성을 파악하는 것이 필요함


#### 회귀 계수 분석

<img width="693" alt="스크린샷 2022-06-07 오전 11 38 47" src="https://user-images.githubusercontent.com/93904398/172284077-31810159-a25d-404a-9f15-cb32d87ee028.png">

- 무료 평가판 등록 Clicks 수가 많아질 수록 ‘Enrollments’는 감소함
- 새로운 기능이 무료 평가판 등록 감소에 유의미한 역할을 한다고 해석할 수 있음

<img width="692" alt="스크린샷 2022-06-07 오전 11 42 00" src="https://user-images.githubusercontent.com/93904398/172284388-27f9fb76-d577-4f0f-9440-1fb9f0a4ff50.png">

- Course overview 페이지에는 Enrollments 감소를 위한 새로운 기능이 적용되지 않기 때문에 페이지 조회수가 늘수록 Enrollments가 증가하는 것을 볼 수 있음
- Enrollments를 더 줄이려는 비즈니스 목적이 있다면 Course overview 페이지에도 새로운 기능을 추가하는 것이 좋음

<img width="704" alt="스크린샷 2022-06-07 오전 11 42 46" src="https://user-images.githubusercontent.com/93904398/172284464-a4924b22-082d-44aa-aaf2-574ae9bd2606.png">

- 실험군일 때(=새로운 기능이 적용됐을 때) Enrollments의 감소가 나타남
- 다만, 통계적으로 유의미한 수준은 아니기 때문에 해석에 주의가 필요함

## 결론

- 새로운 기능을 사용하면 클릭수와 페이지뷰를 조절하여 무료 사용자의 등록률을 낮출 수 있지만, 유료 사용자의 등록률을 높일 수는 없을 것으로 보임
- 새로운 기능이 유료 사용자의 비율을 높일 수 없으므로 **비즈니스 상의 이득이 크다고 볼 수 없음**
- 새로운 기능을 도입하지 않는 것을 제안함

## 보완 지점

- 머신러닝 결과에 신뢰성을 높이기 위해 더 많은 샘플이 필요함
- 비즈니스 상 유의미한 이윤을 보장할 베이스라인을 임의로 설정했으나, 실제 업무에서는 고객 획득 비용(CAC)를 고려하여 설정할 필요가 있음

## 레퍼런스

- A/B tests with python : https://www.kaggle.com/code/tammyrotem/ab-tests-with-python/notebook
- 오차한계의 개념 및 구현 방법 : https://apcalculus.tistory.com/218
- 샘플 사이즈의 의미 : https://playinpap.github.io/sample-size-calculator-statistics/
- 지표에 대한 이해 : Udacity, A/B testing, Choosing and Characterizing Metrics(https://classroom.udacity.com/courses/ud257/lessons/456f424d-0cbc-481c-a0ab-a6217013f46e/concepts/bde635a8-6c93-4f14-8b5b-85387a072a8c)
