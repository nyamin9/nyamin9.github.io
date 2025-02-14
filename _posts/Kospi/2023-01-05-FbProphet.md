---
title : "🏆데이콘 KOSPI 분석 04-FbProphet : KOSPI 예측"

categories:
    - Kospi
tags:
    - [데이콘, KOSPI, Visualization, plotly, fbprophet]

toc : true
toc_sticky : true 
use_math : true  

date: 2023-01-05
last_modified_at: 2023-01-05  
---  
* * *  

## 🏆 1. KOSPI 지수 예측해보기  

📈 주가 예측에 주로 사용하는 FbProphet 모듈을 사용해서 코스피지수를 예측할 것입니다.  
📈 이 모듈을 설치하고 사용하기가 정말 어려웠습니다. 이 모듈을 사용하실 분들은 처음부터 코랩에서 실행하시는 게 좋을 것 같습니다!!  
📈 FbProphet 모듈 사용을 위해 미리 설치해줘야 할 모듈이 상당히 많습니다. 설치를 먼저 하고, KOSPI 지수를 예측할 것입니다.  

***  


### 🏆 1.1. 필요한 모듈 설치  

```py
# wheel 모듈 설치
pip install wheel

# cython 모듈 설치
pip install cython

# pystan 모듈 설치
# 특정 버전에서만 작동할 수도 있어 여러 버전을 실험해 보았는데, 2.17.1.0 버전에서 잘 작동했다.
pip install pystan==2.17.1.0

# fbprophet 모듈 설치
pip install fbprophet==0.6

# fbprophet 모듈 업데이트
pip install --upgrade fbprophet

# 설치한 모듈 임포트
from fbprophet import Prophet
from fbprophet.plot import plot_plotly, plot_components_plotly

# statsmodels 모듈 설치
!pip install statsmodels==0.11.1
```  

📈 위 순서대로 모듈을 설치하면 뭔가 좌르르륵 많이 출력됩니다.  

📈 블로그 포스팅에서는 가독성을 위해서 이러한 설치 출력을 모두 배제하였다는 점 양해 부탁드립니다!!  

📈 실제로 fbprophet 모듈을 사용하실 분들도 위의 순서를 따르시는 게 좋을 것 같다고 생각합니다🙂.<br>  

***  

### 🏆 1.2. 2019~2022년도 코스피 지수를 사용한 예측  

```py
# stick_2019 변수에 2019년도 이후의 코스피지수 선언
# 예측하고자 하는 column으로 지수의 종가를, 예측을 위한 날짜 데이터로는 인덱스를 선언
stock_2019 = kospi_2019
stock_2019['y'] = stock_2019['Close']
stock_2019['ds'] = stock_2019.index

# 예측을 위한 모델 생성 : m_2019
m_2019 = Prophet()
m_2019.fit(stock_2019)

# 향후 15일간의 지수를 예측
future_2019 = m_2019.make_future_dataframe(periods=15)
forecast_2019 = m_2019.predict(future_2019)
```  
```py
# matplotlib - 시각화 
# 파란 실선이 실제 지수를, 주황색 실선이 예측 지수를 의미

plt.figure(figsize=(15,7))
plt.plot(kospi_2019.index, kospi_2019["Close"], label="real")
plt.plot(forecast_2019["ds"], forecast_2019["yhat"], label="forecast")
plt.grid(True)
plt.legend()
plt.show()
```  
<p align="center"><img src="https://user-images.githubusercontent.com/65170165/210681611-b87961de-5ae7-4930-a0cc-826a01072435.png" width="1500" /></p><br>  

- 실제 지수와 예측 값 사이에 차이가 있긴 하지만, 전체적인 추세는 모두 따라가고 있다는 것을 확인할 수 있었습니다.  
  
```py
# 전체기간 동안의 지수 트렌드와 월별, 주별 변동량
m_2019.plot_components(forecast_2019);
```  
<p align="center"><img src="https://user-images.githubusercontent.com/65170165/210681837-221fdbed-29a9-4fdb-81be-f8c57d2f6e0a.png" width="700" /></p><br>  

- 2019~2022 기간 동안의 지수 트렌드와 월별, 주별 변동량을 나타냈습니다.  
- 코스피 지수가 3월 말과 한주의 시작인 월요일에 많이 떨어지는 것을 확인할 수 있었습니다.  
- 이렇게 향후 KOSPI지수의 행보를 대략적으로 알아보았고, 트렌드와 월별, 주별 동향도 알아보았습니다. 이제 실제로 값을 예측해보도록 합시다.     

