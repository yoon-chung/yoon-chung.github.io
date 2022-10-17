---
layout: single
title: \[SQL] 기본문법과 응용예제
categories:
  - SQL
tags:
  - [sql, pandas]
toc: true
toc_sticky: true
date: 2022-10-17
---

## Tables and Basic Queries
행: observations (또는 items, records)\
열: variables (또는 attributes)


```python
import sqlite3 as db
conn = db.connect('example.db')  # database 연결
c = conn.cursor()  # 명령실행 위한 cursor()를 c로 임의 저장
```


```python
c.execute("DROP TABLE IF EXISTS Students")  # 이미 존재하는 'Student' 테이블있다면 삭제
c.execute("CREATE TABLE Students (id INTEGER, name TEXT)") # 2개 열 가진 테이블생성
```




    <sqlite3.Cursor at 0x2894e350880>



### INSERT INTO
item 추가


```python
c.execute("INSERT INTO Students VALUES (02, 'Andy')")
c.execute("INSERT INTO Students VALUES (09, 'Bill')")
c.execute("INSERT INTO Students VALUES (11, 'Chris')")
c.execute("INSERT INTO Students VALUES (13, 'Dan')")
```




    <sqlite3.Cursor at 0x2894e350880>



### excutemany()
item 추가


```python
# 튜플 리스트로 추가하기
add_students = [(14, 'Ethan'),
                 (22, 'Frank'),
                 (31, 'Gavin'),
                 (37, 'Henry'),
                 (42, 'James')]

c.executemany('INSERT INTO Students VALUES (?, ?)', add_students)
conn.commit()  # 변경내용을 database에 반영하겠다고 확정하는 명령어
```

### SELECT
조회


```python
c.execute("SELECT * FROM Students")
results = c.fetchall()
print("\nThe entries of Students:\n", results, "Results:", len(results))
```

    
    The entries of Students:
     [(2, 'Andy'), (9, 'Bill'), (11, 'Chris'), (13, 'Dan'), (14, 'Ethan'), (22, 'Frank'), (31, 'Gavin'), (37, 'Henry'), (42, 'James')] Results: 9
    

### Join
`INNER JOIN(A, B)` A와 B 교집합\
`OUTER JOIN(A, B)` A와 B 합집합 (missing value는 Null/SQL, NaN/Pandas로 채움)\
`LEFT JOIN(A, B)` A 기준\
`RIGHT JOIN(A, B)` B 기준

### Where
조건


```python
# Grades테이블 생성
c.execute('DROP TABLE IF EXISTS Grades')
c.execute('CREATE TABLE Grades (id INTEGER, course TEXT, grade REAL)')

add_items = [(9, "Arts", 4.0),
           (9, "Ethics", 3.0),
           (9, "History", 1.0),
           (13, "Arts", 4.0),
           (13, "Ethics", 4.0),
           (13, "History", 4.0),
           (31, "Arts", 2.0),
           (31, "History", 3.0)
           ]

c.executemany('INSERT INTO Grades VALUES (?, ?, ?)', add_items)
conn.commit()
```


```python
# 예제: Grades테이블에서 id 대신 Students테이블의 name을 불러오시오
query = '''
        SELECT Students.name, Grades.course, Grades.grade
        FROM Students, Grades
        WHERE Students.id = Grades.id
'''

for match in c.execute(query):
    print(match)
```

    ('Bill', 'Arts', 4.0)
    ('Bill', 'Ethics', 3.0)
    ('Bill', 'History', 1.0)
    ('Dan', 'Arts', 4.0)
    ('Dan', 'Ethics', 4.0)
    ('Dan', 'History', 4.0)
    ('Gavin', 'Arts', 2.0)
    ('Gavin', 'History', 3.0)
    


```python
# 예제: History과목의 (name, grade)으로 이루어진 리스트 생성하시오
query = '''
        SELECT Students.name, Grades.grade
        FROM Students, Grades
        WHERE Students.id = Grades.id and Grades.course = 'History'
'''
c.execute(query)
output = c.fetchall()
output
```




    [('Bill', 1.0), ('Dan', 4.0), ('Gavin', 3.0)]




