# BigDataProject
3학년 2학기 inhatc 빅데이터처리 기말과제

# 주제: 자동차 제작 결함 및 리콜 신고 데이터 분석
## 분석 내용:
- 가구마다 개인용 자동차를 보유하게 되면서 자동차는 삶의 일상의 일부가 되었지만, 안전성에 대해서는 산발적인 문제 제기가 계속되고 있습니다.
- 이에 따라 전격적인 자동차 리콜 사태도 종종 발생하여 화제가 되고 있습니다.
- 최근에도 저희 아파트 단지의 주차장에서 한 차량에 화재가 발생했던 일이 있었습니다.
- 리콜이 발생하는 주요 요인 중에 하나로 자동차를 제작 과정에서의 결함도 있다고 생각합니다.

#### 이에 따라 한국교통안전공단에서 제공한 2023년까지의 자동차 제작 결함 신고 정보를 바탕으로 유의미한 패턴 및 인사이트를 분석

### 리콜(recall): 제품의 설계, 제조 단계에서 결함이 발견되었을 때 문제 예방의 차원에서 판매자가 무상으로 수리, 점검 및 교환을 해주는 소비자 보호 제도

# 파일 설명

```
bigData_project.ipynb, bigData_project.py
```

데이터 수집, 가공, 전처리, 시각화의 전 과정에 대한 전체 코드들이 들어있는 구글 코렙 파일, py 파일입니다.

```
cars_recall1.csv
```

한국교통안전공단_자동차제작결함신고정보.csv
자동차 리콜센터에 접수된 자동차 결함 의심 신고 유형에 대한 데이터로 접수일자, 제작자, 차명, 모델 년도 등의 항목을 제공합니다.
<br>

[데이터셋 미리보기]
<div align="center">

  |                          데이터셋1                           |
  | :-------------------------------------------------------: |
  | <img width="600" height="350" src="./df 사진/cars_recall1.PNG"/> |
  
  |                          데이터셋2                           |
  | :-------------------------------------------------------: |
  | <img width="700" height="350" src="./df 사진/cars_recall2.PNG"/> |

</div>

```
cars_recall2.csv
```

한국교통안전공단_자동차 결함 리콜 현황.csv
자동차의 리콜 현황에 대한 데이터로 제작자, 차명, 생산기간(From), 생산기간(To) 리콜 사유 등의 항목을 제공합니다.

```
result.csv
```

# 2.2 데이터 전처리
## 2.2.1 CSV 파일 불러오기 및 열이름 설정

[소스 코드]

```
import pandas as pd

df1 = pd.read_csv("/content/cars_recall1.csv", encoding="euc-kr")
df2 = pd.read_csv("/content/cars_recall2.csv", encoding="euc-kr")

#  데이터프레임의 정보 출력
print(df1.info())
print("\n")
print(df2.info())

# 각 데이터프레임의 열 명 지정
column_names = ["제작사", "차명", "생산기간(부터)", "생산기간(까지)", "리콜개시일", "리콜사유"]
column_names2 = ["접수일자", "제작사", "차명", "모델년도"]
df1.columns = column_names
df2.columns = column_names2

# 변경 결과 출력
print(df1)
print("\n")
print(df2)
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="600" height="500" src="./깃허브 사진/3.PNG"/> |
  
  |                          결과2                           |
  | :-------------------------------------------------------: |
  | <img width="800" height="320" src="./깃허브 사진/4.PNG"/> |

  </div>

## 2.2.2 데이터 가공 - 필요한 데이터만 추출 및 가공
- 데이터셋1에서 열 이름 중에 생산기간(부터), 생산기간(까지) 부분에 대하여 “모델 년도”라는 열 하나로 전처리를 하였습니다. 생산기간(부터)과 리콜 사유라는 열들은 필요 없는 부분이고, 생산기간(까지)는 생산을 완료하였기 때문에, 이를 “모델 년도”로 간주하였습니다. 
- 먼저 필요 없는 부분들은 drop()으로 삭제하였습니다.
  
<br>

[소스 코드]

```
# 생산기간은 년도 부분만 추출
# 생산기간과 모델 년도를 동일하게 간주함
# 생산기간(까지)를 모델 년도로 간주함
df1.drop(['리콜사유', '생산기간(부터)'], axis=1, inplace=True)
print(df1)
print(df2)
```

- 열 이름 변경을 위해 rename()으로 변경하고, 해당 내용들의 형식을 년도만 추출하여 변경하였습니다.

```
#df1에서 생산기간(까지)를 모델년도로 설정하고, 년도만 추출하여 변경사항 적용
df1.rename(columns={'생산기간(까지)':'모델년도'}, inplace=True)
df1['모델년도'] = df1['모델년도'].str.extract(r'(\d{4})')
print(df1)
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="300" src="./깃허브 사진/4.PNG"/> |
  
  |                          결과2                           |
  | :-------------------------------------------------------: |
  | <img width="800" height="200" src="./깃허브 사진/4-1.PNG"/> |

  </div>

