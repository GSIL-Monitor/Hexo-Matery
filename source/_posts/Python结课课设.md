---
title: Python结课课设
categories: [语言]
tags: [Python,Numpy,Pandas,AI]
copyright: true
comments: true
top: false
author: XQ
date: 2018-11-27 19:47:26
updated: 2018-11-27 19:47:26
keywords:
description: 使用了numpy部分功能实现了一个简单的感知器算法
passwords:
img:
---

##### 1.代码分析

```python
#运行环境MacOS-Pycharm
#使用了numpy部分功能实现了一个简单的感知器算法

'''
所谓感知机，就是二类分类的线性分类模型，其输入为样本的特征向量，输出为样本的类别，取+1和-1二值，即通过某样本的特征，就可以准确判断该样本属于哪一类。顾名思义，感知机能够解决的问题首先要求特征空间是线性可分的，再者是二类分类，即将样本分为{+1, -1}两类。
'''

import numpy as np

class Perceptron:
 
    #损失
    #输入为：特征，标记，权值，偏置
    #输出为：dot
    #使用dot()描点，matmul()矩阵乘法
    @staticmethod
    def loss_function(train_feature, train_label, weight, biases):
        if train_feature.shape[1] == weight.shape[0] and train_feature.shape[0] == train_label.shape[0]:
            return - np.dot((np.matmul(train_feature, weight) + biases).flatten(), train_label.flatten())
        else:
            print(f'train_feature and weight_feature shape is {train_feature.shape[1] == weight.shape[0]}')
            print(f'train_feature and train_label shape is {train_feature.shape[1] == train_label.shape[0]}')
       
    #更新权值和偏置
    #输入为：学习率，权值，偏置，特征，标记
    #输出为：更新后的权值和偏置
    @staticmethod
    def update_weight_biases(learning_rate, weight, bias, features, label):
        """
        错误分类点来更新权重和偏置
        :param learning_rate: 学习率
        :param features: 特征
        :param label: 标记
        :return:
        """
        weight_update = (weight + (learning_rate * label * features)[:, np.newaxis])
        bias_update = (bias + learning_rate * label)

        return weight_update, bias_update

    #感知器
    #输入为：权值，偏置，特征
    #输出为：0、1、-1、prediction预测值
    @staticmethod
    def perceptron(weight, bias, features):
        def classify(row):
            if row[0] < 0:
                return -1
            elif row[0] > 0:
                return 1
            else:
                return 0

        result = np.matmul(features, weight) + bias     #矩阵乘
        prediction = np.apply_along_axis(classify, 1, result) #维度运算得到新数组，

        return prediction 
	#初始化
    #学习率默认0.1，最大step1000
    def __init__(self, learning_rate=0.1, max_steps=1000):
        self.learning_rate = learning_rate
        self.steps = max_steps
        
	#训练
    #输入：特征，标签
    #输出：空，主要是打印信息
    def train(self, features, label):
        weight_length = features.shape[1]  # 权重的长度获取
        bias = 0  # 偏置初始值
        weight = np.zeros(weight_length)[:, np.newaxis] #某一列零数组
        label = label[:, np.newaxis]  # 权重初始值
        error = self.loss_function(features, label, weight, bias)
        #         if error <= self.stop_error:
        #             self.weight = weight
        #             self.bias = bias
        #             self.error = error

        #         else:
        for _ in range(self.steps):
            label_hat = label.flatten() * (np.matmul(features, weight) + bias).flatten()  ## 预测
            if len(np.where(np.array(label_hat.flatten() > 0))[0]) == len(label_hat.flatten()): #where寻找符合条件的功能，即预测准确
                break

            else:
                index = np.where(np.array(label_hat.flatten() <= 0))[0]  ## 找到没预测准的数据
                wrong_result = np.random.choice(index) #随即值
                weight, bias = self.update_weight_biases(self.learning_rate,
                                                         weight,
                                                         bias,
                                                         features[wrong_result, :],
                                                         label[wrong_result])
                error = self.loss_function(features, label, weight, bias)
                if _ % 50 == 0:
                    print(f'After {_} steps, loss is {error}.')
                #                 if error <= self.stop_error:
                #                     break
                else:
                    continue
        print('Training finished.')
        self.weight = weight
        self.bias = bias
        self.error = error
	
    #模型参数
    #输出：权值偏置矩阵
    def model_parameters(self):
        return np.array([self.weight, self.bias])
    
	#拟合
    #输入：特征
    #输出：预测值
    def fit(self, features):
        return self.perceptron(self.weight, self.bias, features) 


if __name__ == '__main__':
    X = np.array([[3, 3], [4, 3], [1, 1],[2, 2]])
    Y = np.array([1, 1, -1, 1])
    perceptron = Perceptron(learning_rate=1)
    perceptron.train(X, Y)
    print(perceptron.model_parameters())
    print(perceptron.fit([[1, 1], [3, 3]]))
```

