---
layout: single
title: \[판다스 기초] 데이터 정리
categories:
  - Pandas
tags:
  - [python, pandas, tidying data]
toc: true
toc_sticky: true
date: 2022-10-11
---

판다스 라이브러리는 티블(tibbles),데이터프레임(dataframe),시리즈(series) 등 데이터 처리를 위한 모듈. 


```python
import pandas as pd  # 판다스 로딩 및 버젼 확인
print("=== pandas version: {} ===\n".format(pd.__version__))
import sys
print("=== Python version ===\n{}".format(sys.version))
```

    === pandas version: 1.4.3 ===
    
    === Python version ===
    3.9.12 (main, Apr  4 2022, 05:22:27) [MSC v.1916 64 bit (AMD64)]
    

판다스는 2D 테이블 형식의 `DataFrame`, 데이터프레임의 한개 컬럼 형식의 `Series`를 주로 다룬다.


```python
from pandas import DataFrame, Series
```

## `Series` 

티블의 변수를 저장할 때 쓰는 컬럼 형식.


```python
obj = Series([1, -2, 3, -4, 5])  
obj
# 결과: 인덱스와 dtype: int64의 값 확인
```




    0    1
    1   -2
    2    3
    3   -4
    4    5
    dtype: int64



`Series`는 파이썬의 `list`보다 처리 속도가 빠르다.

string과 int, float이 섞이면 `Series`의 `dtype`은 `object`


```python
obj2 = Series(['1', -2, '3', -4, 5])
obj2
```




    0     1
    1    -2
    2     3
    3    -4
    4     5
    dtype: object




```python
print(obj.dtype)
print(obj2.dtype)
```

    int64
    object
    

`n`이 `Series`의 length일 때 `Series`는 `range(0, n)`, 즉 0 ~ n-1까지의 인덱스를 가진다.


```python
list(obj.index)
```




    [0, 1, 2, 3, 4]




```python
obj[1:4]
```




    1   -2
    2    3
    3   -4
    dtype: int64




```python
I = [0, 2, 3] # 인덱스 설정
obj[I] # obj[[0, 2, 3]]와 같음
```




    0    1
    2    3
    3   -4
    dtype: int64




```python
I_index = obj < 0  # obj 값이 0보다 작은 인덱스이다. (조건)
I_index # bool 타입 보여줌
```




    0    False
    1     True
    2    False
    3     True
    4    False
    dtype: bool




```python
obj[I_index]
```




    1   -2
    3   -4
    dtype: int64



### `Series`를 딕셔너리처럼 만들기


```python
obj3 = Series([1, -2, 3, -4, 5, -6],
              ['a', 'b', 'c', 'd', 'e', 'f'])
obj3
```




    a    1
    b   -2
    c    3
    d   -4
    e    5
    f   -6
    dtype: int64




```python
dic = {'a': 1, 'c': 3, 'e': 5, 'b': -2, 'd': -4, 'f': -6}   #딕셔너리를 시리즈로 만들기
obj4 = Series(dic)
print(obj4)
```

    a    1
    c    3
    e    5
    b   -2
    d   -4
    f   -6
    dtype: int64
    


```python
index = [0, 2, 4] # 인덱스 리스트
obj4[index]
```




    a    1
    e    5
    d   -4
    dtype: int64




```python
names = ['c', 'e', 'f'] # 인덱스 값 리스트
obj4[names]
```




    c    3
    e    5
    f   -6
    dtype: int64




```python
I_index = obj4 < 0  # 조건 : 값<0
I_index
```




    a    False
    c    False
    e    False
    b     True
    d     True
    f     True
    dtype: bool




```python
obj4[I_index]  # 조건 성립하는 값들
```




    b   -2
    d   -4
    f   -6
    dtype: int64



### isin : 조건, 검색
딕셔너리처럼 `Series`도 `in` 사용가능. 하지만 대체로 `list`와 `Series`에서 `in`보다는 `Series.isin` 사용한다.


```python
'c' in obj4
```




    True



`Series`도 벡터처럼 연산가능하다.


```python
print(obj3, "\n")
print(obj3 + 5, "\n")
print(obj3 + 5 > 0, "\n")
print((-2.5 * obj3) + (obj3 + 5))
```

    a    1
    b   -2
    c    3
    d   -4
    e    5
    f   -6
    dtype: int64 
    
    a     6
    b     3
    c     8
    d     1
    e    10
    f    -1
    dtype: int64 
    
    a     True
    b     True
    c     True
    d     True
    e     True
    f    False
    dtype: bool 
    
    a     3.5
    b     8.0
    c     0.5
    d    11.0
    e    -2.5
    f    14.0
    dtype: float64
    

