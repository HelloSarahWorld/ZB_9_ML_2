# 네이버 쇼핑몰 리뷰데이터 감성분석
<img src="https://img.shields.io/badge/NaverShopping-#03C75A?style=for-the-badge&logo=Naver&logoColor=white">
네이버 쇼핑몰 리뷰데이터 감성 분석 프로젝트를 위한 데이터 및 python code가 있습니다.

해당 데이터를 활용하여 진행한 프로젝트는 **리뷰데이터_감성분석_프로젝트.pdf**에서 확인할 수 있습니다.

**프로젝트 목표:** 네이버 쇼핑몰 리뷰 데이터를 활용하여 별점이 없는 리뷰에 대하여 긍정 리뷰인지 부정 리뷰인지 판별
---
## 🧨 프로젝트 선정 배경
- 네이버 쇼핑몰 데이터 중 일부 데이터는 리뷰 데이터는 있으나 별점이 부재<br>
<img width="244" alt="image" src="https://user-images.githubusercontent.com/122243187/233984921-1ae6bd36-5ef6-4936-b799-f33930c58a50.png">
- 별점이 부재한 쇼핑 아이템에 대한 정보를 보완할 수 있는 방법은 없을까?
- 수많은 리뷰 데이터를 짧은 시간 간결하게 표현해 줄 방법은 없을까? <br>
### **→ 별점이 없는 리뷰에 대하여 리뷰 데이터를 기반으로 소비자가 긍정하는 쇼핑 아이템인지 부정하는 쇼핑 아이템인지 판별하는 모델을 구축해보자**

---
## 📚 데이터
### naver_shopping.txt
* 출처: [bab2min](https://github.com/bab2min/corpus/tree/master/sentiment)<br>
<img width="586" alt="image" src="https://user-images.githubusercontent.com/122243187/233986585-b3148075-b4b4-4484-802c-383587e74d3a.png">
<br>
* 네이버쇼핑에서 제품별 리뷰를 별점과 함께 수집한 데이터
* 탭으로 분리되어 있으며, 첫번째 필드는 별점(1~5), 두번째 필드는 텍스트가 위치
* 긍정, 부정으로 분류하기 애매한 3점에 해당하는 리뷰는 제외
<br>
<img width="199" alt="image" src="https://user-images.githubusercontent.com/122243187/233988192-e01c3341-1d71-4340-b1ee-ae992fd94d8b.png">
* 1점, 2점은 부정으로, 4점, 5점은 긍정으로 분류할 경우 긍정과 부정이 각각 50.01%, 49.99%로 약 1:1 비율로 분포
---
## 👓 EDA
### 워드 클라우드를 통해 빈출되는 불용어, 유의어 사전 구축
<img width="439" alt="image" src="https://user-images.githubusercontent.com/122243187/233989963-bbd591fb-33e5-46b3-a483-5441b33930d6.png">
<br>
#### <관련파일 1> review_synonym.txt
* 유의어(ex: 좋아요, 좋네요, 좋구요, 좋습니다, 조아요 등) 일원화를 위해 유의어 사전 구축

#### <관련파일 2> stopwords_self_improved.txt
* 불용어(ex:을, 를, 가 등) 제거를 위해 불용어 사전 구축
* [온라인상 공유되어 있는 기본 불용어 사전](https://raw.githubusercontent.com/yoonkt200/FastCampusDataset/master/korean_stopwords.txt) 활용
* naver_shopping.txt 데이터에 맞춰 워드클라우드를 통해 평점과 관계없이 빈출되는 불용어(구매, 배송, 생각, 사용, 가격, 제품 등) 추가
<br>
---
## 🎢 전처리
### 불용어를 제거하고 형태소별로 토큰화된 리뷰 데이터 형성
<img width="732" alt="image" src="https://user-images.githubusercontent.com/122243187/233990896-ebfc376d-fe20-4747-8b21-a806ed7f97cc.png">
* 부사 및 1글자 형태소를 제외하는 토큰화 함수와 전체 형태소를 활용하는 토큰화 함수 중 어떤 것이 성능이 좋은지 직접 실험적으로 접근해보고자 함

#### <관련 파일> naver_shopping_tokenized_review_improved.csv
* **tokenized1**: 2글자 이상인 명사, 형용사, 동사만 활용
* **tokenized2**: 전체 형태소 활용
---
## 🪢 모델
### 1) DTM vs TFIDF
* 단어를 vectorized하여 Feature를 형성하는 과정에서 단어 출현 빈도를 기반으로 하는 DTM과 단어의 중요도에 따라 가중치를 부여하는 TFIDF 중 어떤 것이 성능이 좋은지 직접 실험적으로 접근해보고자 함
* 토큰화 함수 2가지 X Vectorized 기법 2가지를 각각 시현한 관계로 총 4가지 모델이 형성됨

### 2) Logistic Regression
* Classification에 사용할 수 있는 모델이면서 coefficient 기준으로 주요 키워드를 추출할 수 있는 Logistic Regression 모델을 채택
* 데이터 셋이 20만건 이상인 관계로 large data sets에 적합한 saga를 penalty(elasticnet, l1, l2), C(0.01, 0.1, 1, 5, 10), max iter(100, 500, 1000)에 대하여 GridSearch 수행
* 24시간이 경과하였으나 튜닝이 진전이 없어 parameter의 조합의 경우의 수를 점차 줄여서 GridSearch를 수행한 결과 규제변수 C에 대하여만 간신히 튜닝할 수 있었음


## 🖼️ 최종 모델 및 주요 긍부정 키워드
### 1) 최종 모델
<img width="560" alt="image" src="https://user-images.githubusercontent.com/122243187/233993466-3af1f5d3-4834-4e49-8373-58ab86dca231.png">
* 최종적으로 전체 형태소를 활영하여 TFIDF로 Vectorized한 모델이 성능이 가장 우수하였음

### 2) 최종 모델에 기반한 주요 긍부정 키워드
* **일부 부사와 이모티콘이 주요 긍부정 키워드로 등장한 점**은 전체 형태소를 활용하는 토큰화함수를 적용한 모델의 성능이 우수하게 나타난 점을 설명해주는 요인이 될 수 있음
#### 2-1) 주요 긍정 키워드
* 좋아요, 맛있어요, 예뻐요, 만족해요, 최고, ^^, !, 잘, 빠르고, ㅎㅎ 등

#### 2-2) 주요 부정 키워드
* 실망, 최악, 반품, 그렇다, 별로, 비추, 불편해요, ㅡㅡ, 못, 엉망 등