- 데이터셋1번과 연결하기 위해, 열 이름을 같게 하기 위해 데이터셋2번의 “접수일자”를 “리콜개시일“로 변경하였습니다.

[소스 코드]

```
df2.rename(columns={'접수일자':'리콜개시일'}, inplace=True)
print(df1)
print(df2)
```

[출력 결과]
<div align="center">
  
  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="170" src="./깃허브 사진/5.PNG"/> |

</div>

## 2.2.3 데이터 가공 - 특정 열의 위치 바꾸기
- 데이터셋1의 순서와 같게 만들기 위해, 데이터셋2번의 ”리콜개시일“이라는 열의 위치를 맨 끝으로 바꾸었습니다.
  
[소스 코드]

```
column_to_move = '리콜개시일'

# 특정 열을 제외한 나머지 열들을 리스트에 추가, 새로운 순서를 만들기 위함
new_column_order = [col for col in df2.columns if col != column_to_move]
new_column_order.append(column_to_move) # 위치를 바꾸고 싶은 열을 맨 끝에다 추가

# 열을 다시 재정렬
df2 = df2[new_column_order]
print(df2)
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="600" height="180" src="./깃허브 사진/6.PNG"/> |

  </div>

## 2.2.4 데이터 가공 - 두 데이터프레임 연결하기
- 데이터 전처리 결과, 데이터셋1번과 데이터셋2번이 모두 4개의 같은 이름의 열들로 구성하게 되었습니다. 
- 하지만 열 하나가 데이터 타입이 다르다는 것을 확인하였고, 이에 대한 추가 작업이 필요해 보입니다.

<div align="center">
  
  <img width="600" height="180" src="./깃허브 사진/7.PNG"/> 

</div>
  
- 많은 데이터양으로 유의미한 결과를 내기 위해 concat()과 합집합 옵션으로 두 데이터프레임을 연결하였습니다.
- 또, 다시 순서를 날짜 순으로 정렬하기 위해, 기준을 ”리콜개시일“로 설정하여 오름차순으로 정렬하였습니다.
- 해당 결과를 df3라는 새로운 데이터프레임에 저장하였습니다.

[소스 코드]

```
df3 = pd.concat([df1, df2], join='outer')
print(df3)

# ”리콜개시일“ 열을 기준으로 오름차순으로 정렬(기본값 생략됨: ascending=True)
df3 = df3.sort_values(by='리콜개시일')

# Reset the index after sorting (optional)
df3 = df3.reset_index(drop=True)
print(df3)
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="300" src="./깃허브 사진/8.PNG"/> |

</div>

# 2.3 시각화를 위한 데이터 분류
- 데이터 전처리, 가공의 과정을 거친 두 데이터셋들을 연결한 결과 데이터셋입니다. 이 csv 파일을 바탕으로 시각화를 진행하였습니다.

<div align="center">

  |                          두 데이터셋들을 연결한 결과 데이터셋                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="300" src="./깃허브 사진/8.PNG"/> |

</div>
  
## 2.3.1 결측치, NaN 값 확인 및 제거하기
- 연결되어 만들어진 데이터프레임에서 결측치나 NaN 값이 있다면 제거하거나 다른 대체 값으로 처리합니다.