`Series` 객체도 인덱스를 기준으로 vector 스타일로 동작한다.


```python
print(obj3)
```

    a    1
    b   -2
    c    3
    d   -4
    e    5
    f   -6
    dtype: int64
    


```python
obj_l = obj3[[0,2,4]]
obj_l
```




    a    1
    c    3
    e    5
    dtype: int64




```python
obj3 + obj_l
```




    a     2.0
    b     NaN
    c     6.0
    d     NaN
    e    10.0
    f     NaN
    dtype: float64



지정하지않은 요소는 NaN값으로 출력된다. (_outer-join_ 과 유사)

### `.apply(fun)`
각 요소에 함수를 적용한 `Series`의 copy가 출력된다.


```python
abs(-7) # Python built-in function
```




    7




```python
obj3.apply(abs) # 모든 요소를 절대값으로 바꾸어 복사본 출력
```




    a    1
    b    2
    c    3
    d    4
    e    5
    f    6
    dtype: int64




```python
obj3 # apply 적용 후 복사본이 출력되고 원본데이터는 변하지 않는다.
```




    a    1
    b   -2
    c    3
    d   -4
    e    5
    f   -6
    dtype: int64



### `Series` 네이밍하기


```python
print(obj3.name)
```

    None
    


```python
obj3.name = 'test'
obj3
```




    a    1
    b   -2
    c    3
    d   -4
    e    5
    f   -6
    Name: test, dtype: int64



## `DataFrame` 

동일 인덱스를 가진 `Series` 컬럼들을 가진다. 


```python
member = DataFrame({'nick': ['jr', 'ky', 'mt', 'jt', 'nh', 'de', 'kt', 'nn'],
                   'id': [30324, 30312, 30316, 30317, 30306, 30308, 30303, 30318],
                   'name': ['jared', 'kelly', 'matt', 'jonathan', 'nhan', 'dale', 'kitti', 'nolan']})
print(type(member), member)
```

    <class 'pandas.core.frame.DataFrame'>   nick     id      name
    0   jr  30324     jared
    1   ky  30312     kelly
    2   mt  30316      matt
    3   jt  30317  jonathan
    4   nh  30306      nhan
    5   de  30308      dale
    6   kt  30303     kitti
    7   nn  30318     nolan
    


```python
display(member) # member만 입력해도 동일
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
      <th>nick</th>
      <th>id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>jr</td>
      <td>30324</td>
      <td>jared</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ky</td>
      <td>30312</td>
      <td>kelly</td>
    </tr>
    <tr>
      <th>2</th>
      <td>mt</td>
      <td>30316</td>
      <td>matt</td>
    </tr>
    <tr>
      <th>3</th>
      <td>jt</td>
      <td>30317</td>
      <td>jonathan</td>
    </tr>
    <tr>
      <th>4</th>
      <td>nh</td>
      <td>30306</td>
      <td>nhan</td>
    </tr>
    <tr>
      <th>5</th>
      <td>de</td>
      <td>30308</td>
      <td>dale</td>
    </tr>
    <tr>
      <th>6</th>
      <td>kt</td>
      <td>30303</td>
      <td>kitti</td>
    </tr>
    <tr>
      <th>7</th>
      <td>nn</td>
      <td>30318</td>
      <td>nolan</td>
    </tr>
  </tbody>
</table>
</div>



```python
member.columns  # 컬럼 확인 
```




    Index(['nick', 'id', 'name'], dtype='object')




```python
type(member['name']) # 각 컬럼은 Series
```




    pandas.core.series.Series




```python
member.index # 인덱스 확인
```




    RangeIndex(start=0, stop=8, step=1)




```python
member.index == member['nick'].index # 모두 동일 인덱스 가짐
```




    array([ True,  True,  True,  True,  True,  True,  True,  True])




```python
target_fields = ['name', 'id']  # 컬럼 지정
member[target_fields]
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
      <th>name</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>jared</td>
      <td>30324</td>
    </tr>
    <tr>
      <th>1</th>
      <td>kelly</td>
      <td>30312</td>
    </tr>
    <tr>
      <th>2</th>
      <td>matt</td>
      <td>30316</td>
    </tr>
    <tr>
      <th>3</th>
      <td>jonathan</td>
      <td>30317</td>
    </tr>
    <tr>
      <th>4</th>
      <td>nhan</td>
      <td>30306</td>
    </tr>
    <tr>
      <th>5</th>
      <td>dale</td>
      <td>30308</td>
    </tr>
    <tr>
      <th>6</th>
      <td>kitti</td>
      <td>30303</td>
    </tr>
    <tr>
      <th>7</th>
      <td>nolan</td>
      <td>30318</td>
    </tr>
  </tbody>
</table>
</div>



