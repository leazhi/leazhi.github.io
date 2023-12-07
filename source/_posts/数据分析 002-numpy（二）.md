---
title: 数据分析 002-numpy（二）
author: leazhi
tags:
  - [python]
  - [numpy]
categories:
  - [python]
  - [datanalysis]
date: 2023-10-18 16:59:29
cover: 
discription: 
keywords: 
password: 
message: 
---

## 数组的处理

生成测试数组：
```python
import numpy as np

arr1 = np.arange(10).reshape(2, 5)
print(arr1)
```

### 数组转置

矩阵转置
```python
print(arr1.T)           # 转置的内容不会被保存

# 等同于
print(np.transpose(arr1))
```


### 数组轴对换

轴的概念

## 常见的数组操作

测试数组：
```bash
# 一维数组
arr = np.array([7, 9, 6, 42, 9, 4, 8, 1, 2, 20])

# 二维数组
arr2 = np.array([[4, 5, 7, 15], [26, 33, 5, 3], [12, 6, 7, 4]])
```


### 获取数组从小到大的排序序号（索引）

```bash
print(arr.argsort())
```


### 排序

#### 一维数组
正序排序
```python
print(arr.sort())                   
```

反序排序（它没有 python 中的那个倒叙参数）
```python
print(arr.sort()[::-1])
```

#### 二维数组

正序排序
```bash
print(arr2.sort())

print(arr2.sort(axis=0))

print(arr2.sort(axis=1))
```

### 添加

数组的添加一定不要忘记了指定轴，否则添加后的数组会变成一维数组，列表类型
```bash
arr3 = np.array([[1, 2, 3], [4, 5, 6]])

print(np.append(arr3,[10, 11, 12]， axis=0))
```

使用 insert 添加
```bash
print(np.insert(arr3, 1, [7, 8, 9], axis=0))
```

### 删除

删除 0 轴是按行进行删除的， 1 轴是按列进行删除的

```bash
print(np.delete(arr3, 1, axis=0))
```

### 去重

```bash
u = np.unique(arr)
print(u)
```

### 拼接
```bash
a = np.array([[1, 2, 3], [4, 5, 6]])
b = np.array([[7, 8, 9], [10, 11, 12]])

print(np.concatenate((a, b), axis=0))

print(np.concatenate((a, b), axis=1))
```

### 堆叠

```bash
a = np.array([[1, 2, 3], [4, 5, 6]])
b = np.array([[7, 8, 9], [10, 11, 12]])

arr1 = np.hstack((a,b)) # 水平堆叠
arr2 = np.vstack((a,b)) # 垂直堆叠
print(arr1, arr2)
```

```bash
np.stack((a, b), axis = 1)              # 堆叠,生成三维数组
```

### 拆分

```bash
ar = np.arange(16).reshape(4, 4)
print(ar)

arr1 = np.hsplit(ar, 2) # 水平拆分
arr2 = np.vsplit(ar, 2) # 垂直拆分
print(arr1, arr2)
```

## 数组中的空和无线

### nan 空

```bash
a = np.nan
print(a)
```

### inf 无穷
```bash
b = np.inf
print(b)
```

## 数组函数

### 统计函数

```bash
exam_scores = np.array([85,78,92,88, 70, 95, 80, 85, 90 78, 84, 88, 92, 86, 75])


print(np.mean(exam_scores)) # 均值
print(np.median(exam_scores)) # 中位数
print(np.std(exam_scores)) # 标准差
print(np.var(exam_scores)) # 方差
print(np.max(exam_scores)) # 最大值
print(np.min(exam_scores)) # 最小值
```

### where 的用法

按条件进行筛选
```bash
a = np.array([[1,2], [3,4]])
b = np.array([[5,6], [7,8]])
cond = np.array([[True, False], [True, False]])

res = [(x if c  else y) for x, y, c in zip(a, b, cond)]

res = np.where(cond, a, b)
print(res)
```


如果只有一个条件，则返回的是满足条件的下标
```bash
w = np.array([10, 22, 55,15])
print(np.where(w > 10)) # 返回大于10的数组
```

```bash
arr1 = np.random.randn(4, 4)
np.where(arr1>0, 5, -5)         # 条件为真，则赋值5，否则赋值-5
```

## 通用函数

```bash
a = np.arange(-10, 0).reshape(2, 5)
print(a)

print(np.abs(a))                # 绝对值
print(np.exp(a))    # 指数
print(np.log(a))                # 自然对数,不写底数，默认底数为 e 
```

## 图像的灰度案例

```bash
import numpy as np
import matplotlib.pyplot as plt

# 读取图片
n1 = plt.imread('./data/lena.png')

plt.imshow(n1)                  # 图片展示（三维数组）
plt.show()                      # 展示图片


# 灰度处理
n2 = np.array([0.299, 0.587, 0.114])
x = np.dot(n1, n2)

plt.imshow(x, cmap='gray')
plt.show()

# 亮度处理
plt.figure(figsize=(10, 10)
n2 = n1* 1.88
n2=np.clip(n2, 0, 255)
plt.imshow(n2.astype('uint8'))
plt.show()
```