[소스 코드]

```
df3.isna().sum(axis=0)							
df3 = df3.dropna()
print(df3)
# 결과를 csv로 저장
df3.to_csv("/content/result.csv", encoding="euc-kr")
df3.isna().sum(axis=0)
```

<br>

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="500" height="180" src="./깃허브 사진/9.PNG"/> |

  |                          결과2                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="260" src="./깃허브 사진/10.PNG"/> |

  </div>

## 2.3.2 계절별로 구분하기
- ”리콜개시일“을 기준으로, 계절별로 리콜횟수를 구분합니다.

[소스 코드]

```
# 계절별: 봄(3~5), 여름(6~8), 가을(9~11), 겨울(12~2)
# 계절별로 구분했을 때 구분 결과를 보여주는 열 추가하기
# 봄 Spring, 여름 Summer, 가을 Autumn, 겨울 Winter

# '리콜개시일' 날짜형식으로 변환
df3['리콜개시일'] = pd.to_datetime(df3['리콜개시일'])

# 계절 구분하는 함수 구현
def get_season(month):
    if 3 <= month <= 5:		
        return 'Spring'
    elif 6 <= month <= 8:							 	
        return 'Summer'
    elif 9 <= month <= 11:
        return 'Autumn'
    else: return 'Winter'

# 맨 끝에 새로운 열 추가 및 함수 적용
df3['계절'] = df3['리콜개시일'].dt.month.apply(get_season)
# 변경 사항 출력
print(df3)
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="500" height="250" src="./깃허브 사진/11.PNG"/> |

</div>
  
## 2.3.3 월별로 구분하기
- ”리콜개시일“을 기준으로, 월별로 리콜횟수를 구분합니다.

[소스 코드]	

```
# '리콜개시일' 날짜형식으로 변환
df3['리콜개시일'] = pd.to_datetime(df3['리콜개시일'])

# 월별로 구분하는 함수 구현
def get_month_name(month):
    month_names = [
        'Jan', 'Feb', 'Mar', 'April', 'May', 'June',
        'July', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'
    ]
    return month_names[month – 1]	

# 맨 끝에 새로운 열 추가 및 함수 적용						   
df3['월별'] = df3['리콜개시일'].dt.month.apply(get_month_name)

# 변경 사항 출력
print(df3)
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="500" height="250" src="./깃허브 사진/12.PNG"/> |

</div>

# 3 시각화
## 계절별, 월별, 제작사별, 차량모델별로 시각화 진행

시각화에 사용한 라이브러리

```
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

## 3.1 [계절별] - 그룹화하기 및 리콜 횟수가 많은 순서대로 정렬하기

[소스 코드]

```
# 계절별로 구분하기
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# csv 불러오기
df3 = pd.read_csv("/content/result.csv", encoding="euc-kr")

grouped_Season = df3.groupby(['계절'])
grouped_Season.count().sort_values(by='리콜개시일', ascending=False)

# Reset the index to make '계절' a regular column, not the index
grouped_Season = grouped_Season.count().reset_index()
grouped_Season.rename(columns={"계절": 'Seasons', "리콜개시일": 'recall_count'}, inplace=True)

# Define the order in which you want the seasons to appear
season_order = ['Spring', 'Summer', 'Autumn', 'Winter']

# Create a bar chart with the specified season order
plt.figure(figsize=(8, 5))
sns.set(style="darkgrid")
ax = sns.barplot(x="Seasons", y="recall_count", data=grouped_Season, 
order=season_order)
plt.show()
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="700" height="470" src="./사진/111.png"/> |

</div>

## 3.2 [월별] - 그룹화하기 및 리콜 횟수가 많은 순서대로 정렬하기

[소스 코드]