### 슬라이싱
기본적으로 행 단위로 적용된다.


```python
member[1::2] # 1부터 2칸씩 띄워서 출력
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
      <th>nick</th>
      <th>id</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>ky</td>
      <td>30312</td>
      <td>kelly</td>
    </tr>
    <tr>
      <th>3</th>
      <td>jt</td>
      <td>30317</td>
      <td>jonathan</td>
    </tr>
    <tr>
      <th>5</th>
      <td>de</td>
      <td>30308</td>
      <td>dale</td>
    </tr>
    <tr>
      <th>7</th>
      <td>nn</td>
      <td>30318</td>
      <td>nolan</td>
    </tr>
  </tbody>
</table>
</div>




```python
member2 = member[['name', 'nick']]
member2.index = member['id']  # 인덱스는 id 컬럼으로
member2.index.name = None
member2
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
      <th>name</th>
      <th>nick</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
    </tr>
  </tbody>
</table>
</div>



### `.loc`
인덱스의 값으로 해당 행 찾기


```python
member2.loc[[30312, 30308]]
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
      <th>name</th>
      <th>nick</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
    </tr>
  </tbody>
</table>
</div>



### `.iloc`
0-based 값인 디폴트 인덱스로 해당 행 찾기


```python
member2.iloc[[1, 3]]
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
      <th>name</th>
      <th>nick</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
    </tr>
  </tbody>
</table>
</div>



### 컬럼 추가하기


```python
member2['rating'] = 4.
member2['income'] = '$$'
member2
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
    </tr>
  </tbody>
</table>
</div>




```python
income_as_ints = member2['income'].apply(lambda x: len(x))  # $$의 길이를 integer로 표현
income_as_ints
```




    30324    2
    30312    2
    30316    2
    30317    2
    30306    2
    30308    2
    30303    2
    30318    2
    Name: income, dtype: int64




```python
member2['value'] = member2['rating'] / income_as_ints  # rating을 income으로 나눠 member들의 가치를 나타냄
member2
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



모든 컬럼이 동일 인덱스를 가진 `Series`이므로 value컬럼 생성이 가능했다.

matt와 jonathan의 income 증가로 `'$'`가 하나 더 늘었다고 가정하고 기존 dataframe을 copy하여 member3로 저장한다.


```python
member3 = member2.copy()
```


```python
hike = member3['name'].isin({'matt', 'jonathan'})
# 또는 hike = member3['name'].apply(lambda x: x in {'matt', 'jonathan'})
hike
```




    30324    False
    30312    False
    30316     True
    30317     True
    30306    False
    30308    False
    30303    False
    30318    False
    Name: name, dtype: bool




```python
member3[hike]
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# string 연결해서 $ 추가
s = '$$'
s += '$'
print(s)
```

    $$$
    


```python
# member3[hike]['income'] += '$' 하면 다음의 에러 메시지가 난다. 
# A value is trying to be set on a copy of a slice from a DataFrame.
# Try using .loc[row_indexer,col_indexer] = value instead
```


```python
member3
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



가로로 슬라이싱하면 데이터 원본이 아닌 복사본에 적용되어 원본 데이터의 하위 집합에 대한 참조가 없다.


```python
# .loc[row_indexer,col_indexer] = value
member3.loc[hike, 'income'] += '$'
member3
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
member4 = member2.copy() 
```

income이 증가한 해당 인덱스를 `Series`로 저장하고 string 연결을 하는 방법도 있다. 그러나 지정되지 않은 값들은 `NaN`으로 표시된다. 


```python
hike_id = member4.index[hike]
hike_mark = Series(['$'] * len(hike_id), index=hike_id)
print(hike_id)
print(hike_mark)
```

    Int64Index([30316, 30317], dtype='int64')
    30316    $
    30317    $
    dtype: object
    


```python
member4['income'] + hike_mark
```




    30303    NaN
    30306    NaN
    30308    NaN
    30312    NaN
    30316    $$$
    30317    $$$
    30318    NaN
    30324    NaN
    dtype: object



income 증가여부를 bool 타입 list로 만들어 '$'를 곱하고 string 연결한다.


```python
hike.tolist()   # True, False로 표시됨
```




    [False, False, True, True, False, False, False, False]




```python
[x * '$' for x in list(hike.tolist())]   # 각각의 요소에 '$'를 곱해줌
```




    ['', '', '$', '$', '', '', '', '']




```python
member4 = member2.copy()
member4['income'] += Series([x * '$' for x in hike.tolist()], index=hike.index)
member4
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



DataFrame에서는 대체로 `apply()` 함수를 사용한다.


