
# findbillion  <br>
findbillion想利用統計和AI等方法估算目標價, 以價值型分析為主 <br>
之前的版本大多用matlab和C#做演算法開發<br>
這個版本重寫優化演算法並大量驗證, 嘗試優化準確度, 並且開源<br>
<br>
舊的桌機版可在此處下載 http://www.findbillion.com <br>
演算法從2012年之後就沒有更新<br>
<br>
2020/05/22其中一個帳戶績效如下, 累積報酬約150%, 以長線布局為主, 個股大多為2012年之後進場  <br>
![](./doc/performance/20200522_performance_1.jpg) 
![](./doc/performance/20200522_performance_2.jpg) 
![](./doc/performance/20200522_performance_3.jpg) <br>
 
## 程式碼採用平台如下: <br>
Python 3.6.10 以上 <br>
 <br>
 
## 目錄結構  <br>
data: 相關的數據與資料, 包括2012~2019年部分資產負債表, 損益表, 現金流量表, 月營收, 除權息資料, 月K線  <br>
findbillion: 資料讀取, 財務比率計算與預估  <br>
analysis: 個股分析, 與全面的分析, 含EPS預估以及現金股利預估 <br>
 <br> 
 <br>

## 預估EPS (以2330台積電為例):  <br>
程式碼如analysis/eps/run_est_eps_2330.py <br>
<br>
### 方法1: 利用平均稅後盈餘淨利率
台灣上市櫃公司有一個特點就是會每月公布月營收, 於是我們利用月營收資訊預估EPS <br> 
預估EPS = (平均近4季的稅後盈餘淨利率 x 近12個月的月營收)/股本  <br> 

以預估台積電2013~2019年的EPS為例, 預估誤差都在5%以下

|  年份  |	2013   |	2014  |	2015	  |  2016	|  2017 |	2018	 | 2019   |
| -------| ------| ------ | ------| ------| ------| ------| ------ |
| 正確值 |	7.26  |	10.18  | 11.82 |	12.89 |	13.23 |	13.54 |	13.32  |
| 預測值 |	7.2	  |  9.72  |	11.84 |	12.56 |	13.41 |	13.64 |	12.87  |
| 誤差	  | 0.48% |	-4.49% |	0.17% |	-2.53 |	1.37% |	0.73% |	-3.34% |

 <br>

### 方法2: (建構中)
<br>
<br>

## 預估現金股利 (以2330台積電為例):  <br>
程式碼如analysis/yield/run_est_yield_2330.py <br>

### 方法1: 過去5年平均股利
這是市面上最多人採用的方法, 存股常見的方法<br> 
以台積電為例, 2013~2017年股利如下
![](./doc/2330/cash_divide_2013_2017.png)  <br> 

(圖表來源: 財報狗)<br>
<br>
2018年發佈股利: 8.00元 <br>
過去五年平均股利: 5.70元 <br>
**預估誤差: -28.75%**  <br>
**用過去5年平均股利的方法在公司成長時會低估, 公司衰退時會低估** <br>
<br>

### 方法2: 預估EPS+平均過去5年配息率 <br>
公司的配息率比較固定, 因此可以使用平均配息率做預估 <br>
加上前面EPS預估的方法可推估現金配息 <br>
<br>
2018年發佈股利: 8.00元 <br>
過去五年平均股利:  6.48元 <br>
**預估誤差: -19.03%** <br>
**相較方法1, 誤差有較縮小, 但誤差還是不低**<br>
<br>
### 方法3: 預估EPS+預估配息率 <br>
因為EPS變高時, 通常會提高配息率, 因此建立一簡單回歸模型做配息率的預估
2018年發佈股利: 8.00元 <br>
過去五年平均股利: 7.86元 <br>
**預估誤差: -1.71%** <br>
**預估的誤差值跟前面方法相比大幅減少** <br>
<br>

## 預估EPS (2013~2019年的個股驗證):  <br>
程式碼如analysis/eps/run_est_eps.py <br>
結果如result目錄下  <br>
<br>

### 方法1: 利用平均稅後盈餘淨利率 (全部個股) 
預估EPS = 平均近4季的稅後盈餘淨利率 x 近12個月的月營收  <br> 
驗證2013~2019, **EPS預測+-10%內比例大約在35%上下, 並不高**<br> 

|  年份            | 2013   |  2014  |  2015  |  2016  |  2017  |  2018  |  2019  |
| ---------------- | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| 誤差+-10%內比例   | 35.16% | 39.06% | 37.76% | 35.73% | 34.49% | 35.83% | 36.24% |

<br>


### 方法1: 利用平均稅後盈餘淨利率 (ROE>15%) 
加入ROE>15%的條件<br> 
驗證2013~2019, **EPS預測+-10%內比例大約在60%上下** <br> 
**ROE>15%的個股預估較為準確**

|  年份            | 2013   |  2014  |  2015  |  2016  |  2017  |  2018  |  2019  |
| ---------------- | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| 誤差+-10%內比例   | 60.84% | 64.11% | 62.38% | 63.36% | 58.07% | 65.53% | 66.21% |

<br>

## 預估現金股利 (2012~2018年的個股驗證):  <br>
程式碼如analysis/yield/run_est_yield.py <br>  

### 方法1: 過去5年平均股利 (ROE>15%) <br> 
驗證2013~2018, **現金股利預測+-20%內比例大約在30%上下, 並不高**<br> 

|  年份            | 2013   |  2014  |  2015  |  2016  |  2017  |  2018  |
| ---------------- | ------ | ------ | ------ | ------ | ------ | ------ |
| 誤差+-10%內比例   | 34.26% | 30.83% | 28.22% | 33.73% | 36.02% | 40.17% |

<br>

### 方法2: 預估EPS+平均過去5年配息率 (ROE>15%) <br>
驗證2013~2018, **現金股利預測+-20%內比例大約在35-40%左右, 稍微提升, 但也並不高**<br> 

|  年份            | 2013   |  2014  |  2015  |  2016  |  2017  |  2018  |
| ---------------- | ------ | ------ | ------ | ------ | ------ | ------ |
| 誤差+-10%內比例   | 34.66% | 36.25% | 36.30% | 41.30% | 42.98% | 40.79% |

<br>


### 方法3: 預估EPS+預估配息率 (ROE>15%) <br>
驗證2013~2018, **現金股利預測+-20%內比例大約在50-65%左右, 跟前面的方法相比大幅提升**<br> 

|  年份            | 2013   |  2014  |  2015  |  2016  |  2017  |  2018  |
| ---------------- | ------ | ------ | ------ | ------ | ------ | ------ |
| 誤差+-10%內比例   | 51.39% | 56.30% | 62.23% | 60.89% | 56.84% | 65.49% |


<br> 
<br>
備註: <br>
1. 資料僅供參考不做任何獲利保證及特定股票跟點位推薦建議<br>
2. 資料並不完整, 需要完整資料請至公開資訊觀測站<br>
3. 無論任何股市數據分析皆存在倖存者偏差<br>