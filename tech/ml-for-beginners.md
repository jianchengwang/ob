---
title: ml-for-beginners
slug: ml-for-beginners
author: [jianchengwang]
date: 2021-09-10
excerpt: "微软出品的机器学习教程，这里做个学习记录。"
draft: false
tags: [ml]
---

[ML-For-Beginners](https://github.com/microsoft/ML-For-Beginners) 

微软出品的机器学习教程，这里做个学习记录。

## Introduction to regression

### env

```shell
# install python3 https://www.python.org/downloads/
# creating virtual environments https://docs.python.org/3/library/venv.html
python3 -m venv /path/to/new/virtual/environment
# update pip
python3 -m pip install --upgrade pip
# config pip source
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
# install pandas
pip3 install pandas -i https://mirrors.aliyun.com/pypi/simple
# install matplotlib
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple matplotlib
# install scikit-learn https://scikit-learn.org/stable/install.html
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple scikit-learn
# install jupyter https://pypi.org/project/jupyter/
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple jupyter
# install pytorch
pip3 install torch torchvision
# d2l
pip3 install -U d2l
```

### data

```python
# import libraries
import matplotlib.pyplot as plt
import numpy as np
from sklearn import datasets, linear_model, model_selection

```

