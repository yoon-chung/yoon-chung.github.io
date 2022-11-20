# Numpy
Numerical Python : Array 연산을 지원하는 패키지 (Matrix, Vector 등 사용하는 선형대수 계산) 


```python
import numpy as np
```

## Numpy Array 만들기

### Simple Array


```python
a = np.array([8,9,12,10])
print(a)
```

    [ 8  9 12 10]
    

### `np.arange()`


```python
np.arange(5)  # range와 동일 - 0 ~ 4까지
```




    array([0, 1, 2, 3, 4])



### 다차원 Array


```python
# 2차원 배열 (3행 x 2열)
a = np.array([[1,2], [3,4],[5,6]])
print(a)
```

    [[1 2]
     [3 4]
     [5 6]]
    


```python
print(a.ndim)  # 몇 차원인지
print(a.shape) # 몇 행 x 몇 열인지
print(len(a))  # 행의 개수
```

    2
    (3, 2)
    3
    


```python
a = [[12, 13, 14, 15],[4, 5, 6, 7],[20, 21, 22, 23]]
b = [[0, 1, 2, 3],[16, 17, 18, 19],[8, 9, 10, 11]]

c = np.array([a, b])

print(c)
print(c.ndim)  # 3차원
print(c.shape) # 2개  x 3행 x 4열
print(len(c))  # 전체 array안의 개수
```

    [[[12 13 14 15]
      [ 4  5  6  7]
      [20 21 22 23]]
    
     [[ 0  1  2  3]
      [16 17 18 19]
      [ 8  9 10 11]]]
    3
    (2, 3, 4)
    2
    

### `zeros` `ones` `eye` `diag`


```python
print(np.zeros((2,3)))  # 0으로 채운 2행 x 3열
```

    [[0. 0. 0.]
     [0. 0. 0.]]
    


```python
np.zeros((2,3)).dtype  # 데이터타입은 float
```




    dtype('float64')




```python
print(np.ones((2,3)))  # 1로 채운 2행 x 3열
```

    [[1. 1. 1.]
     [1. 1. 1.]]
    


```python
np.ones((2,3)).dtype  # 데이터타입은 float
```




    dtype('float64')




```python
print(np.eye(3))  # 3행 x 3열의 대각선을 1로 채우기
```

    [[1. 0. 0.]
     [0. 1. 0.]
     [0. 0. 1.]]
    


```python
np.eye(3).dtype  # 데이터타입은 float
```




    dtype('float64')




```python
print(np.diag([1,2,3]))  # 3행 x 3열의 대각선을 리스트값으로 채우기
```

    [[1 0 0]
     [0 2 0]
     [0 0 3]]
    

zeros, ones, eye 함수 모두 dtype이 float

### `np.empty`


```python
a = np.empty((3, 2))  # 3행 x 2열의 빈 매트릭스
b = np.empty((3, 2), dtype=int) # 3행 x 2열의 int 타입의 빈 매트릭스
print(a)
print(b)
```

    [[1. 1.]
     [1. 1.]
     [1. 1.]]
    [[         0 1072693248]
     [         0 1072693248]
     [         0 1072693248]]
    

## 인덱싱, 슬라이싱


```python
print(c)
```

    [[[12 13 14 15]
      [ 4  5  6  7]
      [20 21 22 23]]
    
     [[ 0  1  2  3]
      [16 17 18 19]
      [ 8  9 10 11]]]
    


```python
print(c[0, 2, :]) # 첫번째 아이템의 3번째 행의 모든 열
```

    [20 21 22 23]
    

### `[:end]`
맨 처음부터 end 인덱스까지


```python
print(c[1, 0, :-1]) # 두번째 아이템의 첫번째 행의 맨뒤 열 제외하고 모든열
```

    [0 1 2]
    


```python
print(c[1, 0, ::-1]) # 두번째 아이템의 첫번째 행의 뒤에서부터 거꾸로 모든 열
```

    [3 2 1 0]
    


```python
d = np.array([[0,1,2,3,4,5],[10,11,12,13,14,15],[20,21,22,23,24,25],[30,31,32,33,34,35],[40,41,42,43,44,45],[50,51,52,53,54,55]])
print(d)  # d는 6행 x 6열 2차원 매트릭스
```

    [[ 0  1  2  3  4  5]
     [10 11 12 13 14 15]
     [20 21 22 23 24 25]
     [30 31 32 33 34 35]
     [40 41 42 43 44 45]
     [50 51 52 53 54 55]]
    


```python
d1 = d[0, 3:5] # 첫번째 행의 3~4번째
print(d1)
```

    [3 4]
    


```python
d2 = d[:, 2] # 모든 행의 3번째
print(d2)
```

    [ 2 12 22 32 42 52]
    