```python
# 예제: Students테이블을 기준으로 LEFT JOIN하여 name, grade 선택하시오
query = '''
        SELECT Students.name, Grades.grade
        FROM Students LEFT JOIN Grades ON
        Students.id = Grades.id
'''
c.execute(query)
matches = c.fetchall()
for index, match in enumerate(matches):
    print(index, "=>", match)
```

    0 => ('Andy', None)
    1 => ('Bill', 1.0)
    2 => ('Bill', 3.0)
    3 => ('Bill', 4.0)
    4 => ('Chris', None)
    5 => ('Dan', 4.0)
    6 => ('Dan', 4.0)
    7 => ('Dan', 4.0)
    8 => ('Ethan', None)
    9 => ('Frank', None)
    10 => ('Gavin', 2.0)
    11 => ('Gavin', 3.0)
    12 => ('Henry', None)
    13 => ('James', None)
    

### Aggregations (그룹별 계산)
`AVG` `MIN` `MAX` `SUM` `COUNT`


```python
# id별로 평균 grade 계산
query = '''
        SELECT id, AVG(grade)
        FROM Grades
        GROUP BY id
'''
for match in c.execute(query):
    print(match)
```

    (9, 2.6666666666666665)
    (13, 4.0)
    (31, 2.5)
    

### Clean up
cursor, connection 닫기


```python
c.close()
conn.close()
```

## Data setup
참고 데이터: NYC 311 calls (datasets of complaints filed by residents of New York City via 311 calls)


```python
dataset = db.connect("C:/Users/yooni/GA/CSE6040/NYC-311-2M.db", isolation_level=None)  # DB파일 생성 및 연결
```

### LIMIT
부분 조회


```python
query = '''
        SELECT * FROM data
        LIMIT 4               
'''                             # 4개 행만 불러오기
import pandas as pd
df = pd.read_sql_query(query, dataset)
df.head()
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
      <th>index</th>
      <th>CreatedDate</th>
      <th>ClosedDate</th>
      <th>Agency</th>
      <th>ComplaintType</th>
      <th>Descriptor</th>
      <th>City</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>2015-09-15 02:14:04.000000</td>
      <td>None</td>
      <td>NYPD</td>
      <td>Illegal Parking</td>
      <td>Blocked Hydrant</td>
      <td>None</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2015-09-15 02:12:49.000000</td>
      <td>None</td>
      <td>NYPD</td>
      <td>Noise - Street/Sidewalk</td>
      <td>Loud Talking</td>
      <td>NEW YORK</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>2015-09-15 02:11:19.000000</td>
      <td>None</td>
      <td>NYPD</td>
      <td>Noise - Street/Sidewalk</td>
      <td>Loud Talking</td>
      <td>NEW YORK</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>2015-09-15 02:09:46.000000</td>
      <td>None</td>
      <td>NYPD</td>
      <td>Noise - Commercial</td>
      <td>Loud Talking</td>
      <td>BRONX</td>
    </tr>
  </tbody>
</table>
</div>



### DISTINCT
unique value 찾기 (고유값)


```python
query = 'SELECT DISTINCT Agency FROM data'
df = pd.read_sql_query(query, dataset)

print("Found {} unique Agencies. The first few are:".format(len(df)))
df.head()
```

    Found 50 unique Agencies. The first few are:
    




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
      <th>Agency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NYPD</td>
    </tr>
    <tr>
      <th>1</th>
      <td>DHS</td>
    </tr>
    <tr>
      <th>2</th>
      <td>DOT</td>
    </tr>
    <tr>
      <th>3</th>
      <td>DOHMH</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CHALL</td>
    </tr>
  </tbody>
</table>
</div>



### GROUP BY
그룹핑


```python
# ComplaintType별로 묶기
query = '''
    SELECT ComplaintType, City, Agency
    FROM data
    GROUP BY ComplaintType
'''
df = pd.read_sql_query(query, dataset)
print(df.shape)
df.head()
```

    (200, 3)
    




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
      <th>ComplaintType</th>
      <th>City</th>
      <th>Agency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AGENCY</td>
      <td>BRONX</td>
      <td>HPD</td>
    </tr>
    <tr>
      <th>1</th>
      <td>APPLIANCE</td>
      <td>BRONX</td>
      <td>HPD</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adopt-A-Basket</td>
      <td>Springfield Gardens</td>
      <td>DSNY</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Agency Issues</td>
      <td>None</td>
      <td>DOT</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Air Quality</td>
      <td>NEW YORK</td>
      <td>DEP</td>
    </tr>
  </tbody>
</table>
</div>



그룹별 집합/계산


```python
# ComplaintType별로 카운트한 7개 결과 출력
query = '''
    SELECT ComplaintType, COUNT(*)
    FROM data
    GROUP BY ComplaintType
    LIMIT 7
'''

