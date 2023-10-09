
![header](https://capsule-render.vercel.app/api?type=waving&color=auto&height=300&section=header&text=빅데이터콘&fontSize=90)

<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=Python&logoColor=white"> <img src="https://img.shields.io/badge/jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white">

### 데이터 출저
예술의 전당 데이터를 사용

-------------------------------


### 목적
클래식 공연 활성화를 위한 예술의전당 콘서트홀의 효과적 가격 모델 수립

------
### 분석 배경 및 목적
동적 가격 제도(dynamic pricing): 특정 기준에 따라 가격을 달리 함으로써 매출을 늘리기 위한 전략.

- 서비스 산업에서도 동적가격이 보편적인 전략으로 변화되고 있음을 감안한다면 공연예술산업에서도 동적가격전략의 적극적인 도입이 필요한 시점

- 일반적으로 공연예술의 분야는 공공재의 인식이 있기 때문에 다른 산업군에 비해서 비교적으로 상품의 범용화 단계에 있다고 보기도 어렵고, 탄력성에 관한 상황도 동적가격 도입을 위한 조건을 충족하지 않는 경우가 있음. (가격이 비탄력적임)

- 그러나, 예술업계에서 기부금과 멤버십으로 사용하는 시스템으로 이러한 기부금으로 비영리 공연예술조직을 설립하여 신규 단골 고객을 포섭하는 방식을 사용
실제로, 한 연구결과에 따르면, 공연예술 구매자들의 가격 공정성 지각이  동적 가격제의 도입에 따라 유의미한 결과가 있는지를 연구하자, 부정적인 의견이 적을 것이라는 결과

---------------------------
### 분석 과정

1. 데이터 군집화
   - 동적 가격제 적용을 위한 예매율이 낮은 공연을 군집화

2. 예매율 특징 분석
   - 예매율이 낮은 군의 특징의 특징을 분석
   - 할인율의 적용의 가능성을 분석
3. 예매 예측 및 동적 가격 적용 비교
   - 군집화에서 선출된 군의 값들의 예매취소여부를 예측
   - 구간별로 나눈 값에 할인율 적용 및 예매취소 재 예측
   - 동적가격제의 효과 비교
-------------------------------------
### 전처리 (Preprocessing) 항목
'상당한 결측치들의 존재를 보고 데이터 여부 선정의 우선순위를 정하는 것을 목표로 정함'
- Membershiptype = >  타입1~6에 입력되는 기준을 등급순으로 순차적 배정
- SEAT = > 공연마다 좌석 기준이 다르고 좌석도 여러개여서 하나씩 확인하여 장소별로 분류하고 기준을 정함
- grade => R / S / A / B / C / 전체 일반석   분류
- tokenized_discount: 할인 유형에 따라 8가지의 할인 유형을 만들어 토큰화 변환
- discount_type_count: 공연당 사용된 할인 유형의 갯수
- Time_category: 기준에 따라 오전/오후/저녁으로 분류
- date_difference: 공연날짜 - 공연예매일
- day_of_week: 요일 토큰화
- is_holiday: 공휴일 유무
- reservation_rate: 예매율
- corona_reg: 공연당일 단계별 코로나 규제 단계
- corona_infected: 서울시 당일 코로나 추가 확진자

----------------------------------------------------
#### Membershiptype

- 싹틔우미 - 공연 40% 이상 할인(본인 1장, 지정공연에 한함)
- 노블 - 공연 40% 이상 할인(본인 1장, 지정공연에 한함)
- 그린 - 5가지 프리미엄 혜택 → 공연·전시 5~30% 할인 (최대 2매까지)
- 블루 - 7가지 프리미엄 혜택 → 공연·전시 5~30% 할인 (최대 5매까지)
- 골드 - 10가지 프리미엄 혜택 → 공연·전시 5~40% 할인 (최대 5매까지)
- 이와 같이 확인된 결과를 One-hot encoding (1 or 0)으로 처리

------------------------------------
#### grade
<h4>공연장별로 좌석이 차이가 있음</h4>

- **콘서트홀**
    - R = 1층 BCD 전석 / 2층 BCD 1~3열
    - S = 1층 AE / 2층 AD 1~3열 , BCD 4 ~ 6열
    - A = 2층 AD 4 ~ 7열 / 3층 BCDEF 4~7열 & box석
    - B = 3층 AGMN 전석 & box석

- **IBK챔버홀**
    - R = 1층 ABC 1~11 / BOX
    - S = 나머지 or 전체 일반석

- **리사이틀홀** : 전부 일반석
------------------------------------
#### tokenized_discount
- 1 = 초대권과 차액 하나로 묶음 (둘다 완벽히 무료인 것들이 존재는 하나 무료가 아닌 것들이 있고 할인율을 완벽히 알수가없는것에 비슷함으로 봄) 
- 2 = 할인을 <span style="color:red"> 40%미만으로 받은 고객 </span>
- 3 = 할인을 <span style="color:red"> 40%이상으로 받은 고객 </span> 
- 4 = 할인 받지않고 원가액으로 본 고객(일반)
- 5 = ’기획사’이라는 discount_type열의 토큰값 
- 6 = '장애인’과 '국가유공자’가 해택이 묶여있는 경우가 많아서 2개를 하나의 토큰으로 묶음 
- 7 = 종업자 할인으로 확인되서 ’후원자’,’출연자’,’홍보진행’,’홍보마케팅’,’연주자’를 관련업자로 인지하여 할인율을 제대로 확인이 안되서 토큰값이 2개값으로 되서 하나로 묶음 
- 8 = 할인율이 0% 이지만 가격 또한 0%이고 ‘초대권’과 ‘차액’같은 변수값이 없어 ‘4’토큰과 겹치는 경우가 없는 모든 값이 ‘0’인 값을 8번으로 통합묶음

------------------------------------------------------
## 군집화

- 비인기/인기 공연들을 예매율을 기준으로 n개로 군집화 시도
  - 여러개의 군집화를 진행시킨 결과 n = 3 일 때 집단이 분명하게 드러났음

- 비인기 공연 군집과 인기 공연 군집의 가격, 할인 유형의 갯수와 할인율의 특성 차이를 비교분석
 
   ![1](https://github.com/cav2280/bigcontest/assets/139084776/d0070198-be7d-4de5-acd4-894f42ee13c6)
   ![2](https://github.com/cav2280/bigcontest/assets/139084776/4a1ac0c1-464b-4689-9802-8f41cdf02a1d)
   ![3](https://github.com/cav2280/bigcontest/assets/139084776/77f47daa-ebb7-48f2-ba95-85dfaa32381d)

- 3개의 집단 중 예매율이 제일 낮은 군집(=비인기 그룹)을 따로 **할인율 조정** 분석에 이용

   ![4](https://github.com/cav2280/bigcontest/assets/139084776/52531fe9-50ad-498f-b09a-08869e397829)

--------------------------------------------------
## 모델 선정

- **3 가지 머신러닝**들을 (랜덤 포레스트, Gredient Boosting ,Xtream Gradient Boosting) 을 각각 학습 시키고 정확도 비교
   + 결과 정확도가 제일 높은 Gredient Boosting 모델 선정 

- 수치형에 해당하는 열들은 스케일링 처리
    - 학습시간 감소, 정확도 증가 
       - 예매율과 가격예측에 사용하기로 선정
-----------------------------------------------------
## 분석과정

- 공연별 중복값으로 카운트하여 예매된 숫자를 중복값으로 추출하여 예매율 계산
   - **place,performance_code,play_date,ticket_cancel,play_st_time**

- 공연장마다 좌석수가 정해져있음
   - IBK챔버홀:600석
   - 리사이틀홀:354석
   - 콘서트홀:2505석

- 할인율과 예매취소율의 **상관관계성**을 확인함.

   ![5](https://github.com/cav2280/bigcontest/assets/139084776/89a31892-b0a0-4716-9c86-b99e6e5ff08e)
   ![6](https://github.com/cav2280/bigcontest/assets/139084776/266d4eac-87d3-4400-9a9b-da6ddebb5082)

-------------------------------------------------------------------------
## 분석 결과 활용 및 시사점

<h3>할인율 조정으로 인한 매출 증대</h3>

> 예술의 전당에서 적절한 할인율을 설정하는 것이 **손해**가 아니라 오히려 **매출 증대**에 중요한 요소

> 분석 결과 예술의 전당 입장에서 할인율 조정이 단순히 손익 계산의 문제 X 
  1. 고객들의 예매 의사결정에 크게 영향을 미치는 요소
  2. 적절하게 조절된 할인율은 전체적인 예매율이 상승 

> 초기에 일부 손해처럼 보여도 장기적으로 매출은 실제로 증가하는 효과
----------------------------------------------------------------------------
## 분석 과 활용 및 시사점

+ 중간과정인 기획사,할인업체,초대권 등으로 신규고객이 될 **단골층**으로 신규포섭 방법 제시 **(할인유형의 갯수를 늘림으로써 할인 접근성 증가)**
  + **홍보 효과 증진 및 장기적 고객 유치 가능**

+ 예매율이 높은 공연은 예매취소율이 낮으니 인지도가 높은 공연들을 새롭게 만들어내서 **소비자들이 눈높이와 가격대를 이익교차점**을 찾기

+ 적절한 할인 정책 찾기
   + 너무 높은 할인율은 오히려 장기적으로는 **매출 감소**로 이어질 수 있음
      + *공연 장르,공휴일 유무, 요일별 특수상황* 등 여러 요소를 고려하여 유동적으로 할인혜택들을 **유동적으로 관리 및 운영**

+ 모델 업데이트 및 개선 계속하기
   + 시간이 지남에 따라 데이터(티켓 내역)가 업데이트 되므로 그 값에 따라 주기적으로 모델을 업데이트하며 모델을 항상 **최신 상태 유지**
   + **특정 집단군 고객의 요구** 와 **행동 패턴 변화**에 적응할 수 있는 **강력한 경쟁력 제공**


![footer](https://capsule-render.vercel.app/api?section=footer&height=100)
