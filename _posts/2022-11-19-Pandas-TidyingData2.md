---
layout: single
title: \[판다스 기초] 데이터 정리 (2)
categories:
  - Pandas
tags:
  - [python, pandas, tidying data]
toc: true
toc_sticky: true
date: 2022-11-19
---

데이터 분석 목적에 따라, 컬럼 <-> 행 변화로 테이블 형태 자유롭게 변형 가능

**Table 1.**     피어슨 테스트 적합

|            | Pregnant | Not pregnant |
|-----------:|:--------:|:------------:|
| **Male**   |     0    |      5       |
| **Female** |     1    |      4       |

**Table 2.**     regression에 적합

| Gender  | Pregnant | Count |
|:-------:|:--------:|:-----:|
| Male    | Yes      | 0     |
| Male    | No       | 5     |
| Female  | Yes      | 1     |
| Female  | No       | 4     |

-열: variable, 행: observation 나타냄. (값은 범주형/연속형)\
-분석하고자 하는 방향에 따라 열 또는 행을 다른 형태로 변형할 수 있어야한다.


```python
import pandas as pd
from io import StringIO
```


```python
A_csv = """city,year,cases
A,2010,745
B,2010,37737
C,2010,212258
A,2020,2666
B,2020,80488
C,2020,213766"""

with StringIO(A_csv) as fp:
    A = pd.read_csv(fp)
```


```python
B_csv = """city,year,population
A,2010,1235795
B,2010,56837737
C,2010,256812258
A,2020,2665476
B,2020,82014878
C,2020,2117376956"""

with StringIO(B_csv) as fp:
    B = pd.read_csv(fp)
```

### `.merge()`
-병합: 두 DF를 각 데이터에 존재하는 고유값(key)를 기준\
-디폴트 설정: pd.merge(df_left, df_right, how='inner', on=None)\
-outer 합집합, inner 교집합 의미


```python
C = A.merge(B, on=['city', 'year'])  # 공통 key 두개 기준, 교집합 조인
C
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>year</th>
      <th>cases</th>
      <th>population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>2010</td>
      <td>745</td>
      <td>1235795</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>2010</td>
      <td>37737</td>
      <td>56837737</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>2010</td>
      <td>212258</td>
      <td>256812258</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>2020</td>
      <td>2666</td>
      <td>2665476</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>2020</td>
      <td>80488</td>
      <td>82014878</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>2020</td>
      <td>213766</td>
      <td>2117376956</td>
    </tr>
  </tbody>
</table>
</div>



### `.join()`
`inner-join(A, B)`디폴트: on-keys가 매칭되는 행만 남김\
`outer-join(A, B)`: 합집합, on-keys가 매칭되지 않으면 NaN값으로 채움\
`left-join(A, B)`: A의 모든 행을 남기고 B는 A와 매칭되는 on-keys의 행만 결합\
`right-join(A, B)`: B의 모든 행을 남기고 A는 B와 매칭되는 on-keys의 행만 결합


```python
with StringIO("""a,b,c
bug,4,x
rug,2,x
lug,1,x
mug,3,x""") as fp:
    D = pd.read_csv(fp)

with StringIO("""a,b,d
hug,-1,y
smug,-2,y
rug,-3,y
tug,-4,y
bug,4,y""") as fp:
    E = pd.read_csv(fp)
```


```python
D.merge(E, on=['a', 'b'])   # inner-join
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>bug</td>
      <td>4</td>
      <td>x</td>
      <td>y</td>
    </tr>
  </tbody>
</table>
</div>




```python
D.merge(E, on=['a', 'b'], how='outer')  # outer-join
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>bug</td>
      <td>4</td>
      <td>x</td>
      <td>y</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rug</td>
      <td>2</td>
      <td>x</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>lug</td>
      <td>1</td>
      <td>x</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>mug</td>
      <td>3</td>
      <td>x</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>hug</td>
      <td>-1</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>5</th>
      <td>smug</td>
      <td>-2</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>6</th>
      <td>rug</td>
      <td>-3</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>7</th>
      <td>tug</td>
      <td>-4</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
  </tbody>
</table>
</div>




```python
D.merge(E, on=['a', 'b'], how='left')  # left-join
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>bug</td>
      <td>4</td>
      <td>x</td>
      <td>y</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rug</td>
      <td>2</td>
      <td>x</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>lug</td>
      <td>1</td>
      <td>x</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>mug</td>
      <td>3</td>
      <td>x</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