df = pd.read_sql_query(query, dataset)
df
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
      <th>ComplaintType</th>
      <th>COUNT(*)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>AGENCY</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>APPLIANCE</td>
      <td>11263</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adopt-A-Basket</td>
      <td>50</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Agency Issues</td>
      <td>7428</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Air Quality</td>
      <td>8151</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Animal Abuse</td>
      <td>10614</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Animal Facility - No Permit</td>
      <td>78</td>
    </tr>
  </tbody>
</table>
</div>



### UPPER(), LOWER()
대문자/소문자/혼합형 변경


```python
query = '''
    SELECT UPPER(Agency), LOWER(ComplaintType), LOWER(Descriptor)
    FROM data
    GROUP BY LOWER(ComplaintType)
    LIMIT 10
'''

df = pd.read_sql_query(query, dataset)
df.head()
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
      <th>UPPER(Agency)</th>
      <th>LOWER(ComplaintType)</th>
      <th>LOWER(Descriptor)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>DSNY</td>
      <td>adopt-a-basket</td>
      <td>10a adopt-a-basket</td>
    </tr>
    <tr>
      <th>1</th>
      <td>HPD</td>
      <td>agency</td>
      <td>housing quality standards</td>
    </tr>
    <tr>
      <th>2</th>
      <td>DOT</td>
      <td>agency issues</td>
      <td>bike share</td>
    </tr>
    <tr>
      <th>3</th>
      <td>DEP</td>
      <td>air quality</td>
      <td>air: odor/fumes, vehicle idling (ad3)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NYPD</td>
      <td>animal abuse</td>
      <td>other (complaint details)</td>
    </tr>
  </tbody>
</table>
</div>



### HAVING
그룹화된 필드에 조건을 줄 때\
`WHERE` 기본적인 조건절로서 그룹핑 전 모든 필드에 조건 줄 수 있음


```python
# 혼합형과 대소문자 중복
query1 = '''
      SELECT ComplaintType, COUNT(*)
      FROM (SELECT DISTINCT ComplaintType FROM data)
      GROUP BY LOWER(ComplaintType)
      HAVING COUNT(*) >= 2
'''
df1 = pd.read_sql_query(query1, dataset)
df1
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
      <th>ComplaintType</th>
      <th>COUNT(*)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Elevator</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>PLUMBING</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



### IN
특정한 레코드 값 가져올때


```python
query = '''
      SELECT DISTINCT ComplaintType
      FROM data
      WHERE LOWER(ComplaintType) IN ("plumbing", "elevator")
'''
df = pd.read_sql_query(query, dataset)
df.head()
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
      <th>ComplaintType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>PLUMBING</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Elevator</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Plumbing</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ELEVATOR</td>
    </tr>
  </tbody>
</table>
</div>



### AS
테이블 열 이름변경


```python
query = '''
    SELECT Agency AS AGC, COUNT(*) AS NumComp
    FROM data
    GROUP BY Agency
'''
df = pd.read_sql_query(query, dataset)
df.head()
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
      <th>AGC</th>
      <th>NumComp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3-1-1</td>
      <td>1289</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ACS</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>AJC</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CAU</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CCRB</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



### ORDER BY
정렬 (내림차순 `-`, `DESC` 사용)


```python
query = '''
    SELECT Agency AS AGC, COUNT(*) AS NumComp
    FROM data
    GROUP BY UPPER(Agency)
    ORDER BY -NumComp
'''
# 또는 ORDER BY NumComplaints DESC 

df = pd.read_sql_query(query, dataset)
df.head()
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
      <th>AGC</th>
      <th>NumComp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>HPD</td>
      <td>640096</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NYPD</td>
      <td>340694</td>
    </tr>
    <tr>
      <th>2</th>
      <td>DOT</td>
      <td>322969</td>
    </tr>
    <tr>
      <th>3</th>
      <td>DEP</td>
      <td>181121</td>
    </tr>
    <tr>
      <th>4</th>
      <td>DSNY</td>
      <td>152004</td>
    </tr>
  </tbody>
</table>
</div>



### LIKE %word%
word가 포함된 value값을 모두 조회


```python
query = '''
    SELECT LOWER(ComplaintType) AS type, COUNT(*) AS freq
    FROM data
    WHERE LOWER(ComplaintType) LIKE '%noise%'
    GROUP BY type
    ORDER BY -freq'''