### ` :: `  
인덱스 증가폭 2 (2칸 간격)


```python
d3 = d[2::2, 0::2]  # 세번째 행부터 2개 간격으로 ~ 첫번째 열부터 2개 간격으로
print(d3)
```

    [[20 22 24]
     [40 42 44]]
    

### ` [start:] `
start 인덱스에서 마지막까지


```python
d4 = d[4:, 4:]  # 다섯번째 행부터 끝까지, 다섯번째 열부터 끝까지
print(d4)
```

    [[44 45]
     [54 55]]
    


```python
d4[:,:] = -d4[::-1, ::-1]  # 슬라이싱 후 수정하면 원본 array에 영향을 준다
print(d4)                  
```

    [[-55 -54]
     [-45 -44]]
    


```python
print(d)
```

    [[  0   1   2   3   4   5]
     [ 10  11  12  13  14  15]
     [ 20  21  22  23  24  25]
     [ 30  31  32  33  34  35]
     [ 40  41  42  43 -55 -54]
     [ 50  51  52  53 -45 -44]]
    


```python
d5 = d[4:, 4:].copy()  # .copy()하여 슬라이싱 후 수정하면 원본에 영향 없다
d5[:, :] = -d5[::-1, ::-1]
print(d5)
print(d)
```

    [[44 45]
     [54 55]]
    [[  0   1   2   3   4   5]
     [ 10  11  12  13  14  15]
     [ 20  21  22  23  24  25]
     [ 30  31  32  33  34  35]
     [ 40  41  42  43 -55 -54]
     [ 50  51  52  53 -45 -44]]
    

### 간접적 인덱싱


```python
y = np.random.randint(0,20,10)  # (0 ~ 20사이) 10개의 랜덤 integer
print(y)
```

    [14 14 17  8 12  9 10  2 19  3]
    


```python
index = np.array([2, 5, 8, 9])   # 인덱스를 array로 (3, 6, 9, 10번째)
print(y[index])
```

    [17  9 19  3]
    


```python
# d[i]가 7의 양의 배수일 때 d_7[i]가 True인 'd_7' array를 만들기

d_7 = (d > 0) & (d % 7 == 0)   # d이 양수, 7로 나눈 나머지 == 0인 조건
```

## 연산


```python
e = np.random.randint(-10, 10, size=(3, 2)) # (-10 ~ 10)까지 랜덤숫자를 3행x2열로
print(e)
```

    [[ -2  -5]
     [  0  -3]
     [-10  -6]]
    

### `max` `min` `sum` `mean` `std` `abs`


```python
print('np.max =', np.max(e), '; np.min =', np.min(e))
print('np.sum =', np.sum(e), '; np.mean =', np.mean(e))
print('np.std =', np.std(e))
```

    np.max = 0 ; np.min = -10
    np.sum = -26 ; np.mean = -4.333333333333333
    np.std = 3.197221015541813
    


```python
print('각 행의 max =', np.max(e, axis=1), '; 각 행의 min =', np.min(e, axis=1))
print('각 열의 max =', np.max(e, axis=0), '; 각 열의 min =', np.min(e, axis=0))
```

    각 행의 max = [-2  0 -6] ; 각 행의 min = [ -5  -3 -10]
    각 열의 max = [ 0 -3] ; 각 열의 min = [-10  -6]
    


```python
print(np.abs(e))  # 절대값
```

    [[ 2  5]
     [ 0  3]
     [10  6]]
    


```python
f = np.random.randint(-8, 8, size=(3, 2)) # (-8 ~ 8)까지 랜덤숫자를 3행x2열로
print(f)
```

    [[-8  5]
     [-3 -1]
     [-6  1]]
    

### shape이 같은 array끼리 비교 연산

### `np.maximum`
여러 array 비교 최대값 (np.max는 단일 array내 최대값)


```python
g = np.maximum(e, f)
print(g)
```

    [[-2  5]
     [ 0 -1]
     [-6  1]]
    

### shape이 다른 arrays끼리 비교 연산

### ` Broadcasting`


```python
e_means = np.mean(e, axis=1)
print(e_means)
```

    [-3.5 -1.5 -8. ]
    


```python
e - e_means  # 두 array간 shape 문제로 연산 불가
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    Input In [89], in <cell line: 1>()
    ----> 1 e - e_means
    

    ValueError: operands could not be broadcast together with shapes (3,2) (3,) 



```python
e_means2 = np.reshape(e_means, (len(e_means), 1))
print(e_means2)
print(e_means2.shape)   # 형태 변형
```

    [[-3.5]
     [-1.5]
     [-8. ]]
    (3, 1)
    


```python
e - e_means2
```




    array([[ 1.5, -1.5],
           [ 1.5, -1.5],
           [-2. ,  2. ]])