```
# 월별로 구분하기
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# csv 불러오기
df3 = pd.read_csv("/content/result.csv", encoding="euc-kr")
grouped_monthly = df3.groupby(['월별'])
grouped_monthly.count()["리콜개시일"].sort_values(ascending=False)

# Reset the index to make '계절' a regular column, not the index
grouped_monthly = grouped_monthly.count().reset_index()
grouped_monthly.rename(columns={"월별": 'months', "리콜개시일": 'recall_count'}, inplace=True)

# Define the order in which you want the seasons to appear
month_order = ['Jan', 'Feb', 'Mar', 'April', 'May', 'June', 'July', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

# Create a bar chart with the specified season order
plt.figure(figsize=(10, 5))
sns.set(style="darkgrid")
sns.barplot(x="months", y="recall_count", data=grouped_monthly, order=month_order)
plt.show()
```

[출력 결과]
<div align="center">

  |                          결과1                          |
  | :-------------------------------------------------------: |
  | <img width="700" height="400" src="./사진/112.png"/> |

  </div>

## 3.3 [제작사별] - 그룹화하기 및 리콜 횟수가 많은 순서대로 정렬하기

[소스 코드]
```
# 제작사별로 구분하기
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# 그래프 그리기
df3 = pd.read_csv("/content/result.csv", encoding="euc-kr")
grouped_manufacturer = df3.groupby(['제작사'])
grouped_manufacturer.count()["차명"].sort_values(ascending=False)
pd.DataFrame(grouped_manufacturer.count()["차명"].sort_values(ascending=False)).rename(columns={"차명":"리콜횟수"})
grouped_manufacturer = grouped_manufacturer.count().reset_index()
grouped_manufacturer.rename(columns={"제작사": 'manufacturer', "리콜개시일": 'recall_count'}, inplace=True)
top_manufacturers = grouped_manufacturer.sort_values(by='recall_count', ascending=False).head(20)

# 시각화
plt.figure(figsize=(25, 15))
sns.set(style="darkgrid")
sns.set_palette("Set2")
ax = sns.barplot(x="manufacturer", y="recall_count", data=top_manufacturers)

# Matplotlib 함수로 한글 폰트 설정 및 xticks 조정
plt.xticks(rotation=45, fontname='NanumBarunGothic')
plt.xlabel('제작사', fontname='NanumBarunGothic')
plt.ylabel('리콜횟수', fontname='NanumBarunGothic')
plt.title('상위 20 제작사별 리콜횟수', fontname='NanumBarunGothic')
plt.show()
```

[출력 결과]
<div align="center">

  |                          결과1(상위 10개)                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="600" src="./사진/11-2.png"/> |

  |                          결과2(상위 5개)                          |
  | :-------------------------------------------------------: |
  | <img width="500" height="250" src="./사진/1131.PNG"/> |

  |                          결과3(상위 20개)                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="500" src="./사진/113.png"/> |
  
</div>

## 3.4 [모델별] - 그룹화하기 및 리콜 횟수가 많은 순서대로 정렬하기

[소스 코드]
```
# 모델별로 구분하기
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# 그래프 그리기
df3 = pd.read_csv("/content/result.csv", encoding="euc-kr")
grouped_models = df3.groupby(['차명'])
grouped_models.count()["리콜개시일"].sort_values(ascending=False)
grouped_models = grouped_models.count().reset_index()
grouped_models.rename(columns={"차명": 'models', "리콜개시일": 'recall_count'}, inplace=True)
top_models = grouped_models.sort_values(by='recall_count', ascending=False).head(20)

# 시각화
plt.figure(figsize=(25, 15))
sns.set(style="darkgrid")
sns.set_palette("Set2")
ax = sns.barplot(x="models", y="recall_count", data=top_models)

# Matplotlib 함수로 한글 폰트 설정 및 xticks 조정
plt.xticks(rotation=45, fontname='NanumBarunGothic')
plt.xlabel('모델명', fontname='NanumBarunGothic')
plt.ylabel('리콜횟수', fontname='NanumBarunGothic')
plt.title('상위 20 모델별 리콜횟수', fontname='NanumBarunGothic')
plt.show()
```