df_noisy = pd.read_sql_query(query, dataset)
print("'noise' 포함된 {}개 쿼리".format(len(df_noisy)))
df_noisy
```

    'noise' 포함된 8개 쿼리
    




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
      <th>type</th>
      <th>freq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>noise</td>
      <td>54165</td>
    </tr>
    <tr>
      <th>1</th>
      <td>noise - street/sidewalk</td>
      <td>48436</td>
    </tr>
    <tr>
      <th>2</th>
      <td>noise - commercial</td>
      <td>42422</td>
    </tr>
    <tr>
      <th>3</th>
      <td>noise - vehicle</td>
      <td>18370</td>
    </tr>
    <tr>
      <th>4</th>
      <td>noise - park</td>
      <td>4020</td>
    </tr>
    <tr>
      <th>5</th>
      <td>noise - helicopter</td>
      <td>1715</td>
    </tr>
    <tr>
      <th>6</th>
      <td>noise - house of worship</td>
      <td>1143</td>
    </tr>
    <tr>
      <th>7</th>
      <td>collection truck noise</td>
      <td>184</td>
    </tr>
  </tbody>
</table>
</div>



### <> 
특정 레코드값을 제외

### COLLATE NOCASE
대소문자 구분을 무시


```python
# name = 'None'을 제외하고 City이름은 대소문자 구별하지 않음
query = '''
    SELECT UPPER(City) AS name, COUNT(*) AS freq
    FROM data
    WHERE name <> 'None'
    GROUP BY City COLLATE NOCASE
    ORDER BY -freq
    LIMIT 10
'''
df2 = pd.read_sql_query(query, dataset)
df2
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
      <th>freq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BROOKLYN</td>
      <td>579363</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NEW YORK</td>
      <td>385655</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BRONX</td>
      <td>342533</td>
    </tr>
    <tr>
      <th>3</th>
      <td>STATEN ISLAND</td>
      <td>92509</td>
    </tr>
    <tr>
      <th>4</th>
      <td>JAMAICA</td>
      <td>46683</td>
    </tr>
    <tr>
      <th>5</th>
      <td>FLUSHING</td>
      <td>35504</td>
    </tr>
    <tr>
      <th>6</th>
      <td>ASTORIA</td>
      <td>31873</td>
    </tr>
    <tr>
      <th>7</th>
      <td>RIDGEWOOD</td>
      <td>21618</td>
    </tr>
    <tr>
      <th>8</th>
      <td>WOODSIDE</td>
      <td>15932</td>
    </tr>
    <tr>
      <th>9</th>
      <td>CORONA</td>
      <td>15740</td>
    </tr>
  </tbody>
</table>
</div>




```python
top5_cities = list(df2.head(5)['name'])
top5_cities   # complaint 숫자로 본 top5 도시
```




    ['BROOKLYN', 'NEW YORK', 'BRONX', 'STATEN ISLAND', 'JAMAICA']



### 문자들의 list를 하나의 문자로 


```python
def strs_to_args(str_list):
    assert type (str_list) is list
    assert all ([type (s) is str for s in str_list])
    ###
    c = str(list(i for i in str_list))
    c= c.replace('[','')
    c= c.replace(']','')
    c= c.replace("'",'"')
    return c

# 테스트
a=['a', 'b', 'c', 'd']
strs_to_args(a)
```




    '"a", "b", "c", "d"'




```python
#예제: top5 도시들의 complaint type별 count를 구하시오
query = '''SELECT LOWER(ComplaintType) AS complaint_type, 
                UPPER(CITY) as city_name, COUNT(*) as complaint_count
            FROM data
            WHERE city_name IN ({})
            GROUP BY city_name, complaint_type            
'''.format(strs_to_args(top5_cities))

df_complaints_by_city = pd.read_sql_query(query, dataset)
df_complaints_by_city
###
display(df_complaints_by_city.head(10))
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
      <th>complaint_type</th>
      <th>city_name</th>
      <th>complaint_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>adopt-a-basket</td>
      <td>BRONX</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>agency</td>
      <td>BRONX</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>air quality</td>
      <td>BRONX</td>
      <td>666</td>
    </tr>
    <tr>
      <th>3</th>
      <td>animal abuse</td>
      <td>BRONX</td>
      <td>1973</td>
    </tr>
    <tr>
      <th>4</th>
      <td>animal facility - no permit</td>
      <td>BRONX</td>
      <td>10</td>
    </tr>
    <tr>
      <th>5</th>
      <td>animal in a park</td>
      <td>BRONX</td>
      <td>189</td>
    </tr>
    <tr>
      <th>6</th>
      <td>appliance</td>
      <td>BRONX</td>
      <td>3758</td>
    </tr>
    <tr>
      <th>7</th>
      <td>asbestos</td>
      <td>BRONX</td>
      <td>264</td>
    </tr>
    <tr>
      <th>8</th>
      <td>beach/pool/sauna complaint</td>
      <td>BRONX</td>
      <td>13</td>
    </tr>
    <tr>
      <th>9</th>
      <td>best/site safety</td>
      <td>BRONX</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>



```python
# 예제: 전체도시에서 top10 complaints를 정렬
query = '''Select LOWER(ComplaintType) as type, Count(ComplaintType) as freq
           from data GROUP BY type 
           ORDER BY -freq
            '''