```py
#2019~2022 까지의 데이터를 사용한 결과 코스피지수의 예측
forecast_2019[['ds', 'yhat', 'yhat_lower', 'yhat_upper']].iloc[-15:]
```  
```
>> Out[44] 


                ds	       yhat	 yhat_lower	 yhat_upper
913	2022-09-14	2363.812642	2276.453019	2455.542920
914	2022-09-15	2359.269320	2272.205219	2453.624150
915	2022-09-16	2355.217647	2260.617968	2446.259285
916	2022-09-17	2352.057246	2262.644187	2441.262350
917	2022-09-18	2347.922472	2252.701098	2441.691746
918	2022-09-19	2339.581314	2251.434051	2430.593805
919	2022-09-20	2341.494508	2258.825601	2433.583536
920	2022-09-21	2334.967461	2243.451821	2430.275066
921	2022-09-22	2327.559200	2232.059805	2416.580543
922	2022-09-23	2320.743648	2232.000082	2411.908552
923	2022-09-24	2314.971382	2228.765209	2418.079241
924	2022-09-25	2308.425217	2224.299974	2403.270875
925	2022-09-26	2297.918064	2204.928287	2386.758705
926	2022-09-27	2297.951072	2211.819755	2394.883481
927	2022-09-28	2289.864683	2198.925061	2383.381680
```

- 사용한 데이터는 2022-09-13 까지의 데이터였습니다.  
- fbprophet 예측 모델을 통해서 향후 15일간의 코스피지수를 예상해보았습니다.  
- 2019~2022 까지의 데이터를 사용한 결과 코스피지수의 예측값은 아래와 같습니다.  
- 예측한 종가가 갈수록 낮아지는 추세이며, 상한가와 하한가의 차이가 꽤나 크게 예측되고 있음을 확인할 수 있습니다.  
  
***  

### 🏆 1.3. 2022년도 코스피 지수만을 사용한 예측  

📈 이어서 2022년도의 데이터만을 사용해 코스피지수를 예측해보았습니다.  
📈 동일하게 향후 15일 간의 지수를 예측하였으며, 이 예측 결과를 forecast_2022 객체에 저장하였습니다.  

```py
stock_2022 = kospi_2022
stock_2022['y'] = stock_2022['Close']
stock_2022['ds'] = stock_2022.index

m_2022 = Prophet()
m_2022.fit(stock_2022)

future_2022 = m_2022.make_future_dataframe(periods=15)
forecast_2022 = m_2022.predict(future_2022)

plt.figure(figsize=(15,7))
plt.plot(kospi_2022.index, kospi_2022["Close"], label="real")
plt.plot(forecast_2022["ds"], forecast_2022["yhat"], label="forecast")
plt.grid(True)
plt.legend()
plt.show()
```  
<p align="center"><img src="https://user-images.githubusercontent.com/65170165/210683422-df4ad3ac-fc46-4c44-b48b-b11ef836334f.png" width="1500" /></p><br>    

- 앞서 살펴본 2019~2022년도의 결과와 비교해보면 실제값과 예측값의 차이가 꽤나 크게 나고 있습니다.  
- 그럼에도 어느 정도 추세는 잘 따라가는 모습을 볼 수 있는데, 이는 상대적으로 데이터의 양이 적기 때문일 것이라 생각합니다.  

```py
# 트렌드와 요일별 지수 변동
m_2022.plot_components(forecast_2022);
```  
<p align="center"><img src="https://user-images.githubusercontent.com/65170165/210683698-b865f0d8-7666-453b-b1bc-f4aeb2edc4db.png" width="750" /></p><br>  

- 이번에도 트렌드와 요일별 지수 변동을 살펴보았습니다.  
- 2022년도만 보면 7월에 지수가 정말 많이 떨어졌고, 월요일만 특히 낮았던 지난 3년과 달리 주중 내내 지수가 마이너스인 경우를 확인 할 수 있습니다.  
- 지수가 쉽게 회복하지 못하고 있는 현상을 위의 동향을 바탕으로 알아볼 수도 있을 것 같습니다.  
- 이제 2022년도 데이터로 지수를 예측해보도록 합시다!!  
  
```py
forecast_2022[['ds', 'yhat', 'yhat_lower', 'yhat_upper']].iloc[-15:]
```  
```
>> Out[49]

	        ds	       yhat	 yhat_lower	 yhat_upper
171	2022-09-14	2483.494673	2417.317779	2546.866758
172	2022-09-15	2482.589250	2418.292829	2541.825097
173	2022-09-16	2492.464880	2428.540714	2556.977455
174	2022-09-17	2503.760585	2439.685602	2567.591851
175	2022-09-18	2505.333261	2435.380120	2568.248356
176	2022-09-19	2488.467385	2423.058140	2553.402026
177	2022-09-20	2492.644270	2428.006095	2559.014198
178	2022-09-21	2494.503400	2431.314316	2559.545292
179	2022-09-22	2493.597977	2430.479788	2558.036223
180	2022-09-23	2503.473607	2437.839679	2566.018739
181	2022-09-24	2514.769311	2447.731730	2576.440760
182	2022-09-25	2516.341988	2449.493558	2584.204460
183	2022-09-26	2499.476112	2430.244812	2562.016261
184	2022-09-27	2503.652996	2437.587989	2567.562299
185	2022-09-28	2505.512127	2440.712487	2573.243216
```  
***  

### 🏆 1.4. 2019~2022 데이터 예측값과 2022년 데이터 예측값 비교  