D.merge(E, on=['a', 'b'], how='right') # right-join
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>hug</td>
      <td>-1</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>1</th>
      <td>smug</td>
      <td>-2</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>2</th>
      <td>rug</td>
      <td>-3</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tug</td>
      <td>-4</td>
      <td>NaN</td>
      <td>y</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bug</td>
      <td>4</td>
      <td>x</td>
      <td>y</td>
    </tr>
  </tbody>
</table>
</div>



### `apply()`


```python
CC=C.copy()
CC['year']=CC['year'].apply(lambda x: "`{:02d}".format(x % 100))
CC
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>year</th>
      <th>cases</th>
      <th>population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>`10</td>
      <td>745</td>
      <td>1235795</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>`10</td>
      <td>37737</td>
      <td>56837737</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>`10</td>
      <td>212258</td>
      <td>256812258</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A</td>
      <td>`20</td>
      <td>2666</td>
      <td>2665476</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B</td>
      <td>`20</td>
      <td>80488</td>
      <td>82014878</td>
    </tr>
    <tr>
      <th>5</th>
      <td>C</td>
      <td>`20</td>
      <td>213766</td>
      <td>2117376956</td>
    </tr>
  </tbody>
</table>
</div>




```python
# x % 100 :  x를 100으로 나눈 나머지
103%100
```




    3




```python
# {:02d} : 최소 두자리수 만들기
"{:02}".format(2)
```




    '02'



**연습문제**


```python
# apply함수를 이용해서 비율을 구하기
def cal(F):
    assert 'cases' in F.columns and 'population' in F.columns
    ###
    def cal(F):
        return F['cases']/F['population']
    G = F.copy()
    G['proportion'] = G.apply(cal, axis=1)   # axis=1 열방향 적용
    return G
```


```python
# 정렬하기
def organize(F):
    
    # 컬럼명을 이름순으로 정렬
    var_names = sorted(F.columns)
    G = F[var_names].copy()
    
    # 각 행을 오름차순으로 정렬
    G.sort_values(by=var_names, inplace=True)
    
    # 인덱스를 재정렬
    G.reset_index(drop=True, inplace=True)
    
    return G
```


```python
# 두 테이블이 같은지 확인하는 함수 만들기 v.1
def check_equivalent(A, B):
    
    o_A = organize(A)
    o_B = organize(B)
    equal = (o_A == o_B)
    return equal.all().all()   # all()이 하나면 컬럼별로 T/F 출력
```


```python
a=pd.DataFrame({'a':[1,2], 'b':[5,7]})
b=pd.DataFrame({'a':[1,2], 'b':[4,7]})
(a==b).all()   # all()이 하나면 컬럼별로 T/F 출력
```




    a     True
    b    False
    dtype: bool



`.all()` \
반복 가능 객체의 모든 요소가 True면 True 반환. 하나라도 False면 False 반환 \
(dictionary의 경우, key가 True인지 체크)\
`.any()`\
반복 가능 객체의 요소가 하나라도 True면 True 반환


```python
# 두 테이블이 같은지 확인하는 함수 만들기 v.2
def check_equivalent(A, B):
    for c in A.columns:
        total = list(zip(A[c], B[c]))
        for t in total:
            if t[0] == t[1]:
                return True
            else:
                return False
```

### `Melting`
컬럼을 행으로 바꾸기


