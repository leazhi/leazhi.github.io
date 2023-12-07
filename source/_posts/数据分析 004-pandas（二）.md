---
title: 数据分析 004-pandas（二）
author: leazhi
tags:
  - [python]
  - [datanalysis]
  - [pandas]
categories:
  - [python]
  - [datanalysis]
date: 2023-10-24 07:29:22
cover: 
discription: 
keywords: 
password: 
message: 
---


## 导入所需的库

```python
import numpy as np
import pandas as pd
```

## 索引操作
```python
df1 = pd.DataFrame(np.arange(9).reshape(3,3), index=['sh', 'cs', 'b'], columns=['a', 'b', 'c'])
print(df1)
'''
    a  b  c
sh  0  1  2
cs  3  4  5
b   6  7  8
'''
```

### 获取索引
```python
print(df1.index)
'''
Index(['sh', 'cs', 'b'], dtype='object')
'''
```

### 获取索引的值
```python
print(df1.index.tolist())
'''
['sh', 'cs', 'b']
'''
```

### 修改索引

#### 通过赋值的方式

该方式会直接覆盖原数据
```python
df1.index=['Shanghai', 'Beijing', 'Huhan']
print(df1)
'''
          a  b  c
Shanghai  0  1  2
Beijing   3  4  5
Huhan     6  7  8
'''
```

批量处理
```python
def test_map(x):
    return x+'_ABC'
df1 = df1.rename(index=test_map, columns=test_map)
print(df1)
'''
              a_ABC  b_ABC  c_ABC
Shanghai_ABC      0      1      2
Beijing_ABC       3      4      5
Huhan_ABC         6      7      8
'''
```

### 设置索引
```python
df2 = pd.DataFrame({'A': range(4), 'B': [2, 8, 9, 1], 'C': list('abcd'), 'D': [1, 2, 34, 4]})
print(df2)
'''
   A  B  C   D
0  0  2  a   1
1  1  8  b   2
2  2  9  c  34
3  3  1  d   4
'''

df3 = df2.set_index('C', drop=False)    # 这里的 drop 是是否删除设置索引列的数据,False 不删除
print(df3)
'''
   A  B  C   D
C             
a  0  2  a   1
b  1  8  b   2
c  2  9  c  34
d  3  1  d   4
'''
```
## 数据的操作
```python
data = {
    'Date': ['2023-10-01', '2023-10-02', '2023-10-03'],
    'Setps': [8000, 9000, 7500]
}

df = pd.DataFrame(data)
print(df)
'''
         Date  Setps
0  2023-10-01   8000
1  2023-10-02   9000
2  2023-10-03   7500
'''
```
### 添加

新增一列
```python
df['cost'] = [30, 40, 25]
print(df)
'''
         Date  Setps  cost
0  2023-10-01   8000    30
1  2023-10-02   9000    40
2  2023-10-03   7500    25
'''
```
### 合并

#### concat

concat 作拼接不去作任何处理，直接进行拼接，元数据是怎么样的，拼接出来的也保持一致
```python
yb_data = {
    'Day': ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'],
    'Forecast': ['Sunny', 'Partly Cloudy', 'Rainy', 'Cloudy', 'Sunny']
}

df1 = pd.DataFrame(yb_data)

qw_data = {
    'Day': ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'],
    'Temperature': [28, 29, 24, 22, 27]
}

df2 = pd.DataFrame(qw_data)

df_concat = pd.concat([df1, df2], axis=1)
print(df_concat)
'''
         Day       Forecast        Day  Temperature
0     Monday          Sunny     Monday           28
1    Tuesday  Partly Cloudy    Tuesday           29
2  Wednesday          Rainy  Wednesday           24
3   Thursday         Cloudy   Thursday           22
4     Friday          Sunny     Friday           27
'''
```