📈 두 데이터로부터 나온 예측값들을 비교해봅시다.  

```py
# 예측값으로부터 마지막 20일간의 수치만 가져옴
df_2019 = forecast_2019[['ds', 'yhat']].iloc[-20:]
df_2022 = forecast_2022[['ds', 'yhat']].iloc[-20:]
df_2019 = df_2019.set_index('ds')
df_2022 = df_2022.set_index('ds')
df_2022

# 각 예측을 병합하여 새로운 데이터프레임인 forecast를 생성함
# column yhat_x가 2019~2022 지수를, yhat_y가 2022 지수를 반영한 예측값임

forecast = pd.merge(df_2019, df_2022, how = 'inner', left_index = True, right_index = True)
forecast
```  
```
>> Out[66]

	             yhat_x	     yhat_y
        ds		
2022-09-05	2369.353959	2466.449932
2022-09-06	2375.783320	2470.626816
2022-09-07	2374.069666	2472.485947
2022-09-08	2371.692720	2471.580523
2022-09-13	2367.426042	2481.635543
2022-09-14	2363.812642	2483.494673
2022-09-15	2359.269320	2482.589250
2022-09-16	2355.217647	2492.464880
2022-09-17	2352.057246	2503.760585
2022-09-18	2347.922472	2505.333261
2022-09-19	2339.581314	2488.467385
2022-09-20	2341.494508	2492.644270
2022-09-21	2334.967461	2494.503400
2022-09-22	2327.559200	2493.597977
2022-09-23	2320.743648	2503.473607
2022-09-24	2314.971382	2514.769311
2022-09-25	2308.425217	2516.341988
2022-09-26	2297.918064	2499.476112
2022-09-27	2297.951072	2503.652996
2022-09-28	2289.864683	2505.512127
```  

- 두 예측값을 시각화를 통해 비교해봅시다🙂.  

```py
plt.figure(figsize=(15,7))
plt.plot(forecast.index, forecast["yhat_x"], label="2019-2022")
plt.plot(forecast.index, forecast["yhat_y"], label="2022")
plt.grid(True)
plt.legend()
plt.show()
```  
<p align="center"><img src="https://user-images.githubusercontent.com/65170165/210684979-07b5061a-f78d-49c2-b017-392798002ca8.png" width="1500" /></p><br>  

- 비교 결과, 9월 5일부터 9월 28일까지 2022년도의 코스피 지수를 통해 예측한 결과가 3년간의 지수를 통해 예측한 결과보다 모두 상위에 있는 것을 알 수 있습니다.  
- 3년간의 데이터는 2020년과 2021년도의 지수가 압도적으로 높았기 때문에 계속해서 떨어지고 있는 2022년도의 지수에 좀 더 민감하게 반응하고 있는것이 아닐까 생각합니다.  
  
## 🏆 2. 결과 분석  

- 이렇게 plotly를 통해 시각화하고, fbprophet을 통해서 향후 15일 간의 코스피지수도 예측해보았습니다.  
   
- 이동평균선들을 사용해서 시각화해본 결과 아직 장기이동평균선과 단기이동평균선 사이의 차이가 커서 쉽게 코스피지수가 반등하기는 힘들어 보이는 것이 사실입니다. 하지만 이렇게 숫자로만 하는 시각화 예측과 달리 주가는 얽혀있는 상황이 참 많습니다. 데이터로는 표현할 수 없는 국제 정세와 사람들의 소비 심리 하나하나가 주가에 정말 많은 영향을 미치기 때문에, 이런 시각화 모델 하나만 보고 주가가 오르지 않을거야!! 라고 생각하실 필요는 전혀 없습니다. 숫자가 항상 참을 말하는 건 아니니까요.  
- fbprophet 모듈을 사용해서 진행한 예측은 3년간의 데이터와 2022년의 데이터를 사용했을 때 서로 생각보다 큰 차이를 보여주었습니다. 코스피 지수가 한창 최고점을 찍고 있었을 때의 데이터를 학습한 것과, 계속 떨어지고 있는 데이터를 학습한 것이 차이를 만든 가장 큰 이유가 아닐까 생각합니다. 전체적으로 호황일 때의 데이터를 학습하면 아무래도 모델 자체가 높은 수치에는 적은 가중치를 두고, 낮은 수치에는 높은 가중치를 둘테니 지속적으로 떨어지는 중인 2022년도의 낮은 수치에 민감하게 반응하기 때문일 것입니다. 2022년도의 데이터를 학습한 경우는 정반대라고 생각하면 되실 것 같습니다!!  
- 하지만 말했듯이 수치가 항상 모든 것을 말해주는 것은 아닙니다. 심지어 코스피지수는 우리나라 기업의 대부분의 정보를 담고 있으니, 세세한 움직임에 반응하기는 더 어려울 수 있지 않을까 합니다. 처음 해본 시계열 분석이라 많이 부족했던 것 같습니다. 이러한 경험을 바탕으로, 앞으로 더 많은 분석을 통해 발전해나가고 싶습니다🙂.  

***



 
