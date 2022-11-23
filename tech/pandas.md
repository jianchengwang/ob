---
title: pandas
slug: pandas
author: [jianchengwang]
date: 2020-10-06
excerpt: "[*pandas*](http://pandas.pydata.org/) 是一种列存数据分析 API。它是用于处理和分析输入数据的强大工具，很多机器学习框架都支持将 *pandas* 数据结构作为输入。 虽然全方位介绍 *pandas* API 会占据很长篇幅，但它的核心概念非常简单，我们会在下文中进行说明。有关更完整的参考，请访问 [*pandas* 文档网站](http://pandas.pydata.org/pandas-docs/stable/index.html)，其中包含丰富的文档和教程资源。"
draft: false
tags: [python]
---

[*pandas*](http://pandas.pydata.org/) 是一种列存数据分析 API。它是用于处理和分析输入数据的强大工具，很多机器学习框架都支持将 *pandas* 数据结构作为输入。 虽然全方位介绍 *pandas* API 会占据很长篇幅，但它的核心概念非常简单，我们会在下文中进行说明。有关更完整的参考，请访问 [*pandas* 文档网站](http://pandas.pydata.org/pandas-docs/stable/index.html)，其中包含丰富的文档和教程资源。

## 基本概念

首先安装一下 module，可以配置下pip源，或者 加参数`-i`提高下载速度

```shell
mkdir ~/.pip
vim ~/.pip/pip.conf
[global]
index-url = https://mirrors.aliyun.com/pypi/simple
```

```shell
pip3 install pandas -i https://mirrors.aliyun.com/pypi/simple
pip3 install matplotlib -i https://mirrors.aliyun.com/pypi/simple # 如果需要绘制图表
```

*pandas* 中的主要数据结构被实现为以下两类：

- **`DataFrame`**，您可以将它想象成一个关系型数据表格，其中包含多个行和已命名的列。
- **`Series`**，它是单一列。`DataFrame` 中包含一个或多个 `Series`，每个 `Series` 均有一个名称。

数据框架是用于数据操控的一种常用抽象实现形式。[Spark](https://spark.apache.org/) 和 [R](https://www.r-project.org/about.html) 中也有类似的实现。

创建`Series`

```python
city_names = pd.Series(['San Francisco', 'San Jose', 'Sacramento'])
population = pd.Series([852469, 1015785, 485199])

```

创建`DataFrame`，通过``Series``

```pyt
pd.DataFrame({ 'City name': city_names, 'Population': population })
```

更多时候，一般装载整个文件

```python
california_housing_dataframe = pd.read_csv("https://download.mlcc.google.com/mledu-datasets/california_housing_train.csv", sep=",")
```

当然，也可以通过复制表格内容

```python
df = pd.read_clipboard()
df.to_csv("some_data.csv")
```

`DataFrame` 常用方法

```python
# 显示统计信息
california_housing_dataframe.describe()
# 显示前几个记录
california_housing_dataframe.head()
# 绘制图表 
california_housing_dataframe.hist('housing_median_age')
```

## 访问数据

您可以使用熟悉的 Python dict/list 指令访问 `DataFrame` 数据：

```python
cities = pd.DataFrame({ 'City name': city_names, 'Population': population })
print(type(cities['City name']))
print(cities['City name'])
print(type(cities['City name'][1]))
print(cities['City name'][1])
```

 此外，**pandas** 针对高级[索引和选择](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html)提供了极其丰富的 API（数量过多，此处无法逐一列出）。

## 操控数据

您可以向 `Series` 应用 Python 的基本运算指令。例如：

```python
population / 1000
```

[NumPy](http://www.numpy.org/) 是一种用于进行科学计算的常用工具包。*pandas* `Series` 可用作大多数 NumPy 函数的参数：

```python
import numpy as np
np.log(population)
```

对于更复杂的单列转换，您可以使用 `Series.apply`。像 Python [映射函数](https://docs.python.org/2/library/functions.html#map)一样，`Series.apply` 将以参数形式接受 [lambda 函数](https://docs.python.org/2/tutorial/controlflow.html#lambda-expressions)，而该函数会应用于每个值。

下面的示例创建了一个指明 `population` 是否超过 100 万的新 `Series`：

```python
population.apply(lambda val: val > 1000000)
```

`DataFrames` 的修改方式也非常简单。例如，以下代码向现有 `DataFrame` 添加了两个 `Series`：

```python
cities['Area square miles'] = pd.Series([46.87, 176.53, 97.92])
cities['Population density'] = cities['Population'] / cities['Area square miles']
print(cities)
```

## 索引

`Series` 和 `DataFrame` 对象也定义了 `index` 属性，该属性会向每个 `Series` 项或 `DataFrame` 行赋一个标识符值。

默认情况下，在构造时，*pandas* 会赋可反映源数据顺序的索引值。索引值在创建后是稳定的；也就是说，它们不会因为数据重新排序而发生改变。

```python
print(city_names.index)
```

调用 `DataFrame.reindex` 以手动重新排列各行的顺序。例如，以下方式与按城市名称排序具有相同的效果：

```python
cities.reindex([2, 0, 1])
```

重建索引是一种随机排列 `DataFrame` 的绝佳方式。在下面的示例中，我们会取用类似数组的索引，然后将其传递至 `NumPy` 的 `random.permutation` 函数，该函数会随机排列其值的位置。如果使用此重新随机排列的数组调用 `reindex`，会导致 `DataFrame` 行以同样的方式随机排列。 尝试多次运行以下单元格！

```python
cities.reindex(np.random.permutation(cities.index))
```

## 练习

推荐使用 https://colab.research.google.com/ 练习，可以直接导入`ipynb`，练习库`github`上面有很多，这里不多赘述，底下相关链接也有列出。

## 相关链接

[colab intro to pandas](https://colab.research.google.com/notebooks/mlcc/intro_to_pandas.ipynb) 

[user guide](https://pandas.pydata.org/pandas-docs/stable/user_guide/10min.html)

[guipsamora/pandas_exercises](https://github.com/guipsamora/pandas_exercises)