df_complaint_freq = pd.read_sql_query(query, dataset)
top_complaints = df_complaint_freq[:10]
top_complaints
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
      <th>type</th>
      <th>freq</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>heat/hot water</td>
      <td>241430</td>
    </tr>
    <tr>
      <th>1</th>
      <td>street condition</td>
      <td>124347</td>
    </tr>
    <tr>
      <th>2</th>
      <td>street light condition</td>
      <td>98577</td>
    </tr>
    <tr>
      <th>3</th>
      <td>blocked driveway</td>
      <td>95080</td>
    </tr>
    <tr>
      <th>4</th>
      <td>illegal parking</td>
      <td>83961</td>
    </tr>
    <tr>
      <th>5</th>
      <td>unsanitary condition</td>
      <td>81394</td>
    </tr>
    <tr>
      <th>6</th>
      <td>paint/plaster</td>
      <td>69929</td>
    </tr>
    <tr>
      <th>7</th>
      <td>water system</td>
      <td>69209</td>
    </tr>
    <tr>
      <th>8</th>
      <td>plumbing</td>
      <td>60105</td>
    </tr>
    <tr>
      <th>9</th>
      <td>noise</td>
      <td>54165</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 도시별 top10 complaints별 count
df_plot = top_complaints.merge(df_complaints_by_city,
                               left_on=['type'],
                               right_on=['complaint_type'],
                               how='left')
df_plot.dropna(inplace=True)
df_plot.head(3)
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
      <th>type</th>
      <th>freq</th>
      <th>complaint_type</th>
      <th>city_name</th>
      <th>complaint_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>heat/hot water</td>
      <td>241430</td>
      <td>heat/hot water</td>
      <td>BRONX</td>
      <td>79690</td>
    </tr>
    <tr>
      <th>1</th>
      <td>heat/hot water</td>
      <td>241430</td>
      <td>heat/hot water</td>
      <td>BROOKLYN</td>
      <td>72410</td>
    </tr>
    <tr>
      <th>2</th>
      <td>heat/hot water</td>
      <td>241430</td>
      <td>heat/hot water</td>
      <td>JAMAICA</td>
      <td>3376</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 예제: 도시별 전체 불만 중 해당 불만이 차지하는 비중을 'frac'으로 나타내라
df_plot['frac']=df_plot['complaint_count']/df_plot['freq']  # 열 추가
df_plot_fraction = df_plot.copy()  # DF 복사
df_plot_fraction = df_plot.drop('complaint_count', axis=1)  # 'count'열 삭제
###
df_plot_fraction.head(2)
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
      <th>type</th>
      <th>freq</th>
      <th>complaint_type</th>
      <th>city_name</th>
      <th>frac</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>heat/hot water</td>
      <td>241430</td>
      <td>heat/hot water</td>
      <td>BRONX</td>
      <td>0.330075</td>
    </tr>
    <tr>
      <th>1</th>
      <td>heat/hot water</td>
      <td>241430</td>
      <td>heat/hot water</td>
      <td>BROOKLYN</td>
      <td>0.299921</td>
    </tr>
  </tbody>
</table>
</div>



### 날짜, 시간