```python
a = pd.DataFrame({'cities':['a','b','c'], 2020:[123,456,789], 2021:[751,849,621]})
b = pd.DataFrame({'cities':['a','a','b','b','c','c'], 'year':[2020,2021,2020,2021,2020,2021],'cases':[123,751,456,849,789,621]})
display(a)
display(b)   # a 테이블을 b 테이블로 형태 변화
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cities</th>
      <th>2020</th>
      <th>2021</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>123</td>
      <td>751</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>456</td>
      <td>849</td>
    </tr>
    <tr>
      <th>2</th>
      <td>c</td>
      <td>789</td>
      <td>621</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cities</th>
      <th>year</th>
      <th>cases</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2020</td>
      <td>123</td>
    </tr>
    <tr>
      <th>1</th>
      <td>a</td>
      <td>2021</td>
      <td>751</td>
    </tr>
    <tr>
      <th>2</th>
      <td>b</td>
      <td>2020</td>
      <td>456</td>
    </tr>
    <tr>
      <th>3</th>
      <td>b</td>
      <td>2021</td>
      <td>849</td>
    </tr>
    <tr>
      <th>4</th>
      <td>c</td>
      <td>2020</td>
      <td>789</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>2021</td>
      <td>621</td>
    </tr>
  </tbody>
</table>
</div>



```python
def melt(df, list_col, key, value):
    # list_col: 바꿀 컬럼명들, key: 새로운 컬럼명, value: 값을 가져갈 컬럼명
    keep_vars = df.columns.difference(list_col) # 바꿀 컬럼명 제외한 나머지 컬럼들
    melted_sections = [] # 빈 리스트
    for c in list_col:   # 바꿀 컬럼 하나하나에 대해서 
        melted_c = df[keep_vars].copy() # DF와 동일한 컬럼들만 카피
        melted_c[key] = c   # melted_c['year'] = 2020 또는 2021
        melted_c[value] = df[c] # melted_c['cases'] = df[2020] 또는 df[2021]
        melted_sections.append(melted_c) # 빈 리스트에 확장
    melted = pd.concat(melted_sections) # 행 방향으로 데이터 결합
    return melted
```

### `Casting`
행을 병합


```python
a = pd.DataFrame({'cities':['a','a','b','b','c','c'], 'year':[2020,2021,2020,2021,2020,2021],'cases':[123,751,456,849,789,621]})
b = pd.DataFrame({'cities':['a','b','c'], 2020:[123,456,789], 2021:[751,849,621]})
display(a)
display(b)   # a 테이블을 b 테이블로 형태 변화
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cities</th>
      <th>year</th>
      <th>cases</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2020</td>
      <td>123</td>
    </tr>
    <tr>
      <th>1</th>
      <td>a</td>
      <td>2021</td>
      <td>751</td>
    </tr>
    <tr>
      <th>2</th>
      <td>b</td>
      <td>2020</td>
      <td>456</td>
    </tr>
    <tr>
      <th>3</th>
      <td>b</td>
      <td>2021</td>
      <td>849</td>
    </tr>
    <tr>
      <th>4</th>
      <td>c</td>
      <td>2020</td>
      <td>789</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>2021</td>
      <td>621</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cities</th>
      <th>2020</th>
      <th>2021</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>123</td>
      <td>751</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>456</td>
      <td>849</td>
    </tr>
    <tr>
      <th>2</th>
      <td>c</td>
      <td>789</td>
      <td>621</td>
    </tr>
  </tbody>
</table>
</div>



```python
def cast(df, key, value, join_how='outer'):
    fixed_vars = df.columns.difference([key, value])
    tibble = pd.DataFrame(columns=fixed_vars) # 빈 프레임
    # key, value: input df의 컬럼 중 새로운 테이블의 컬럼명/value가 될 값 

    cols = df[key].unique()  # df['year']의 고유값들
    for c in cols:
        df_c = df[df[key]==c] # df['year']==2020 or 2021인 
        del df_c[key]  #변수까지 완전 삭제
        df_c = df_c.rename(columns = {value:c}) #value에서 c로 컬럼명 변경
        tibble = tibble.merge(df_c, on=list(fixed_vars), how=join_how)
        # df의 남은 컬럼 기준 병합
    return tibble
```


```python
# 한 컬럼의 값을 두개 컬럼으로 나누기
a = pd.DataFrame({'product':['a','b'], 'year':[2020,2021],'rate':['123/751', '521/879']})
b = pd.DataFrame({'product':['a','b'], 'year':[2020,2021],'purchase':[123,521],'total':[751,879]})
display(a)
display(b)  # a 테이블을 b 테이블로 형태 변화
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product</th>
      <th>year</th>
      <th>rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2020</td>
      <td>123/751</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>2021</td>
      <td>521/879</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product</th>
      <th>year</th>
      <th>purchase</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2020</td>
      <td>123</td>
      <td>751</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>2021</td>
      <td>521</td>
      <td>879</td>
    </tr>
  </tbody>
</table>
</div>



```python
import re

