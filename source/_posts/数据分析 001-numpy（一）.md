---
title: 数据分析 001-numpy（一）
author: leazhi
tags:
  - [python]
  - [numpy]
categories:
  - [python]
  - [datanalysis]
date: 2023-10-18 16:57:46
cover: 
discription: 
keywords: 
password: 
message: 
---



## numpy 的优势

当数据量达到一定级别后，numpy 的计算会比原生 Python 块。numpy 是专门处理高维苏剧计算的包
```bash
import random
import time
import numpy as np


# 原生 python
a = []
for i in range(10000000000):
  a.append(random.random())
t1 = time.time()
sum1 = sum(a)
t2 = time.time()


# numpy 
b = np.array(a)       # 列表转数组
t3 = time.time()
sum2 = np.sum(b)
t4 = time.time()

print(t2 - t1, t4 - t3)
```

特点：同类型的数据
```bash
import numpy as np

arr1 = np.array([3, 2,1.5,9])
print(arr1)

# 输出结果：
[3.  2.  1.5 9. ]
```

### numpy 的安装
```bash
pip install -y numpy
```

### numpy 的使用
```bash
import numpy as np
```

## 一维数组

### 直接传入列表的方式

```bash
arr1 = np.array([3, 2, 1.5, 9])
print(arr1, type(arr1))
```


### 传入 range 生成序列

```bash
b = np.array(range(10))
print(b, type(b))
```

### 使用 numpy 自带的 np.arange() 函数生成数组

```bash
arr2 = np.arange(0,10,2)    # 起始索引，结束索引，步长
print(arr2, type(arr2))
```

### 使用 np.linspace() 生成数组

返回在间隔[开始，结束]上计算的 num 个均匀间隔样本
```bash
arr3 = np.linspace(2.0, 3.0, num=5, retstep=True)   # 表示在 2 和 3 之间均匀生成 5 个数，retstep 表示是否返回步长
print(arr3, type(arr3))
```

## 二维数组

### 直接传入列表的方式

```bash
lst = [[1,2], [3,4], [5,6], [7,8,]
arr5 =np.array(lst)
print(arr5, type(arr5))

t = [(1,2), (3,4), (5,6), (7,8)]
arr6 = np.array(t)
print(arr6, type(arr6))
```

### 全 0 数组
```bash
np.zeros((3,4))       # 三行四列全0数组
```

### 全 1 数组
```bash
np.ones((2,5))        # 两行五列全1数组
```

### 空数组
```bash
np.empty((2,3))       # 两行三列空数组
```

## 基本属性

```bash
arr = np.array([1, 2, 3, 4, 5, 6, 7])
print(arr.shape)       # 返回数组的维度(几行几列)，如果是一维数组的话，只返回数组形式的行数
print(arr.dtype)       # 返回数组元素的数据类型
print(arr.size)        # 返回数组元素的数量
print(arr.ndim)        # 返回数组的维数
print(arr.itemsize)    # 返回数组中每个元素的字节大小
print(arr.nbytes)      # 返回数组中所有元素的字节大小
print(arr.T)           # 数组转置
```

## 数据类型转换
```bash
arr = np.array([6.8, 9.9, -5, 1, 3.3, 11.11])
arr2 = arr.astype(np.int32)
print(arr2, arr2.dtype)
```

### 创建数组时指定元素类型

```bash
arr3 = np.arange(10, dtype=np.float)
print(arr3, arr3.dtype)
```


## 索引和切片

### 索引

#### 一维数组

```bash
ar =np.arange(5, 20)
print(ar)                      # 输出数组
```

取出数组中的 10
```bash
print(ar[5])                  # 输出数组中的 10
```

取 7~10
```bash
print(ar[2:6]) #
```

取数组中所有的偶数
```bash
print(ar[ar%2==0])
print(ar[1::2])
```



#### 二维数组

```bash
ar2 = np.arange(16).reshape(4,4)      # 转换数组的形状
print(ar2)                            # 输出数组
```

取二维数组中 ar2 中的第 1 行
```bash
print(ar2[0])                       # 输出数组中的 1~4
```

取二维数组中 ar2 中的元素为 2 
```bash
print(ar2[0][2])
print(ar2[0,2])
```

取二维数组中 ar2 中的前3行
```bash
print(ar2[:3])
```

二维数组中 ar2 中的 5，6，9，10
```bash
print(ar2[1:3,1:3])       # 逗号前是行，逗号号是列
```


#### 三维数组

```bash
# arr3d = np.arange(8).reshape(2,2,2)   # 创建三维数组
arr3d = np.array([[[1,2,3],[4,5,6]],[7,8,9],[[1,1,1],[5,5,5],[9,9,9]]])
print(arr3d.shape)
```

取三维数组中 arr3d 中的元素 5
```bash
print(arr3d[0,1,1])
```

### 布尔索引

```bash
freinds = (['Jone', 'Jack', 'Rose', 'Lily', 'Tom'])
data = np.random.randn(5, 4)      # 5 行 4 列
print(data)

print(freinds=='Jack')
print(data[freinds=='Jack'])        # 返回第二行的数据

print(data[freinds=='Jack', 2])     # 返回第二行的数据中的第二个元素

# 取反
print(data[~(freinds=='Jack')])   
```

```bash
heights = np.array([1.73, 1.68, 1.71, 1.89, 1.79])
weights = np.array([65.4, 59.2, 63.6, 88.4, 68.7])

bmi = weights/heights**2
print(bmi)

```


## 作业

1.反转一个向量（第一个元素变为最后一个）
```bash
arr = np.arange(10)
print(arr)
print(arr[::-1])
```

2.创建一个 10 x 10 的矩阵，并找到它的最大值和最小值
```bash
arr = np.random.randn(10, 10)
print(arr)
print(arr.min())
print(arr.max())
```

3.创建一个二位数组，其中边界值为1，其余值为0
```bash
arr = np.ones((10, 10))
arr[1:-1, 1:-1] = 0
print(arr)
```

4.创建一个5x5的矩阵，其中值从-5到5
```bash
arr = np.arange(-5, 5, 0.1)
print(arr)
```



