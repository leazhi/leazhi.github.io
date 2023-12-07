---
title: 数据分析 003-pandas（一）
author: leazhi
tags:
  - [python]
  - [pandas]
categories:
  - [python]
  - [datanalysis]
date: 2023-10-20 16:53:30
cover: 
discription: 
keywords: 
password: 
message: 
---

## pandas 简介

pandas 是基于 numpy 构建的，用于数据分析的库，提供了大量能快速便捷地处理数据的函数和方法。

pandas 提供了大量能快速便捷地处理数据的函数和方法，特别是处理时间序列的数据，它比 Python 内置的 datetimes 模块和 Numpy 更强大。

pandas 包含以下几个核心数据结构：

- Series：一维数据结构，与 Numpy 中的 ndarray 类似，由一组数据以及一组与之相关的数据标签（即索引）组成。
- DataFrame：二维数据结构，用类似表格的形式表示数据。
- Panel：三维数据结构，可以理解为 DataFrame 的容器。

## pandas 安装
```python
pip install pandas
```

## Series 

Series 通常是指 Excel 中的列，一列为 一个 Series ,多个 Series 就组成了 DataFrame。

### 数据生成

#### 通过 numpy 的方式生成

Series 生成数据时会自动生成索引（在不指定索引的情况下，默认生成位置索引，且位置从 0 开始）和数据，如下：

```python
import numpy as np
import pandas as pd

S = pd.Series(np.random.rand(5))
print(S)
'''
0    0.709380
1    0.002081
2    0.250082
3    0.299655
4    0.836594
dtype: float64
'''

# 获取索引（位置索引）
print(S.index)
'''
RangeIndex(start=0, stop=5, step=1)
'''

# 获取值
print(S.values)
'''
[0.76221382 0.33324289 0.9359301  0.23841135 0.3779343 ]
'''
```


#### 通过字典的方式生成

```python
dic = {"red": 100, "green": 300, "black": 500, "pink": 900}

s1 = pd.Series(dic)
'''
red      100
green    300
black    500
pink     900
dtype: int64
'''

# 获取索引（标签索引）
print(s1.index)
'''
Index(['red', 'green', 'black', 'pink'], dtype='object')
'''

# 获取值
print(s1.values)
'''
[100 300 500 900]
'''
```

#### 自定义索引

```python
# 不指定索引
arr = np.random.randn(5)
s2 = pd.Series(arr)
print(s2)
'''
0   -0.542155
1   -0.316450
2   -1.364619
3    1.486984
4    0.483951
dtype: float64
'''

# 指定索引
s = pd.Series(arr, index = ['a', 'b', 'c', 'd', 'e'], dtype=object)
print(s)
'''
a   -0.542155
b    -0.31645
c   -1.364619
d    1.486984
e    0.483951
dtype: object
'''
```

#### 通过索引取值

取但个值
```python
sel = pd.Series(data=[1, 2, 3, 4], index=list('abcd'))
print(sel)
'''
a    1
b    2
c    3
d    4
dtype: int64
'''

## 获取数字 2：
# 通过标签索引
print(sel['b'])
'''
2
'''

# 通过位置索引
print(sel[1])
'''
2
'''
```

取多个不连续的值，要多加一层 []
```python
# 通过位置索引
print(sel[[0,2]])
'''
a    1
c    3
'''

# 通过标签索引
print(sel[['a','c']])
'''
a    1
c    3
dtype: int64
'''
```

取多个连续的值。**<font color="red">需要注意的是</font>**：通过标签索引切片，结束标签会包含；通过位置标签进行切片，结束标签不包含。
```python
# 通过标签索引
print(sel['a':'c'])
'''
a    1
b    2
c    3
dtype: int64
'''

# 通过位置索引
print(sel[0:2])
'''
a    1
b    2
dtype: int64
'''
```

#### 布尔索引

凡是遇到判断的值只有两种： True ,False

```python
s = pd.Series(np.random.rand(3)*100)
s[4]=None
print(s)
'''
0    66.771276
1    96.794972
2     2.747238
4          NaN
'''

print(s[s>50])      # 中括号是条件，按条件筛选
'''
0    66.771276
1    96.794972
dtype: float64
'''
```