当数据不一一对应作拼接的时候，直接进行拼接，元数据是怎么样的，拼接出来的也保持一致
```python
qw_data = {
    'Day': ['Tuesday', 'Monday', 'Wednesday', 'Thursday', 'Friday'],
    'Temperature': [28, 29, 24, 22, 27]
}
df2 = pd.DataFrame(qw_data)

df_concat = pd.concat([df1, df2], axis=1)
print(df_concat)
'''
        Day       Forecast        Day  Temperature
0     Monday          Sunny    Tuesday           28
1    Tuesday  Partly Cloudy     Monday           29
2  Wednesday          Rainy  Wednesday           24
3   Thursday         Cloudy   Thursday           22
4     Friday          Sunny     Friday           27
'''
```
#### merge

它注重列的合并
```python
user_data = {
    'CustomerID': [1, 2, 3, 4, 5],
    'Name': ['Rose', 'Bob', 'Jack', 'David', 'Lucy'],
    'Email': ['rose@163.com', 'bob@163.com', 'jack@163.com', 'david@163.com', 'lucy@163.com']
}
df_user = pd.DataFrame(user_data)

buy_data = {
    'CustomerID': [1 ,2, 1, 3, 4, 3, 6],
    'OrderID': [101, 102, 103, 104,105, 106, 107],
    'Procuct': ['A', 'B', 'C', 'D', 'E', 'A', 'B'],
    'Quantity': [2, 1, 3, 2, 4, 1, 2]
}
df_buy= pd.DataFrame(buy_data)

df_merge = pd.merge(df_user, df_buy, on='CustomerID', how='inner')       # on 是根据哪个字段进行拼接, how 是拼接的方式，默认为 inner(交集）
print(df_merge)
'''
   CustomerID   Name          Email  OrderID Procuct  Quantity
0           1   Rose   rose@163.com      101       A         2
1           1   Rose   rose@163.com      103       C         3
2           2    Bob    bob@163.com      102       B         1
3           3   Jack   jack@163.com      104       D         2
4           3   Jack   jack@163.com      106       A         1
5           4  David  david@163.com      105       E         4
'''

df_merge = pd.merge(df_user, df_buy, on='CustomerID', how='outer')       # on 是根据哪个字段进行拼接, how 是拼接的方式， outer(并集）
print(df_merge)
'''
   CustomerID   Name          Email  OrderID Procuct  Quantity
0           1   Rose   rose@163.com    101.0       A       2.0
1           1   Rose   rose@163.com    103.0       C       3.0
2           2    Bob    bob@163.com    102.0       B       1.0
3           3   Jack   jack@163.com    104.0       D       2.0
4           3   Jack   jack@163.com    106.0       A       1.0
5           4  David  david@163.com    105.0       E       4.0
6           5   Lucy   lucy@163.com      NaN     NaN       NaN
7           6    NaN            NaN    107.0       B       2.0
'''

df_merge = pd.merge(df_user, df_buy, on='CustomerID', how='left')       # on 是根据哪个字段进行拼接, how 是拼接的方式， left 表示以 df_user 为主进行拼接，没有的数据会以 Nan 填充
print(df_merge)
'''
   CustomerID   Name          Email  OrderID Procuct  Quantity
0           1   Rose   rose@163.com    101.0       A       2.0
1           1   Rose   rose@163.com    103.0       C       3.0
2           2    Bob    bob@163.com    102.0       B       1.0
3           3   Jack   jack@163.com    104.0       D       2.0
4           3   Jack   jack@163.com    106.0       A       1.0
5           4  David  david@163.com    105.0       E       4.0
6           5   Lucy   lucy@163.com      NaN     NaN       NaN
'''


df_merge = pd.merge(df_user, df_buy, on='CustomerID', how='right')       # on 是根据哪个字段进行拼接, how 是拼接的方式， right 表示以 df_buy 为主进行拼接，没有的数据会以 Nan 填充
print(df_merge)
'''
   CustomerID   Name          Email  OrderID Procuct  Quantity
0           1   Rose   rose@163.com      101       A         2
1           2    Bob    bob@163.com      102       B         1
2           1   Rose   rose@163.com      103       C         3
3           3   Jack   jack@163.com      104       D         2
4           4  David  david@163.com      105       E         4
5           3   Jack   jack@163.com      106       A         1
6           6    NaN            NaN      107       B         2
'''
```

