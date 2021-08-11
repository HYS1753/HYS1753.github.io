---
author_profile: true
date: 2021-07-00
title: "Python Pandas 라이브러리 5 (데이터 재구조화)"
categories: 
    - Python
tag: 
    - Pandas

# 목차
toc: true  
toc_sticky: true 
# sidebar:
#  nav: "docs"
---

# Pandas(데이터 재구조화)

---

## 데이터 구간화

```python
# 동일한 길이로 범주화
df1 = pd.cut(df.math, 3)
# 각 범주 구간 별로 동일하게 3개의 개수를 가지도록 범주 만듬
# label = False 는 label이 0부터 시작하게 끔 함
df2 = pd.qcut(df.math, 3, label=False) 
# pd.qcut(df.math, 3, labels=np.arange(3, 0, -1))
```

## 데이터 전치

```python
df.T
```

## 피봇 테이블

`df.pivot(index, columns, values)`
`df.pivot_table(data, index, columns, values, aggfunc)`

### example table

```
	student_no	class	science	english	math	sex
0	1	A	50	98	50	m
1	2	A	60	97	60	w
2	3	A	78	86	45	w
3	4	A	58	98	30	m
4	5	B	65	80	90	w
5	6	B	98	89	50	m
6	7	B	45	90	80	m
7	8	B	25	78	90	w
8	9	C	15	98	20	w
9	10	C	45	93	50	w
```

```python
# 반(class) 변수를 행 데이터로 피봇
pd.pivot_table(df,index='class',columns='sex',values='science')
```

```
sex	m	w
class		
A	54.0	69.0
B	71.5	45.0
C	NaN	30.0
```

```python
# 성별 변수를 행 데이터로 피봇
pd.pivot_table(df, index='sex',columns = 'class', values='science')
```

```
class	A	B	C
sex			
m	54.0	71.5	NaN
w	69.0	45.0	30.0
```

# 데이터 그룹 분석

---

## 집계함수 사용
`df.groupby(['그룹변수'])`
`df.get_group('그룹 변수 지정')`

**example data**
```
	student_no	class	science	english	math	sex
0	1	A	50	98	50	m
1	2	A	60	97	60	w
2	3	A	78	86	45	w
3	4	A	58	98	30	m
4	5	B	65	80	90	w
5	6	B	98	89	50	m
6	7	B	45	90	80	m
7	8	B	25	78	90	w
8	9	C	15	98	20	w
9	10	C	45	93	50	w
```

### 반별로 그룹화(A,B,C반)

```python
# 반별 그룹 오브젝트만 생성
df1 = df.groupby(['class'])
# 반 중 A 반 그룹만 확인
df1.get_group('A')
```

출력

```
student_no	class	science	english	math	sex
0	1	A	50	98	50	m
1	2	A	60	97	60	w
2	3	A	78	86	45	w
3	4	A	58	98	30	m
```

### 반별 그룹 평균 확인

```python
df.groupby('class').mean()
```

출력

```
	student_no	science	english	math
class				
A	2.5	61.50	94.75	46.25
B	6.5	58.25	84.25	77.50
C	9.5	30.00	95.50	35.00
```

### 반별, 성별 그룹 평균 확인

```python
df.groupby(['class', 'sex']).mean()
```

출력

```

student_no	science	english	math
class	sex				
A	m	2.5	54.0	98.0	40.0
    w	2.5	69.0	91.5	52.5
B	m	6.5	71.5	89.5	65.0
    w	6.5	45.0	79.0	90.0
C	w	9.5	30.0	95.5	35.0
```

### 반별 수학 평균

```python
df['math'].groupby(df['class']).mean()
# 같은 의미로
# df.groupby(df['class'])['math'].mean()
```

출력

```
class
A    46.25
B    77.50
C    35.00
Name: math, dtype: float64
```

### 성별 수학 평균

```python
# 성별 수학 평균
df_mean = df['math'].groupby(df['sex']).mean()
print(df_mean)
```

출력

```
sex
m    52.500000
w    59.166667
Name: math, dtype: float64
```