<center>感知器输出结果</center>

![屏幕快照.png](https://i.loli.net/2018/11/27/5bfd29484bca5.png)



##### 2.扩展部分

1.Python+第三方库的应用：`Python语法简单，适合编写大中小型程序，在人工智能独树一帜，由于python的特性，使得Python与库结合适合于科学计算，数据分析，数据可视化，文本处理，机器学习，网络爬虫，网络应用开发，Game，VR，计算机视觉等领域。本门课使用以下Numpy，Pandas库，实现矩阵运算，科学计算功能。`

> 数据分析

```
Numpy: 表达N维数组的最基础库 
Pandas: Python数据分析高层次应用库
SciPy: 数学、科学和工程计算功能库
```

> 数据可视化

```
Matplotlib: 高质量的二维数据可视化功能库
Seaborn: 统计类数据可视化功能库
Mayavi:三维科学数据可视化功能库
```

> 文本处理

```
PyPDF2: 用来处理pdf文件的工具集 
NLTK: 自然语言文本处理第三方库 
Python-docx: 创建或更新Microsoft Word文件的第三方库
```

> 机器学习

```
Scikit-learn: 机器学习方法工具集
MXNet: 基于神经网络的深度学习计算框架
TensorFlow: 谷歌开发的机器学习计算框架
```

从数据处理到人工智能：数据表示->数据清洗->数据统计->数据可视化->数据挖掘->人工智能

- 数据表示：采用合适方式用程序表达数据
- 数据清洗：数据归一化、数据转换、异常值处理
- 数据统计：数据的该要理解，数据、分布、中位数等
- 数据可视化：直观展示数据内涵的方式
- 数据挖掘：从数据分析获得知识，产生数据外的价值
- 人工智能：数据/语言/图像/视觉/等方面深度分析与决策

> **Python在金融中的应用**
>
> 在过去的十年里，随着自动化技术的出现，科技最终成为杰出的金融机构，银行，保险和投资公司，股票交易公司，对冲基金，券商等公司的一部分。根据2013年的Crosman 报告，与2013年相比，银行和金融公司2014年在科技上的花费要高出4.2%。预计在2020年，一年的金融服务的技术成本将达到5亿美元。正值系统需要维护和不断升级的时候，一些著名的银行雇佣一些开发者是很正常的事情。那么Python用在哪里呢？
>
> Python的语法很容易实现那些金融算法和数学计算，每个数学语句都能转变成一行Python代码，每行允许超过十万的计算量。
>
> 没有其他语言能像Python这样适用于数学，Python精通于计算，以及数学和科学中的排列组合问题。Python的第二个特性是表示数字，序列和算法。比如[SciPy](http://www.scipy.org/)库，很适合用来做技术领域和科学领域的计算，SicPy库被很多工程师，科学家和分析人员使用。[NumPy](http://www.numpy.org/)，也是Python的一个扩展，它可以很好地处理数学函数，数组和矩阵。同时，Python也支持严格的编码模式，因此，使它成为一个平衡的选择，或者说方法。
>
> 使用更少的人达到相同的结果以及实现其他编程语言不能实现的事，是Python首要的优点。Python语法的精确和简洁，以及它大量宝贵的第三方工具使它成为处理金融行业的错综复杂的事务的唯一可靠的选择。
>
> **Python用于分析学**
>
> 近年来分析学在数据、网络、金融等领域获得了突出的地位。应用各种软件组合起来进行数据收集，数据管理，以及数据分析，得出的结论用作商业决策，业务需求分析等等。分析学用于研究一个产品的市场效应，银行的贷款决定，这些都只是分析学的冰山一角。它在大数据，安全，数字和软件分析等领域有很深远的影响，下面是Python在分析学中的主要作用的一个延续：
>
> 在这个信息过载的世界，只有那些可以利用解析数据的优势来得出见解的人会获益。Python对于大数据的解释和分析具有很重要的作用。分析公司开发的很多工具都是基于Python来约束大数据块。分析师们会发现Python并不难学，它是一个强有力的数据管理和业务支持的媒介。
>
> 使用单一的语言来处理数据有它的好处。如果你以前曾经使用过C++或者Java，那么对你来说，Python应该很简单。数据分析可以使用Python实现，有足够的Python库来支持数据分析。 [Pandas](http://pandas.pydata.org/)是一个很好的数据分析工具，因为它的工具和结构很容易被用户掌握。对于大数据来说它无疑是一个最合适的选择。即使是在数据科学领域，Python也因为它的“开发人员友好性”而使其他语言相形见绌。一个数据科学家熟悉Python的可能性要比熟悉其他语言的可能性高得多。
>
> 除了Python在数据分析中那些很明显的优点（易学，大量的在线社区等等）之外，在数据科学中的广泛使用，以及我们今天看到的大多数基于网络的分析，是Python在数据分析领域得以广泛传播的主要原因。
>
> **Python在人工智能领域的应用**
>
> Python和其它好的技术一样，在你的开发团队像病毒一样快速传播，然后找到把它应用到各种应用和工具中的方式。换句话说，Python在开始时像一个黑客，而代码任务像钉子一样。——Mustafa Thamer，Firaxis 游戏
>
> 而人工智能是当今的“东西”，Python在这个领域也取得了显著的成绩，在商业智能领域，Python也证明了它的实用性。回到AI这个话题，Python已经成为一些AI算法的一部分，从简单的双人游戏到复杂的数据工程任务。Python的AI库在当今的软件中扮演重要的角色，包括NLYK，PyBrain，OpenCV，和AIMA。对于一些AI软件功能，短短的一个代码块就足够了。从人脸识别技术，会话接口再到其他领域，Python正在不断地覆盖新领域。
>
> 当谈到AI时，Python是一种现代化的选择。为什么呢，除了一般的原因，Python使原型设计变得更加快捷，同时具有更加稳定的架构。举个例子，比如Scikit-learn（一个机器学习库）。
>
> 在Python中调试是一个很快的过程。它还提供了对其他语言的应用程序设计接口（API）。Python的大量的库很有帮助，但是你必须精通Python，才能很好地利用它。
>
> Python将用于BI，它在网络情报中也是一种力量。自动化的司法调查，安全检查，网页分析都可能使用Python来实现。对于BI来说，有一大堆Python能够使用的工具来使你的工作更加简单，该语言对算法，数学方程有一个自然的倾向，使它成为一个多用途的媒介。
>
> **Python在数学中的应用**
>
> Python和Matlab对比：Python也在威胁着数值计算的专家级语言Matlab,很多在使用Matlab的人都在考虑转去使用Python。Matlab的使用成本太高了，它要检查代码的可移植性，你不能在另一台电脑上运行你的代码。它使用专有的算法，这意味你所使用的大多数算法你是没有办法查看的，而只能相信它们已经正确的实现了。
>
> 同时，Matlab是科学界的支持，是很多大学的一部分，尽管因为费用原因，有一部分你可能支付不起。而Python需要一个综合开发环境（IDE）和额外的程序包。
>
> Python作为开源程序，专门为了简单方便并且系统的使用。因为有第三方库和数据类型，使得使用Python整理数据变成一件很容易的事。因为不是专有的，有了它的类和可以自定义的函数，在程序的任何地方，你都可以根据你的需求很容易的移植Python代码。用户图形界面（GUI）工具包（比如Qt），对于创建一个令人印象深刻的前端很有帮助。最后，Python提供了全方位的编程包。



2.本门课程的收获和改进建议

收获：`Python课程重基础，重应用，在Python语法特性后增加练习熟悉语法。在Python练习中引入Numpy与Pandas库进行数据分析处理，能够深入理解Python这么语言的魅力：从数据到人工智能，作为一门解释型语言，Python在AI领域称霸当之无愧。`

建议：`由于大多数同学有编程语言基础，老师可以简略介绍Python语法，可以重点比较Python与类C语言的差别（Java，C&C++），然后同学们可以自然通过做小Demo来熟悉Python这门语言并使用Python开发项目。`







<center>参考</center>

[感知器算法及实现](https://blog.csdn.net/mutex86/article/details/9159111)

[Python在金融，数据分析，和人工智能中的应用](https://python.freelycode.com/contribution/detail/351)