[출력 결과]
<div align="center">

  |                          결과1(상위 10개)                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="600" src="./사진/13-2.png"/> |

  |                          결과2(상위 5개)                          |
  | :-------------------------------------------------------: |
  | <img width="500" height="250" src="./사진/1142.PNG"/> |

  |                          결과3(상위 20개)                          |
  | :-------------------------------------------------------: |
  | <img width="800" height="500" src="./사진/114.png"/> |
  
</div>

# 4. 결론
## 4.1 집계 결과
#### 계절별 – 봄, 여름, 겨울, 가을
#### 월별 – 8월, 4월, 2월, 3월, 7월...
#### 제작사별 - 1위: 현대자동차/2위: 기아자동차/3위: 메르세데스밴츠/4위: BMW/5위: 르노코리아자동차...
#### 모델별 – 1위: 쏘렌토 하이브리드/2위: 캐스퍼/3위: k8 하이브리드/4위: QM6/5위: 현대 그랜저...

## 4.2 시각화 분석
- 계절별로 봄이 2500건 내외로 리콜건수로 가장 많았고, 이어서 여름, 겨울, 가을 순서대로 결과가 나왔습니다.
- 월별로는 8월이 가장 1200건에 다다르는 횟수로 리콜횟수가 가장 많았고, 4월, 2월, 3월, 7월... 순서로 이어졌습니다.
- 제작사별로는 현대자동차가 대략 2700건로 가장 많았고, 그 다음으로는 기아자동차, 메르세데스벤츠, BMW, 르노코리아자동차... 순서로 결과가 나왔습니다.
- 모델명별로는 기아자동차의 쏘렌토 하이브리드가 대략 570건으로 가장 리콜건수가 많았고, 그 다음으로 현대자동차의 캐스퍼, 기아자동차의 K8 하이브리드, 르노코리아자동차의 QM6, 현대자동차의 그랜저 등등의 순서로 이어졌습니다. 

## 4.3 시각화를 통해 알 수 있는 사실
- 자동차 리콜은 계절에 큰 영향을 미치는 것으로 보입니다. 여름에 차량이 빠르게 달리는 동안 타이어 안에 있는 공기가 외부의 뜨거운 온도와 마찰로 인해 팽창하여 타이어가 터지는 현상이 주로 발생합니다. 그 외에는 다른 원인들도 있겠지만 가장 큰 원인 중 하나라고 생각합니다. [출처: 신아일보 - 여름철 30도 웃돌면 타이어사고 66% 증가] 
- 모델별에 대해서는 특히, 1위의 쏘렌토 하이브리드의 경우, 해당 모델에 탑재된 기능이 제대로 작동하지 않아 리콜이 발생한다고 합니다. 그 외에도, 엔진룸 화재 발생 가능성에 대한 이유로 제작사에서 안전 점검이 부실하여 부득이하게 리콜을 하는 등 다양한 원인이 있는 것으로 보입니다. 
- [출처: Mtoday뉴스 - "무선충전 왜 안되는거야?" 기아, 신형 쏘렌토(MQ4 PE) 1,725대에 무상수리 실시]
- 모델별에서 2위를 차지한 캐스퍼의 경우, 에어컨이나 열선을 가동시키면 차가 덜덜덜 떨라는 현상이 발생하는 경우가 많아 대규모로 리콜을 진행했다고 합니다. [출처: 뉴시스 - "열선 켜면 차가 '덜덜덜'"…캐스퍼, 결함신고 잇달아]
- 또, 3위인 K8 하이브리드는 도로 주행 중에 갑자기 경고등이 뜨면서 서행하는 문제가 발생하여 SW 설정에 오류가 있음을 확인하고 리콜을 진행하였습니다. [출처: 이데일리 - K8하이브리드 '주행 중 돌연 서행'…기아 6만여 대 무상수리]

- 이를 통해, 전반적으로 자동차 제작사들이 제작 완료 후, 상업에 내놓기 전에 여러 번의 점검과 테스트를 필수적으로 해야한다는 점과 계절별로는 주로 여름철, 겨울철에 급격하게 기온이 달라지는 시기에 타이어를 정기적으로 점검하는 습관을 길러야 한다는 점을 알게 되었습니다.


감사합니다. :)