def default_splitter(text): # 숫자를 string으로
    fields = re.findall('(\d+\.?\d+)', text)
    return fields

def separate(df, key, into, splitter=default_splitter):
    # key 컬럼을 into 컬럼들로 변형 (예: key 123/456 => into 123, intoo 456)
    #참고: http://stackoverflow.com/questions/16236684/apply-pandas-function-to-column-to-create-multiple-new-columns

    def apply_splitter(text):
        fields=splitter(text)  # % 나 $ 등 빼고 모두 string으로
        return pd.Series({into[i]:v for i, v in enumerate(fields)})
        # 바꿀 컬럼의 값을 인덱스: 값을 가진 dic -> 시리즈로
    keep_vars=df.columns.difference([key]) # 바꿀 컬럼 빼고 나머지
    t1=df[keep_vars].copy() 
    t2=df[key].apply(apply_splitter) # 바꿀 컬럼을 apply 이용해서 변형
    return pd.concat([t1, t2], axis=1) # 동일한 형태 DF를 열 방향으로 합치기
```


```python
# 두 컬럼의 값을 한개 컬럼으로 합치기
a = pd.DataFrame({'product':['a','b'], 'year':[2020,2021],'purchase':[123,521],'total':[751,879]})
b = pd.DataFrame({'product':['a','b'], 'year':[2020,2021],'rate':['123/751', '521/879']})
display(a)
display(b)  # a 테이블을 b 테이블로 형태 변화
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product</th>
      <th>year</th>
      <th>purchase</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2020</td>
      <td>123</td>
      <td>751</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>2021</td>
      <td>521</td>
      <td>879</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>product</th>
      <th>year</th>
      <th>rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>2020</td>
      <td>123/751</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>2021</td>
      <td>521/879</td>
    </tr>
  </tbody>
</table>
</div>



```python
def str_join_elements(y, sep=""):
    assert type(sep) is str
    yy = sep.join([str(yi) for yi in y])  # string으로 바꾸고 join
    return yy

def unite(df, old_col, new_col, combine=str_join_elements):
    # 참고: http://stackoverflow.com/questions/13331698/how-to-apply-a-function-to-two-columns-of-pandas-dataframe
    keep = df.columns.difference(old_col)
    d = df[keep].copy()
    d[new_col]=df[old_col].apply(combine, axis=1)
    return d
```


```python
# NaN값 제거하고 모든 컬럼값을 integer로 변형하기
from math import isnan  # NaN 값 체크
def remove_nan(df):
    df2 = df[df['a'].apply(lambda x: not isnan(x))] # NaN이 아닌 값만 선택
    df2['a'] = df2['a'].apply(lambda x: int(x)) # a 컬럼의 값을 integer로 변형
    return df2
```


```python
# 예: 'new_sn_m065'등의 값을 가진 'who'컬럼을 여러 컬럼으로 나누기
import re
def divide_who(text):
    m = re.match("^new_?(rel|sn|sp)_(f|m)(\\d{2,4})$", text)
    if m is None or len(m.groups()) != 3: # None이거나 3개 미만일 때
        return ['', '', '']

    fields = list(m.groups()) # 3개 그룹
    if fields[1] == 'f':    
        fields[1] = 'female'
    elif fields[1] == 'm':
        fields[1] = 'male'
    if fields[2] == '020':
        fields[2] = '0-20'
    elif fields[2] == '60':
        fields[2] = '60+'
    elif len(fields[2]) == 4 and fields[2].isdigit(): # 길이 4이고 digit이면
        fields[2] = fields[2][0:2] + '-' + fields[2][2:4] # 첫~두번째값-세번째값
    return fields

# df3 = separate(df,
#                 key='who', # 바꿀 컬럼
#                 into=['type', 'gender', 'age_group'], # 세 컬럼으로
#                 splitter=divide_who)
```