#### 改

同字典删除数据一样，通过 [KEY] 进行修改：

```python
s = pd.Series(np.random.rand(3), index=['a', 'b', 'c'])
print(s)
'''
a    0.828145
b    0.550867
c    0.067155
dtype: float64
'''

s['b'] = 1213
print(s)
'''
a       0.828145
b    1213.000000
c       0.067155
dtype: float64
'''
```

通过 + 号的方式进行修改

``` python
1 = pd.Series(np.random.rand(3), index=['Jack', 'Marry', 'Willia'])
print(s1)
s2 = pd.Series(np.random.rand(3), index=['Wang', 'Willia', 'Marry'])
print(s2)
print(s1 + s2)
'''
Jack      0.648875
Marry     0.669368
Willia    0.520387
dtype: float64
Wang      0.474069
Willia    0.186597
Marry     0.446670
dtype: float64
Jack           NaN
Marry     1.116038
Wang           NaN
Willia    0.706983
dtype: float64
'''
```

#### 删

同字典删除数据一样，通过 drop[KEY] 进行删除：
```python
print(s.drop('b'))
dtype: float64
a       0.828145
c       0.067155
```

### reindex

打乱原本数据的顺序重新组合，索引和值依旧一一对应，对于没有值的索引，默认会使用 Nan 进行填充（为了数据好分析，一般都会使用 fill_value=0 参数进行填充，即将原本的 Nan 修改为 0）

```python
s = pd.Series(np.random.rand(3), index=['a', 'b', 'c'])
print(s)
'''
a    0.809258
b    0.711983
c    0.279286
dtype: float64
'''


print(s.reindex(['c','b', 'd', 'a'], fill_value=0))             # fill_value 填充，不加该参数返回的 Nan
'''
c    0.279286
b    0.711983
d    0.000000
a    0.809258
dtype: float64
'''
```

## DataFrame

### 通过二维数组进行创建
```python
df1 = pd.DataFrame(np.random.randint(0, 20,(4,5)), index=[1, 2, 3, 4], columns=['a', 'b', 'c', 'd', 'e'])
print(df1)

'''
    a   b   c   d   e
1   4   3  13   1  12
2   3   6   2   1   4
3  12  14  10   9   2
4  17  11   0  15   1
'''
```

### 通过字典进行创建

```python
data1 = {'a': [1,2,3],
         'b': [4,5,6],
         'c': [7,8,9]}
print(pd.DataFrame(data1, index=['A', 'B', 'C']))
'''
   a  b  c
A  1  4  7
B  2  5  8
C  3  6  9
'''
```

### 由字典组成的列表创建

```python
data2 = [{'One':1, 'Two':2}, {'One':3, 'Two':2, 'Three':3}]
print(pd.DataFrame(data2))
'''
   One  Two  Three
0    1    2    NaN
1    3    2    3.0
'''
```

### 由字典组成的字典创建：
```python
data3 = {
    'Jack': {'math':90, 'english':89, 'art':78},
    'Marry': {'math':82, 'english':95, 'art':92},
    'Tom': {'math':78, 'english':67}
}
print(pd.DataFrame(data3))
'''
         Jack  Marry   Tom
math       90     82  78.0
english    89     95  67.0
art        78     92   NaN
'''
```


### 常用属性

测试数据：
```python
data = {
    'name': ['Jack', 'Tom', 'Marry'],
    'age': [18, 23,29],
    'gender': ['F', 'M', 'F']
}

df = pd.DataFrame(data, index=['a', 'b', 'c'])

print(df)
'''
    name  age gender
a   Jack   18      F
b    Tom   23      M
c  Marry   29      F
'''
```

#### shape 

查看数组有几行几列
```python
print(df.shape)
'''
(3, 3)
'''
```

#### index.tolist()

转成列表

```python
print(df.index.tolist())

'''
['a', 'b', 'c']
'''
```

#### 查看元素类型

```python
print(df.dtypes)
'''
name      object
age        int64
gender    object
dtype: object
'''
```