#### join

```python
students_data = {
    'StudentID': [1 ,2, 3, 4, 5],
    'Name': ['Alice', 'Bob', 'Charlie', 'David', 'Eve'],
    'Age': [18, 19, 18, 20, 19]
}
df_students = pd.DataFrame(students_data)
df_students = df_students.set_index('StudentID')

scores_data = {
    'StudentID': [1, 2, 3, 4, 5],
    'Math': [90, 85, 92, 78,88],
    'Science': [88, 87, 91, 79, 90]
}
df_scores = pd.DataFrame(scores_data)
df_scores = df_scores.set_index('StudentID')

df_join = df_students.join(df_scores, on='StudentID')
print(df_join)
'''
              Name  Age  Math  Science
StudentID                             
1            Alice   18    90       88
2              Bob   19    85       87
3          Charlie   18    92       91
4            David   20    78       79
5              Eve   19    88       90
'''
```
### 删除
```python
df = pd.DataFrame(np.random.rand(16).reshape(4, 4)*100, columns=['a', 'b', 'c', 'd'])
print(df)
'''
           a          b          c          d
0   8.840977  91.661232  93.729579  22.940104
1  11.785395  65.612190   9.376062  25.620551
2  37.279052  22.771117   4.086980  26.134828
3  29.595431  83.165683  46.653538  66.183894
'''
```
#### del
```python
del df['a']
print(df)
'''
           b          c          d
0  91.661232  93.729579  22.940104
1  65.612190   9.376062  25.620551
2  22.771117   4.086980  26.134828
3  83.165683  46.653538  66.183894
'''
```
#### drop

默认是删除行
```python
print(df.drop(0))
'''
           c          d
0  93.729579  22.940104
1   9.376062  25.620551
2   4.086980  26.134828
3  46.653538  66.183894
'''
```

删除列：
```python
print(df.drop('b', axis=1))
'''
           c          d
0  93.729579  22.940104
1   9.376062  25.620551
2   4.086980  26.134828
3  46.653538  66.183894
'''
```
## 常见查询函数

### query()
```python
data = {
    'A': [1, 2, 3, 4, 5],
    'B': [10, 20, 30, 40, 50]
}
df = pd.DataFrame(data)

# A 列中 >2 的数据.查询的语句要用引号引起来
res1 = df.query('A > 2')
print(res1)
'''
   A   B
2  3  30
3  4  40
4  5  50
'''

# A>2 并且 B<40 的数据
res2 = df.query('(A>2) and (B<40)')
print(res2)
'''
   A   B
2  3  30
'''


cols = ['英语', '数学','语文']
index = [f'stu_{i}' for i in range(1, 11)]
values = np.random.randint(0, 100, (10 ,3))
df = pd.DataFrame(data=values, columns=cols, index=index)
print(df)
'''
        英语  数学  语文
stu_1   59  95  72
stu_2    0  25  77
stu_3   92  15  22
stu_4   99  43  11
stu_5   28  67  18
stu_6   32  57  69
stu_7   74  20  11
stu_8   78  50  45
stu_9   66  74  64
stu_10  40  94  55
'''
# 查询英语成绩大于英语平均值的值
average = df['英语'].mean()
print(df.query('英语>@average'))
'''
       英语  数学  语文
stu_1  59  95  72
stu_3  92  15  22
stu_4  99  43  11
stu_7  74  20  11
stu_8  78  50  45
stu_9  66  74  64
'''
```
### isin
```python
data = {
    'City': ['长沙', '北京','上海','成都','武汉'],
    'Population(millions)': [84, 39, 27, 23, 15]
}
df = pd.DataFrame(data)
print(df)
'''
  City  Population(millions)
0   长沙                    84
1   北京                    39
2   上海                    27
3   成都                    23
4   武汉                    15
'''

print(df['City'].isin(['长沙', '武汉']))
'''
0     True
1    False
2    False
3    False
4     True
Name: City, dtype: bool
'''

in_cities = df[df['City'].isin(['长沙', '武汉'])]
print(in_cities)
'''
  City  Population(millions)
0   长沙                    84
4   武汉                    15
'''
```
### 多级索引
```python
df1 = pd.DataFrame(np.random.randint(70, 100, size=(2, 4)),
                   index=['girl', 'boy'],
                   columns=[
                       ['English', 'English', 'Chinese', 'Chinese'],
                       ['like', 'dislike', 'like', 'dislike']
                   ])
print(df1)
'''
     English         Chinese        
        like dislike    like dislike
girl      80      76      80      92
boy       77      99      86      90
'''


df2 = pd.DataFrame(np.random.randint(70, 120, size=(4,2)),
                   columns=['girl', 'boy'],
                   index=pd.MultiIndex.from_product([
                       ['English', 'Chinese'],
                       ['like', 'dislike']
                   ]))
print(df2)
'''
                 girl  boy
English like       91  111
        dislike    77   75
Chinese like       94  104
        dislike    80   72
'''
```
### 检索多级索引
```python
s = pd.Series(np.random.randint(1, 100, size=6), index=[
    ['a', 'a', 'b', 'b', 'c', 'c'],
    ['期中', '期末', '期中', '期末', '期中', '期末']
])
print(s)
'''
a  期中    57
   期末    27
b  期中    71
   期末    66
c  期中    93
   期末    69
dtype: int64
'''

print(s[0])
'''
57
'''

print(s['a', '期末'])
'''
27
'''

s = s.reset_index()
print(s)
'''
  level_0 level_1   0
0       a      期中  57
1       a      期末  27
2       b      期中  71
3       b      期末  66
4       c      期中  93
5       c      期末  69
'''
```