```python
help(repr)  # 해당 객체의 원래값을 재현할 수 있도록한 개발자를 위한 함수
```

    Help on built-in function repr in module builtins:
    
    repr(obj, /)
        Return the canonical string representation of the object.
        
        For many object types, including most builtins, eval(repr(obj)) == obj.
    
    


```python
member4.apply(lambda x: repr(type(x)))  # 행 방향
```




    name      <class 'pandas.core.series.Series'>
    nick      <class 'pandas.core.series.Series'>
    rating    <class 'pandas.core.series.Series'>
    income    <class 'pandas.core.series.Series'>
    value     <class 'pandas.core.series.Series'>
    dtype: object




```python
member4.apply(lambda x: repr(type(x)), axis=1) # 열 방향
```




    30324    <class 'pandas.core.series.Series'>
    30312    <class 'pandas.core.series.Series'>
    30316    <class 'pandas.core.series.Series'>
    30317    <class 'pandas.core.series.Series'>
    30306    <class 'pandas.core.series.Series'>
    30308    <class 'pandas.core.series.Series'>
    30303    <class 'pandas.core.series.Series'>
    30318    <class 'pandas.core.series.Series'>
    dtype: object



### `apply()`를 이용한 값 수정
'value'값을 수정해보자.


```python
def update_value(row):
    return row['rating'] / len(row['income'])

member4['value'] = member4.apply(update_value, axis=1) # 열 방향 동작
member4
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
  </tbody>
</table>
</div>



### `.concat()`
데이터프레임 결합하기


```python
# income에 따라 분할하기
low = member4['income'] <= '$$'  # '$$'보다 작은 income을 low라고 지정
member_low = member4[low]
member_high = member4[~low]      # ~ low: 조건이 low가 아닌 것

display(member_high)
display(member_low)
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



```python
# 결합
pd.concat([member_high, member_low])
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
  </tbody>
</table>
</div>



### 인덱스 객체


```python
from pandas import Index
```

```python
print(member4.index)
member4.index.isin([30324, 30317])   # 해당 인덱스인지 아닌지 bool로 출력
```

    Int64Index([30324, 30312, 30316, 30317, 30306, 30308, 30303, 30318], dtype='int64')
    




    array([ True, False, False,  True, False, False, False, False])



### `index.union()`
인덱스 추가


```python
member4.index.union([30000])  # 인덱스 추가
```




    Int64Index([30000, 30303, 30306, 30308, 30312, 30316, 30317, 30318, 30324], dtype='int64')



### `index.difference()`
인덱스 제외, 삭제


```python
member4.index.difference([30312, 30318, 30303])  #인덱스 제외
```




    Int64Index([30306, 30308, 30316, 30317, 30324], dtype='int64')



### 인덱스 변경


```python
member5 = member4.reindex(Index([30308, 30313, 30000, 30312, 30317]))  # 새로운 인덱스 순으로 정렬 (없는인덱스는 NaN)

display(member4)
display(member5)
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30324</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30316</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
    <tr>
      <th>30306</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30303</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30318</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30308</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30313</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>30000</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>30312</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>30317</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
    </tr>
  </tbody>
</table>
</div>


### 인덱스 리셋
디폴트 인덱스값으로 되돌리기 


```python
member6 = member4.reset_index(drop=True)    # 원본데이터의 인덱스 drop
member6['id'] = member4.index      # 원본데이터의 인덱스는 'id'컬럼으로
member6
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
      <th>name</th>
      <th>nick</th>
      <th>rating</th>
      <th>income</th>
      <th>value</th>
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>jared</td>
      <td>jr</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
      <td>30324</td>
    </tr>
    <tr>
      <th>1</th>
      <td>kelly</td>
      <td>ky</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
      <td>30312</td>
    </tr>
    <tr>
      <th>2</th>
      <td>matt</td>
      <td>mt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
      <td>30316</td>
    </tr>
    <tr>
      <th>3</th>
      <td>jonathan</td>
      <td>jt</td>
      <td>4.0</td>
      <td>$$$</td>
      <td>1.333333</td>
      <td>30317</td>
    </tr>
    <tr>
      <th>4</th>
      <td>nhan</td>
      <td>nh</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
      <td>30306</td>
    </tr>
    <tr>
      <th>5</th>
      <td>dale</td>
      <td>de</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
      <td>30308</td>
    </tr>
    <tr>
      <th>6</th>
      <td>kitti</td>
      <td>kt</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
      <td>30303</td>
    </tr>
    <tr>
      <th>7</th>
      <td>nolan</td>
      <td>nn</td>
      <td>4.0</td>
      <td>$$</td>
      <td>2.000000</td>
      <td>30318</td>
    </tr>
  </tbody>
</table>
</div>



#### 참고: [Python for Data Analysis (2nd ed.)](http://shop.oreilly.com/product/0636920050896.do)