#### head

查看前几行，默认为 5 行：
```python
print(df.head(2))
'''
   name  age gender
a  Jack   18      F
b   Tom   23      M
'''
```

#### tail

查看组后几行，默认 5 行：
```python
print(df.tail(2))
'''
    name  age gender
b    Tom   23      M
c  Marry   29      F
'''
```

#### values

获取数组所有的值
```python
print(df.values)
'''
[['Jack' 18 'F']
 ['Tom' 23 'M']
 ['Marry' 29 'F']]
'''
```

#### 取值

```python
# 单列
print(df['name'])
'''
a     Jack
b      Tom
c    Marry
Name: name, dtype: object
'''

# 多列
print(df[['name', 'gender']])
'''
    name gender
a   Jack      F
b    Tom      M
c  Marry      F
'''

# 获取第一行的数据
print(df[0:1])      #没有逗号，默认取行
'''
   name  age gender
a  Jack   18      F
'''

# 取某一行中指定数据
print(df[1:2][['name', 'gender']])
'''
  name gender
b  Tom      M
'''

# 取多行多列
print(df[0:2][['name', 'gender']])
'''
   name gender
a  Jack      F
b   Tom      M
'''
```

#### loc 标签索引取值和 iloc 位置索引取值

##### loc 标签索引取值

比如这里我只要取名为 jack 的值：
```python
print(df.loc['a', 'name'])
'''
Jack
'''

# 取多列
print(df.loc[:, ['name', 'gender']])
'''
    name gender
a   Jack      F
b    Tom      M
c  Marry      F
'''
```

##### iloc 位置索引取值

```python
print(df.iloc[0,0])
'''
Jack
'''
```

#### sort_index 索引排序 

默认是升序排序
```python
df1 = pd.DataFrame(np.random.rand(16).reshape(4, 4)*100, index=[5, 4, 3, 2], columns=['a', 'b', 'c', 'd'])
print(df1)
'''
           a          b          c          d
5  89.978904  41.536659  76.601070  49.982262
4  34.973373  12.758041  31.577549  35.007390
3  31.635138  67.699514   4.135434   4.505784
2  70.981724  65.972904  45.914560  24.745171
'''

print(df1.sort_index())
'''
           a          b          c          d
2  70.981724  65.972904  45.914560  24.745171
3  31.635138  67.699514   4.135434   4.505784
4  34.973373  12.758041  31.577549  35.007390
5  89.978904  41.536659  76.601070  49.982262
'''
```

#### sort_values 值排序

默认不加 ascending 按升序排序， ascending=False ，表示降序排序

```python
print(df1.sort_values(by='b', ascending=False))
'''
3  31.944121  88.272019  92.172724  98.170657
4  82.026420  82.737898  72.667160  64.480761
5  56.825245  54.278953  29.378852  76.574349
2  47.482281  33.095362  84.989521  91.769285
'''
```


#### rank 排名

先出现的排名在前，后出现的排名在后
```python
s1 = pd.Series([7, -5, 7, 4, 2, 0, 4])
print(s1)
'''
0    7
1   -5
2    7
3    4
4    2
5    0
6    4
dtype: int64
'''

print(s1.rank(method='first'))
0    6.0
1    1.0
2    7.0
3    4.0
4    3.0
5    2.0
6    5.0
dtype: float64
```





## 实用案例

### 案例一：在同个工作簿里面写入多个表格数据

```python
data1 = {
    'name': ['Rose', 'Jack', 'Tom'],
    'age': [20, 30, 35],
    'city': ['CS', 'BJ', 'SH']
}

df1 = pd.DataFrame(data1)


data2 = {
    'product': ['A', 'B', 'C'],
    'price': [20, 30, 35],
    'quantity': [100, 200, 300]
}

df2 = pd.DataFrame(data2)

with pd.ExcelWriter('Haoke.xlsx', engine='openpyxl') as ex:
    df1.to_excel(ex,sheet_name='表一', index=False)
    df1.to_excel(ex,sheet_name='表二', index=False)
```
2：10：55 