print('='*100)

## 课后作业
```python
# 1.df = pd.DataFrame(np.random.randint(10, 20, (3, 3)), index=['a', 'b', 'c']), 要求：添加 age 列，并添加相应数据
df = pd.DataFrame(np.random.randint(10, 20, (3, 3)), index=['a', 'b', 'c'])
print(df)
'''
    0   1   2
a  15  15  15
b  14  11  10
c  11  11  18
'''

df['age'] = [10, 20, 30]
print(df)
'''
    0   1   2  age
a  15  15  15   10
b  14  11  10   20
c  11  11  18   30
'''

# 2.例 1 中的 df,请按 age 列进行降序排列； 按索引升序排序；并新增按 age 进行的排名列
print(df.sort_values(by='age', ascending=False))
'''
    0   1   2  age
c  11  11  18   30
b  14  11  10   20
a  15  15  15   10
'''

print(df.sort_index())
'''
    0   1   2  age
a  15  15  15   10
b  14  11  10   20
c  11  11  18   30
'''

print(df.set_index('age', drop=False))
'''
      0   1   2  age
age                 
10   15  15  15   10
20   14  11  10   20
30   11  11  18   30
'''

# 3.将 df 中的 c 行的 age 改为 2
df['age'][2]=2
print(df)
'''
    0   1   2  age
a  15  15  15   10
b  14  11  10   20
c  11  11  18    2
'''

df.loc['c', 'age'] =2
print(df)
'''
    0   1   2  age
a  15  15  15   10
b  14  11  10   20
c  11  11  18    2
'''

df.iloc[2,2] =2
print(df)
'''
    0   1   2  age
a  15  15  15   10
b  14  11  10   20
c  11  11   2    2
'''

# 4.新增 priority 列，数据只有 'yes', 'no'; 然后 priority 列中的 yes, no 替换为布尔值 True, False
df['prority']=['yes', 'no', 'no']
print(df)
'''
    0   1   2  age prority
a  15  15  15   10     yes
b  14  11  10   20      no
c  11  11   2    2      no
'''

df['prority'] = df['prority'].map({'yes': True, 'no': False})
print(df)
'''
    0   1   2  age  prority
a  15  15  15   10     True
b  14  11  10   20    False
c  11  11   2    2    False
'''
```