---
title: image-similarity-flask
slug: image-similarity-flask
author: [jianchengwang]
date: 2021-10-28
excerpt: "这几天公司要搞一个茶饼图像识别的功能，就是拍摄茶饼图片，跟已录入茶饼的图片记录对比，筛选出相似度最高的茶饼图片，有点类似谷歌的以图搜图。这边简单搞了个demo，记录下"
draft: false
tags: 
- code
---

这几天公司要搞一个茶饼图像识别的功能，就是拍摄茶饼图片，跟已录入茶饼的图片记录对比，筛选出相似度最高的茶饼图片，有点类似谷歌的以图搜图。这边简单搞了个demo，记录下。

no bb, show me code，可以直接查看[源码示例](https://github.com/jianchengwang/todo-ml/tree/main/image-similarity-flask)

## 技术选型

### 基本流程

---

![架构设计](https://blog.res.jianchengwang.info/posts/image-similarity-flask/process.png)

---

基本流程图如上所示，有点丑，手残党，凑合看，下面进行简单概述，

主要分为两个接口：

1. **upload**

   用户批量上传图片文件 -> `cnn siamese netwrok` 提取图片特征 -> `lbp`算法提取`lbp特征` -> `[imagename, features, lbps]` 保存到`h5`文件存储

2. **match**

   用户上传要识别的图片 -> `cnn siamese network` 提取图片特征 -> h5文件里面保存的特征值进行对比 -> 筛选出相似度超过阈值比如0.9以上的数据 `network match images`

   -> `network match images`的`lbps`跟`label`进行`svc model`训练 -> 提取要识别的图片的`lbps` -> 训练后的`svc model`预测上传图片的`label` 

   -> `network match images` 匹配label为`svc model`预测的数据 -> 返回

### 相关技术

1. web层 flask
2. 机器学习相关类库，`tensorflow-keras`，`opencv` 
3. 算法模型，`cnn siamese netwrok`，`svc`，`lbp`

## 详细设计

因为机器学习很多python类库，所以采用python语言进行编码实现。web层面就不都做介绍了，就是简单的flask实现两个接口。

### siamese network

`tensorflow-kerase `已经内置了很多`CNN`模型，感兴趣的可以自行了解，https://keras.io/api/applications/

之前我们采用`ResNet50`模型进行图像特征的提取，发现如果对实物拍摄的旋转角度不大的话，相似度可以达到**95%**以上，但是一旦角度差异很大的话，相似度就只有**87%**左右，这是因为，卷积神经网络，只具有很小的的**旋转不变性**，主要通过设置`maxpooling`来实现的，但是如果旋转角度太大，就会失效，感兴趣的可以参考以下几个链接，

https://www.quora.com/How-is-a-convolutional-neural-network-able-to-learn-invariant-features

https://benanne.github.io/2015/03/17/plankton.html

https://homes.cs.washington.edu/~pedrod/papers/nips14.pdf

所以，一般需要一定`data augmentation`训练才可以，因为手头没有太多数据，后面发现了`siamese network`感觉很符合我们的预期，

下面简单介绍一下`siamese network`，套用 [Sean Benhur](https://towardsdatascience.com/a-friendly-introduction-to-siamese-networks-85ab17522942)的话，

- Siamese networks contain two (or more) *identical* subnetworks.
- These subnetworks have the *same* architecture, parameters, and weights.
- Any parameter updates are *mirrored* across both subnetworks, meaning if you update the weights on *one*, then the weights in the *other* are updated as well.

比如，我们的任务是检测签名伪造。与其训练一个分类模型来正确地对数据集中的每个独特个体的签名进行分类(这将需要大量的训练数据)，不如我们从训练集中提取两幅图像，并询问神经网络这些签名是否来自同一个人。

另一个额外的好处是，当我们的分类模型在进行分类时不需要选择上述任何一个时(这在实践中非常容易出错)，我们不再需要一个全能类。相反，我们的暹罗网络通过报告两个签名不相同来优雅地处理这个问题。

请记住，暹罗网络体系结构不需要关注传统意义上的分类，即必须从N个可能的类中选择1个。相反，暹罗网络只需要能够报告相同(属于同一类)或不同(属于不同类)。

siamese network architecture used in Dey et al.’s 2017 publication, *[SigNet: Convolutional Siamese Network for Writer Independent Offline Signature Verification](https://arxiv.org/abs/1707.02131):*

![siamese_image_pairs_signet](https://blog.res.jianchengwang.info/posts/image-similarity-flask/siamese_image_pairs_signet.png)

在左边，我们向SigNet模型提供两个签名。我们的目标是确定这些签名是否属于同一个人。

中间是暹罗网络本身。这两个子网具有相同的结构和参数，如果更新了其中一个子网的权值，那么其他子网的权值也会同步更新。

这些子网络的最后一层通常(但不总是)是嵌入层，我们可以在其中计算输出之间的欧氏距离，并调整子网络的权值，以便它们输出正确的决策(是否属于同一类)。

右边显示了我们的损失函数，它结合了子网络的输出，然后检查暹罗网络是否做出了正确的决定。

我们这边使用`keras`深度框架来实现，

下面是一张`keras siamese network process`流程



![keras_siamese_networks_process](https://blog.res.jianchengwang.info/posts/image-similarity-flask/keras_siamese_networks_process.png)



训练`siamese network`时常用的损失函数包括

- Binary cross-entropy
- Triplet loss
- Contrastive loss

我们这边使用官方的示例代码，https://keras.io/examples/vision/siamese_network/

示例代码提供了，`left`，`right`各6000多张的孪生图片，但是这个实际跟我们要的有点差异，因为我们主要是需要提升**旋转不变**的特性，

所以，我们这边对，left文件夹的图片，通过`opencv`随机旋转角度处理得到我们预期的训练集right，然后进行训练即可，我这边是，`epochs=500`进行设置，

**[GeForce RTX 2070 SUPER]** 的显卡大概跑了8个小时，还是挺久的，所以建议使用有显卡的电脑进行[tensorflow-gpu](https://www.tensorflow.org/install/gpu?hl=zh-cn)配置，用cpu感觉要跑好几天。。

```python
embedding.save('siamese_network.h5')
```

最终得到我们想要的`siamese_network.h5`模型，后面就可以通过这个模型进行图片特征值的提取了，

```python
siamese_network = tf.keras.models.load_model(os.path.join(utils.MODELS, "siamese_network.h5"))
feat = siamese_network.predict(img)
```

### lbp

使用训练好的`siamese_network`如果是同一张图片进行旋转或者稍微剪辑之后，相似度从通用的`ResNet50`的**88%**到**98%**，当然这跟训练数据有关，仅供参考。

但是如果是局部特征不明显的数据，比如同样的茶叶，摆成多堆，比如， A，B两堆，[A1,A2]是A茶堆不同角度的图片，其中A1为正面，A2为其他角度，

[B1,B2]类似，如果拿A2取检索，会发现，A1，跟B1，B2的相似度会很接近，都达到了**92%**左右，这显然不符合我们的预期，因为我们只想查到A。

所以我们要对相似度达到一定**阈值**，比如**90%**以上的数据，再进一步进行处理。

所以，就要提高局部纹理特征的识别，这时候就要使用 [Local Binary Patterns](https://en.wikipedia.org/wiki/Local_binary_patterns) 算法了，而且`LBP`算法本身就具有旋转的健壮性，

感兴趣的可以查阅以下相关链接，

 *[Multiresolution Grayscale and Rotation Invariant Texture Classification with Local Binary Patterns](http://www.outex.oulu.fi/publications/pami_02_opm.pdf)* 

[Haralick texture features](http://haralick.org/journals/TexturalFeatures.pdf)

[Gray Level Co-occurrence Matrix](http://www.mathworks.com/help/images/gray-level-co-occurrence-matrix-glcm.html)

构造LBP纹理描述符的第一步是将图像转换为灰度。对于灰度图像中的每个像素，我们在中心像素周围选择一个大小为r的邻域。然后计算该中心像素的LBP值，并存储在与输入图像相同宽度和高度的输出2D数组中。

例如，让我们看看原始的LBP描述符，它像这样在固定的3 × 3像素邻域上操作

![lbp_thresholding](https://blog.res.jianchengwang.info/posts/image-similarity-flask/lbp_thresholding.jpg)

在上图中，我们取中心像素(用红色突出显示)，并根据其8像素的邻域对其进行阈值设置。如果中心像素的强度大于等于其相邻像素的强度，则设该值为1;否则，我们设它为0。对于8个周围像素，我们总共有2 ^ 8 = 256种可能的LBP编码组合

然后，我们需要计算中心像素的LBP值。我们可以从任何邻近的像素开始，顺时针或逆时针地工作，但我们的顺序必须保持一致，我们的图像中的所有像素和我们的数据集中的所有图像。给定一个3 × 3的邻域，那么我们就有8个邻域需要进行二进制测试。这个二进制测试的结果存储在一个8位数组中，然后我们将其转换为十进制，就像这样

![lbp_calculation-1024x299](https://blog.res.jianchengwang.info/posts/image-similarity-flask/lbp_calculation-1024x299.jpg)

在这个例子中，我们从右上角的点开始，沿着顺时针方向积累二进制字符串。然后我们可以将这个二进制字符串转换为十进制，得到值23

此值存储在输出LBP 2D阵列中，如下所示：

![lbp_to_output-1024x519](https://blog.res.jianchengwang.info/posts/image-similarity-flask/lbp_to_output-1024x519.jpg)

然后对输入图像中的每个像素重复这个阈值、积累二进制字符串并将输出的十进制值存储在LBP数组中的过程。

下面是一个计算和可视化完整LBP 2D数组的示例

![lbp_2d_array](https://blog.res.jianchengwang.info/posts/image-similarity-flask/lbp_2d_array.jpg)

最后一步是计算输出LBP数组的直方图。由于一个3 × 3的邻域有2 ^ 8 = 256种可能的模式，因此我们的LBP 2D数组的最小值为0，最大值为255，允许我们构建一个256-bin的LBP代码直方图作为最终的特征向量

![lbp_256bin_histogram-1024x648](https://blog.res.jianchengwang.info/posts/image-similarity-flask/lbp_256bin_histogram-1024x648.jpg)

这个原始LBP实现的一个主要好处是，我们可以在图像中捕获非常细粒度的细节。然而，能够在如此小的尺度上捕捉细节也是该算法的最大缺点，我们不能在不同的尺度上捕捉细节，只有固定的3 × 3尺度

为了解决这个问题，Ojala等人提出了对原始LBP实现的扩展，以处理可变的邻域大小。为了解释可变的邻域大小，引入了两个参数

1. The number of points *p* in a circularly symmetric neighborhood to consider (thus removing relying on a square neighborhood).
2. The radius of the circle *r*, which allows us to account for different scales.

![lbp_num_points_radii](https://blog.res.jianchengwang.info/posts/image-similarity-flask/lbp_num_points_radii.jpg)

最后，重要的是我们考虑LBP均匀性的概念。如果最多两个0-1或1-0转换，则认为LBP是均匀的。例如，模式00001000（2转变）和10000000（1转变）被认为是均匀的图案，因为它们最多包含两个0-1和1-0转换。另一方面，图案01010010）不被认为是均匀的模式，因为它具有六个0-1或1-0转换。

局部二进制模式中统一原型的数量完全依赖于点p的数量。随着p值的增加，结果直方图的维数也会增加。请参考Ojala et al.的原始论文，对基于该值得出图案数量和统一图案的完整解释。然而，暂时只需要记住，给定LBP中的点p的数量，就有p + 1个统一模式。直方图的最终维度是p + 2，其中添加的条目列出了所有不均匀的模式。

那么，为什么统一的LBP模式如此有趣呢?简单地说，它们增加了一层额外的旋转和灰度不变性，因此通常用于从图像中提取LBP特征向量。

本地二进制模式实现可以在 [scikit-image](http://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.local_binary_pattern) and [mahotas](http://mahotas.readthedocs.org/en/latest/lbp.html) 包中找到。

OpenCV也实现了LBP，但严格地在人脸识别的背景下，底层的LBP提取器不会暴露于原始的LBP直方图计算。

所以这边使用scikit-image实现

```python
from skimage import feature
class LocalBinaryPatterns:
	def __init__(self, numPoints, radius):
		# store the number of points and radius
		self.numPoints = numPoints
		self.radius = radius
	def describe(self, image, eps=1e-7):
		# compute the Local Binary Pattern representation
		# of the image, and then use the LBP representation
		# to build the histogram of patterns
		lbp = feature.local_binary_pattern(image, self.numPoints,
			self.radius, method="uniform")
		(hist, _) = np.histogram(lbp.ravel(),
			bins=np.arange(0, self.numPoints + 3),
			range=(0, self.numPoints + 2))
		# normalize the histogram
		hist = hist.astype("float")
		hist /= (hist.sum() + eps)
		# return the histogram of Local Binary Patterns
		return hist
```

提取了图片的lbp，然后因为我们存储的图片是符合一定命名规范的，比如`a-000001.jpg`表示茶饼a的第一张图片，`a-000002.jpg`表示茶饼a的第二张图片，所以比如我们可以通过`a-000001.jpg`得到`a`作为`labels`，然后提取的lbp作为`features`，训练我们的 [Linear Support Vector Machine](https://en.wikipedia.org/wiki/Support_vector_machine#Linear_SVM)，学习不同纹理类之间的区别。

```python
# from sklearn.svm import LinearSVC
from sklearn import svm
# train
svc_model = svm.SVC(C=100.0, cache_size=1000, random_state=42)
svc_model.fit(data, labels)
# predict
prediction = svc_model.predict(testHist.reshape(1, -1))
```

然后我们获取输入图片的lbp，让svm模型识别出对应的label，再从`siamese netwrok`已经筛选出的相似度90%以上图片集，再进行label的匹配，即可得到指定较高相似度的图片集合。

## 相关链接

[keras guide](https://keras.io/guides/)

[siamese-networks-with-keras-tensorflow-and-deep-learning](https://www.pyimagesearch.com/2020/11/30/siamese-networks-with-keras-tensorflow-and-deep-learning/)

[local-binary-patterns-with-python-opencv](https://www.pyimagesearch.com/2015/12/07/local-binary-patterns-with-python-opencv/)

[image-similarity-flask](https://github.com/jianchengwang/todo-ml/tree/main/image-similarity-flask)

