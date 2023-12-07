---
title: 数据分析 005-pandas（三）聚合和分组
author: leazhi
tags:
  - [python]
  - [pandas]
categories:
  - [python]
  - [datanalysis]
date: 2023-10-25 09:00:27
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
from numpy import nan as NaN
```


## 函数应用和映射

### 函数映射

测试数据

```python
df = pd.DataFrame(np.random.randn(4 ,3), columns=list('bde'), index=['Shenzhen', 'Beijing', 'Changsha', 'Shanghai'])
print(df)
'''
                 b         d         e
Shenzhen -0.042159  1.565690 -0.371330
Beijing  -1.156061 -0.023265  0.858431
Changsha -0.270876  1.104130 -1.328757
Shanghai -0.536744 -0.919526  0.703672
'''
```

#### apply()

用于对 DataFrame 中的数据进行按行或者按列操作     
默认是 0 轴方向(也就是按列)
```python
f = lambda x:x.max() - x.min()
print(df.apply(f))
'''
b    1.113903
d    2.485216
e    2.187188
dtype: float64
'''
```

按 行 进行操作, 只需要家参数 axis
```python
print(df.apply(f, axis=1))
'''
Shenzhen    1.937020
Beijing     2.014492
Changsha    2.432887
Shanghai    1.623198
dtype: float64
'''
```


##### 使用内置平方根函数 sqrt()
df = pd.DataFrame([[4, 9]]*3, columns=['A', 'B'])
print(df)
'''
   A  B
0  4  9
1  4  9
2  4  9
'''

print(df.apply(np.sqrt))
'''
     A    B
0  2.0  3.0
1  2.0  3.0
2  2.0  3.0
'''


##### 生活案例-计算税额
data = {
    '商品名称': ['商品A', '商品B', '商品C', '商品D'],
    '销售价格': [20.0, 15.0, 30.0, 25.0]
}

df = pd.DataFrame(data)
print(df)
'''
  商品名称  销售价格
0  商品A  20.0
1  商品B  15.0
2  商品C  30.0
3  商品D  25.0
'''

df['销售税额'] = df['销售价格'].apply(lambda x:x*0.1)
print(df)
'''
  商品名称  销售价格  销售税额
0  商品A  20.0   2.0
1  商品B  15.0   1.5
2  商品C  30.0   3.0
3  商品D  25.0   2.5
'''
# 相对于简单的运算也可以使用下面的计算方式
df['销售税额2'] = df['销售价格']*0.1
print(df)
'''
  商品名称  销售价格  销售税额  销售税额2
0  商品A  20.0   2.0    2.0
1  商品B  15.0   1.5    1.5
2  商品C  30.0   3.0    3.0
3  商品D  25.0   2.5    2.5
'''

def func(x):
    return pd.Series([x.min(), x.max()], index=['min', 'max'])
print(df.apply(func))
'''
    商品名称  销售价格  销售税额  销售税额2
min  商品A  15.0   1.5    1.5
max  商品D  30.0   3.0    3.0
'''

#### map()

# 用于对 Series 中的每一个数据操作，它可以是函数，也可以是字典
s = pd.Series(['apple', 'banana', 'carrot', 'apple', 'banana', 'carrot'])
price = {'apple': 5.0, 'banana': 2.5, 'carrot': 1.2}

# 将商品和价格进行映射
map_s = s.map(price)
print(map_s)
'''
0    5.0
1    2.5
2    1.2
3    5.0
4    2.5
5    1.2
dtype: float64
'''

#### applymap()

# 用于对 DataFrame 的每一个元素操作


## 获取唯一索引

### df.index.is_unique
# 判断是否是唯一值

### df.index_unique()

# 获取唯一值


#### 带有重复标签的轴索引
df = pd.Series(range(5), index=['a', 'a', 'b', 'b', 'c'])
print(df)
'''
dtype: float64
a    0
a    1
b    2
b    3
c    4
dtype: int64
'''
print(df.index.unique())
'''
Index(['a', 'b', 'c'], dtype='object')
'''

## 统计方法及函数

data = {
    '姓名': ['Lucy', 'Lily', 'Tom', 'Jack'],
    '数学': [85, 92, 78, 65],
    '英语': [76, 88, 92, 71],
    '历史': [90, 82, 68, 56]
}

df = pd.DataFrame(data)
print(df)
'''
     姓名  数学  英语  历史
0  Lucy  85  76  90
1  Lily  92  88  82
2   Tom  78  92  68
3  Jack  65  71  56
'''

# 设置姓名这一列为索引
df = df.set_index('姓名')
print(df)
'''
     数学  英语  历史
姓名              
Lucy  85  76  90
Lily  92  88  82
Tom   78  92  68
Jack  65  71  56
'''

# 求平均值
print(df.mean())
'''
数学    80.00
英语    81.75
历史    74.00
dtype: float64
'''

# 求每个学生的平均成绩
print(df.mean(axis=1))
'''
姓名
Lucy    83.666667
Lily    87.333333
Tom     79.333333
Jack    64.000000
dtype: float64
'''

# 求每科成绩最高分是谁？
print(df.idxmax())      # idxmax() 获取的是最高值及对应的索引
'''
数学    Lily
英语     Tom
历史    Lucy
dtype: object
'''

# 求每科成绩最低分是谁？
print(df.idxmin())
'''
数学    Jack
英语    Jack
历史    Jack
dtype: object
'''

### 累计函数



### 统计方法


## 数据处理

df1 = pd.DataFrame([[1, 2, 3], [NaN, 5, NaN], [NaN, 9, 8], [NaN, NaN, NaN]])
print(df1)
'''
     0    1    2
0  1.0  2.0  3.0
1  NaN  5.0  NaN
2  NaN  9.0  8.0
3  NaN  NaN  NaN
'''


### 滤除空值： dropna()
# 作用于  Series 和 DataFrame
print(df1.dropna())             # 只要有空值就会删除（也称 暴力删除）,相当于加了参数 how='any'
'''
     0    1    2
0  1.0  2.0  3.0
'''

# 滤除全部为空值的行
print(df1.dropna(how='all'))    # 只删除全部为空值的行
'''
     0    1    2
0  1.0  2.0  3.0
1  NaN  5.0  NaN
2  NaN  9.0  8.0
'''

# 滤除全部为空值的列
print(df1.dropna(how='all',axis=1))     # 只删除全部为空值的列
'''
     0    1    2
0  1.0  2.0  3.0
1  NaN  5.0  NaN
2  NaN  9.0  8.0
3  NaN  NaN  NaN
'''

# 阈值: thresh = n ，表示保留至少 n 个非空值数据的行
print(df.dropna(thresh=1))          # 保留每行至少有 1 个非空值数据的行

### 填充空值：fillna()
# 常数填充；均值填充；插值填充

print(df1.fillna(0))
'''
     0    1    2
0  1.0  2.0  3.0
1  0.0  5.0  0.0
2  0.0  9.0  8.0
3  0.0  0.0  0.0
'''

# 每列填充不相同的值（将 0 列中的空值填充为10 ，1 列中的空值填充为20 ，2 列中的空值填充为30）
print(df1.fillna({0:10, 1:20, 2: 30}))
'''
      0     1     2
0   1.0   2.0   3.0
1  10.0   5.0  30.0
2  10.0   9.0   8.0
3  10.0  20.0  30.0
'''

# 向上填充
print(df1.fillna(method='ffill'))
'''
     0    1    2
0  1.0  2.0  3.0
1  1.0  5.0  3.0
2  1.0  9.0  8.0
3  1.0  9.0  8.0
'''


### 去重

s1 = pd.Series(list('aaaabbbbccccde'))
print(s1)
'''
0     a
1     a
2     a
3     a
4     b
5     b
6     b
7     b
8     c
9     c
10    c
11    c
12    d
13    e
dtype: object
'''

s2 = pd.DataFrame({
    'A': [1, 1, 1, 2, 2, 3, 4, 1],
    'B': list('aaabbbcU')
})
print(s2)
'''
dtype: object
   A  B
0  1  a
1  1  a
2  1  a
3  2  b
4  2  b
5  3  b
6  4  c
7  1  U
'''
#### drop_duplicates() 去重

print(s1.drop_duplicates())
'''
0     a
4     b
8     c
12    d
13    e
dtype: object
'''

print(s2.drop_duplicates())
'''
   A  B
0  1  a
3  2  b
5  3  b
6  4  c
7  1  U
'''

# 指定对某一列进行去重（A 列）
print(s2.drop_duplicates(['A']))
'''
   A  B
0  1  a
3  2  b
5  3  b
6  4  c
'''

# 统计重复值出现的次数
print(s1.value_counts())
'''
a    4
b    4
c    4
d    1
e    1
Name: count, dtype: int64
'''

##### unique() 得到唯一值；


### 替换：replace()
# 前面是旧值，后面是新值
s = pd.Series(list('aaabbbcde'))
print(s)
'''
0    a
1    a
2    a
3    b
4    b
5    b
6    c
7    d
8    e
dtype: object
'''

# 将里面的 c 替换成空值
print(s.replace('c', np.NaN))
'''
0      a
1      a
2      a
3      b
4      b
5      b
6    NaN
7      d
8      e
dtype: object
'''

# 批量指定替换
print(s.replace({'a': 'Python', 'b': 'Golang'}))
'''
0    Python
1    Python
2    Python
3    Golang
4    Golang
5    Golang
6         c
7         d
8         e
dtype: object
'''

## str 属性

df = pd.DataFrame({
    'k1': list('abcdef'),
    'k2':['h', 'haha', 'six', 'star', np.nan, '123']
})
print(df)
'''
  k1    k2
0  a     h
1  b  haha
2  c   six
3  d  star
4  e   NaN
5  f   123
'''
### count() 统计元素出现的次数

s = pd.Series(list('aaabbbcde'))

# 统计 a 出现的次数
print(s.str.count('a'))
'''
0    1
1    1
2    1
3    0
4    0
5    0
6    0
7    0
8    0
dtype: int64
'''

### lower() 将字符串中的大字母变换成小写


### upper() 将字符串中的小写字母替换成大写
# 把 k1 列变成大写
print(df['k1'].str.upper())
'''
dtype: int64
0    A
1    B
2    C
3    D
4    E
5    F
'''

df['k3'] = df['k1'].str.upper()
print(df)
'''
  k1    k2 k3
0  a     h  A
1  b  haha  B
2  c   six  C
3  d  star  D
4  e   NaN  E
5  f   123  F
'''


### len() 求长度

### startswith() 表示以什么开头

### endswith() 表示以什么结尾

### replace() 替换
df = pd.DataFrame(np.random.randn(3, 2), columns=[' col A', 'col B'], index=range(3))
print(df)
'''
# 注意： col A 前面是有空格的
      col A     col B
0 -0.328012  1.339511
1 -1.614227 -1.658222
2 -1.599471 -1.161017
'''

df.columns = df.columns.str.replace(' ', '-')
print(df)
'''
     -col-A     col-B
0 -0.328012  1.339511
1 -1.614227 -1.658222
2 -1.599471 -1.161017
'''



### split() 分割,

# 要处理的数据必须是字符串个是
s = pd.Series(['a, b, c', '1, 2, 3', np.nan, ['a, a, a']])
print(s)
'''
0      a, b, c
1      1, 2, 3
2          NaN
3    [a, a, a]
dtype: object
'''

# 按逗号分割
print(s.str.split(','))
'''
0    [a,  b,  c]
1    [1,  2,  3]
2            NaN
3            NaN
dtype: object
'''

# 取整个分割的第一个单元格
# 注意：这种方式取值会视整个数据为一个嵌套列表，即类似于 [[a,  b,  c], [1,  2,  3], [NaN], [Nan]]，而[0] 表示取索引为 0 行对应的数据
print(s.str.split(',')[0])
'''
['a', ' b', ' c']
'''

# str 定位到单元格内，取单元格内的第一个元素
# 注意：下面这种方式取值是定位每个子列表中的索引进行取值
print(s.str.split(',').str[0])
'''
0      a
1      1
2    NaN
3    NaN
dtype: object
'''

# 拆分
print(s.str.split(',', expand=True))
'''
     0    1    2
0    a    b    c
1    1    2    3
2  NaN  NaN  NaN
3  NaN  NaN  NaN
'''

### extract() 提取

# 通过正则表达方式提取内容
data = {
    'info': ['Jone 24', 'Lucy 30', 'Bob 28']
}
df = pd.DataFrame(data)

df['name'] = df['info'].str.extract(r'(\w+)')
print(df)

df['age'] = df['info'].str.extract(r'(\d+)')
print(df)


df = pd.DataFrame({
    'k1': ['a, b, c', '1, 2, 3', ['a, .,  ']],
    'k2': ['a-b-c', '1-2-3', ['-,:- ']]
})

### 字符串索引 通过下标取值



### str 属性生活案例
data = {
    '姓名': ['lily Smith', 'lucy Johnson', 'jack Brown', 'tom Lee'],
    '入职日期': ['2022-05-15', '2020-10-01', '2019-07-20', '2023-03-10']
}
df = pd.DataFrame(data)
print(df)
'''
             姓名        入职日期
0    lily Smith  2022-05-15
1  lucy Johnson  2020-10-01
2    jack Brown  2019-07-20
3       tom Lee  2023-03-10
'''

# 需求：将名首字母大写
df['名'] = df['姓名'].str.split(' ').str[0].str.capitalize()
print(df)
'''
             姓名        入职日期     名
0    lily Smith  2022-05-15  Lily
1  lucy Johnson  2020-10-01  Lucy
2    jack Brown  2019-07-20  Jack
3       tom Lee  2023-03-10   Tom
'''

## 分组聚合
df = pd.DataFrame({
    'name': ['BOSS', 'Lilei', 'Lilei', 'Han', 'BOSS', 'BOSS', 'Han', 'BOSS'],
    'Year': [2020, 2020, 2020, 2020, 2021, 2021, 2021, 2021],
    'salary': [999999, 20000, 25000, 5000, 9999999, 999999, 7500, 999999],
    'Bouns': [100000, 20000, 20000, 5000, 200000, 300000, 3000, 400000]
})
print(df)
'''
    name  Year   salary   Bouns
0   BOSS  2020   999999  100000
1  Lilei  2020    20000   20000
2  Lilei  2020    25000   20000
3    Han  2020     5000    5000
4   BOSS  2021  9999999  200000
5   BOSS  2021   999999  300000
6    Han  2021     7500    3000
7   BOSS  2021   999999  400000
'''


print(df.groupby('name').count())
'''
       Year  salary  Bouns
name                      
BOSS      4       4      4
Han       2       2      2
Lilei     2       2      2
'''


print(df.groupby('name').mean())
'''
          Year     salary     Bouns
name                               
BOSS   2020.75  3249999.0  250000.0
Han    2020.50     6250.0    4000.0
Lilei  2020.00    22500.0   20000.0
'''



data = {
    'Category': ['A', 'A', 'B', 'B', 'A', 'B'],
    'Value': [1, 2, 3, 4, 5, 6]
}

df = pd.DataFrame(data)
print(df)
'''
  Category  Value
0        A      1
1        A      2
2        B      3
3        B      4
4        A      5
5        B      6
'''

print(df.groupby('Category').agg({'Value': ['sum', 'mean', 'max']}))
'''
           sum      mean max
Category                    
A            8  2.666667   5
B           13  4.333333   6
'''