```python
query = '''
        SELECT LOWER(ComplaintType), CreatedDate, UPPER(City)
        FROM data
        WHERE CreatedDate >= "2015-09-15 00:00:00.0" 
        and CreatedDate < "2015-09-16 00:00:00.0"
        ORDER BY CreatedDate 
'''    # 2015-09-15 ~ 2015-09-16 날짜 사이 발생한 items 모두
df = pd.read_sql_query (query, dataset)
df.head(2)
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
      <th>LOWER(ComplaintType)</th>
      <th>CreatedDate</th>
      <th>UPPER(City)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>illegal parking</td>
      <td>2015-09-15 00:01:23.000000</td>
      <td>None</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blocked driveway</td>
      <td>2015-09-15 00:02:29.000000</td>
      <td>REGO PARK</td>
    </tr>
  </tbody>
</table>
</div>



#### `strftime()`
날짜/시간을 string으로 변환


```python
query = '''
    SELECT CreatedDate, STRFTIME('%H', CreatedDate) AS Hour, LOWER(ComplaintType)
    FROM data
    LIMIT 5
'''    # 시간을 Hour로 저장
df = pd.read_sql_query (query, dataset)
df.head(2)
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
      <th>CreatedDate</th>
      <th>Hour</th>
      <th>LOWER(ComplaintType)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2015-09-15 02:14:04.000000</td>
      <td>02</td>
      <td>illegal parking</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2015-09-15 02:12:49.000000</td>
      <td>02</td>
      <td>noise - street/sidewalk</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 예제: 시간대별 count 구하기
query = '''
    SELECT STRFTIME('%H', CreatedDate) AS hour, count(*) AS count
    FROM data GROUP BY hour'''
df_complaints_by_hour = pd.read_sql_query(query, dataset)
###
df_complaints_by_hour.head(2)
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
      <th>hour</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>00</td>
      <td>564703</td>
    </tr>
    <tr>
      <th>1</th>
      <td>01</td>
      <td>23489</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 예제: noise들어간 compalint type중에 00:00시 날짜를 제외하고 시간대별 정렬
query='''SELECT STRFTIME('%H', CreatedDate) AS hour, count(*) AS count
         FROM data 
         WHERE LOWER(ComplaintType) LIKE '%noise%' AND
         STRFTIME('%H:%M:%f', CreatedDate) <> '00:00:00.000'
         GROUP BY hour
'''
df_noisy_by_hour = pd.read_sql_query(query, dataset)
df_noisy_by_hour.head(3)
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
      <th>hour</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>00</td>
      <td>15349</td>
    </tr>
    <tr>
      <th>1</th>
      <td>01</td>
      <td>11284</td>
    </tr>
    <tr>
      <th>2</th>
      <td>02</td>
      <td>7170</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 예제: top3 complaint를 시간대별로, count 역순 정렬
top3 = strs_to_args(top_complaints[:3]['type'].tolist())

query='''
    SELECT LOWER(ComplaintType) as complaint_type, strftime('%H', CreatedDate) as hour, count(*) as count
    FROM data
    WHERE (complaint_type IN ({}) and strftime('%H:%M:%f', CreatedDate)<> '00:00:00.000')
    GROUP BY complaint_type, hour
    ORDER BY hour, -count
'''.format(top3)

df_top3_complaints_by_hour = pd.read_sql_query(query, dataset)
df_top3_complaints_by_hour.head(10)
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
      <th>complaint_type</th>
      <th>hour</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>street condition</td>
      <td>00</td>
      <td>1298</td>
    </tr>
    <tr>
      <th>1</th>
      <td>street light condition</td>
      <td>00</td>
      <td>891</td>
    </tr>
    <tr>
      <th>2</th>
      <td>heat/hot water</td>
      <td>00</td>
      <td>139</td>
    </tr>
    <tr>
      <th>3</th>
      <td>street condition</td>
      <td>01</td>
      <td>1093</td>
    </tr>
    <tr>
      <th>4</th>
      <td>street light condition</td>
      <td>01</td>
      <td>623</td>
    </tr>
    <tr>
      <th>5</th>
      <td>heat/hot water</td>
      <td>01</td>
      <td>89</td>
    </tr>
    <tr>
      <th>6</th>
      <td>street condition</td>
      <td>02</td>
      <td>661</td>
    </tr>
    <tr>
      <th>7</th>
      <td>street light condition</td>
      <td>02</td>
      <td>385</td>
    </tr>
    <tr>
      <th>8</th>
      <td>heat/hot water</td>
      <td>02</td>
      <td>68</td>
    </tr>
    <tr>
      <th>9</th>
      <td>street condition</td>
      <td>03</td>
      <td>349</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 시간대별 complaint 수를 line chart로 그리시오
df_pivot = pd.pivot_table(df_top3_complaints_by_hour, values='count', columns='complaint_type', index='hour')

import matplotlib.pyplot as plt
df_pivot.plot(figsize=(10, 8))
plt.xlabel('Hour of Day')
plt.ylabel('Number of Complaints')
plt.show()
```

<p align="left"><img src="/assets/images/221017_output.png" width="500" height="300"></p>

