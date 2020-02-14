---
layout: post
title: 线性回归
categories:
- Deep Learning
tags:
- DL
math: true
---

上次在[深度学习起步](http://geeklu.com/2016/07/deep-learning-intro/)提到了监督学习和非监督学习，这里要讲的回归就属于监督学习的范畴。Test  

这里先介绍下"回归"（Regression）这个词使用的来源，Regression作为术语被首次使用在高尔顿(Francis Galton,1822～1911)在他的1886年发表的一篇研究人类遗传问题的名为“Regression towards mediocrity in hereditary stature”的论文中，其中相关的上下文就是人类身高的分布相对稳定而不产生两极分化，总是回归于平均水平。 后来该术语被其他人采纳，以至于它今天作为一个一般的统计方法存在，详细资料可以看这篇[https://en.wikipedia.org/wiki/Regression_toward_the_mean](https://en.wikipedia.org/wiki/Regression_toward_the_mean)。

## 1. 概率知识回顾   
学习生涯中最早接触**回归**这个概念是高中数学里统计学部分的**线性回归方程**吧，不过估计都忘得差不多了。   
回归问题属于统计学，现应用于数据分析，机器学习等多个领域，虽然各个领域对回归的细节表述不完全一致，但是其底层的数学意义是完全一致的。
  这里就简单介绍说下一元线性模型，假设给定一个样本数据的集合：

$$
  (x^{(1)}, y^{(1)}),\,(x^{(2)}, y^{(2)})\,...\,(x^{(n)},y^{(n)})
$$

>这里的(n)不是指数，而是指第n个样本数据 ,为什么没有使用$x_n,y_n$呢，因为当非一元的线性的时候，比如二元的时候第n个样本数据就得这样表示了($x_1^{(n)},x_2^{(n)},y^{(n)}$)来表示，也就是这种情况会出现下标被占用的情况。

这里x为自变量(independent variable)，y为因变量(dependent variable)，且假设x，y之间存在线性关系，那么我们可以建立一个模型:

$$
y^{(i)} = \theta_0 + \theta_1x^{(i)} + u^{(i)}
$$

其中 $\theta_0$和 $\theta_1$是两个常数，$u^{(i)}$ 是第i个样本数据的误差项，误差项满足下列基本假定：数学期望为0，且是独立的，服从均值为0,方差为常数 $\delta^2$的正态分布。   
上面这种模型函数，很多地方叫做总体回归函数 ,总体回归函数一般是未知的，且只有一条，而我们一般研究的是另一个叫做样本回归函数的模型：

$$
\hat{y}^{(i)} = \hat{\theta}_0 + \hat{\theta}_1x^{(i)}
$$
其中实际样本中的 $y^{(i)}$ 值和 $\hat{y}^{(i)}$ 的差 $e^{i} =y^{(i)}-\hat{y}^{(i)}$，我们称之为残差。虽然样本回归函数的模型中 $\hat{\theta}_0 ,\, \hat{\theta}_1$ 随着样本的变化而变化，通过样本数据可以估算出相应的值的，在机器学习的训练中，随着训练的不断进行，也就是样本数据的不断增加，模型的参数也是在不断的变化的。为了方便和通用我就将回归函数写成下面的形式：
$$
y = \theta_0 + \theta_1x
$$

## 2. 案例分析
下面我们来通过实际的例子而且从机器学习的角度来加深理解一元线性回归模型。
假设下面是某公司某十个月的广告费用和对应的销售额：

<table  border="1">
<tbody>
<tr><td><em>广告费（万）</em></td><td><em>销售额（万）</em></td></tr>
<tr><td>4</td><td>9</td></tr>
<tr><td>8</td><td>20</td></tr>
<tr><td>9</td><td>22</td></tr>
<tr><td>8</td><td>15</td></tr>
<tr><td>7</td><td>17</td></tr>
<tr><td>12</td><td>23</td></tr>
<tr><td>6</td><td>18</td></tr>
<tr><td>10</td><td>25</td></tr>
<tr><td>6</td><td>10</td></tr>
<tr><td>9</td><td>20</td></tr>
</tbody>    
</table>

这组数据你可以把它看成训练数据，对于输入广告费都对应一个销售额。我们设广告费为自变量x,销售额为因变量y，那么我们的目标就是根据训练数据集(样本)找出最佳的 $\theta_0$, $\theta_1$(就是找出确定的样本回归函数)，然后就可以通过这个回归函数对训练数据外的输入做出结果的评估。
我们设 $y=h_{(\theta_0,\theta_1)}(x) = \theta_0 + \theta_1x$, 为了方便可以写作$h_{(\theta)}(x) = \theta_0 + \theta_1x$ 。  

>人们历来喜欢通过函数的几何意义来加深理解，因为将函数在坐标系中进行表达，更加直观。比如一元函数y=f(x)是在二维坐标系中的线,二元函数z=f(x,y)
是三维坐标系中的曲面，多于二元的函数的几何意义就不太好表达了。

一元线性回归函数在二维坐标系统其实就是一条直线，我们现将上述的那些训练数据在二维坐标系中表示出来。   
<img src="http://wx4.sinaimg.cn/mw1024/65cc0af7gw1f6r04s9c76j20q90hi75x.jpg" width="80%" height="80%" />​      

图上散落的那些交叉点就是上表中的数据，然后我们的目标就是根据这些点拟合出一条直线，然后再通过这条直线，对新的广告费用的输入，给出对应的收入的评估。   
如果通过机器学习的概念来描述这个过程大概是下面的样子：   
<img src="http://wx4.sinaimg.cn/mw1024/65cc0af7gw1f6t9n6icr9j20b4081zkn.jpg" width="50%" height="50%" />    
图中的h是hypothesis的意思，这是机器学习的专业属于，你可以把它看做一个函数的名字。    
所以我们这里的目标就是如果来表示h，我这里就假设h就是一个一元线性的函数，可以表示为：  
$$
h_{(\theta)}(x) = \theta_0 + \theta_1x
$$
所以要想确定h函数，那就是要找出最优的 $\theta_0$和 $\theta_1$的值，使得得到的直线能够最好地拟合训练数据中的所有的点。假设训练数据中有一个点 $(x^{(i)}, y^{(i)})$ ，那么 $h_{(\theta)}(x^{(i)})$ 和 $y^{(i)}$ 越接近越好。然而问题是训练数据中的点有多个，所以我们需要一个评估模型，来对 $\theta_0$和 $\theta_1$的取值进行评估。   

$h_{(\theta)}(x^{(i)}) - y^{(i)}$ 可以叫做，第i个样本数据的误差，误差有可能为正也有可能为负数，所以对其做下平方，$(h_{(\theta)}(x^{(i)}) - y^{(i)})^2$ ,这是其中的一个点，然后我们需要综合考虑所有的点，就得到误差的平法和(若你想刨根问底，为啥使用误差平方和，而不是绝对值的和，可以参考这篇文章 [http://blog.sciencenet.cn/blog-430956-621997.html](http://blog.sciencenet.cn/blog-430956-621997.html))
$$
\sum_{i=0}^m (h_{(\theta)}(x^{(i)}) - y^{(i)})^2
$$

上面公式中的m为样本容量。也就是说当误差平方和最小的时候 $\theta_0$和 $\theta_1$是最优的。为了数学上计算方便，将此过程描述成如下：
$$
\frac{ 1 }{ 2*m }\sum_{i=0}^m (h_{(\theta)}(x^{(i)}) - y^{(i)})^2
$$
带入h函数,将问题转变成求 $\theta_0$和 $\theta_1$的函数
$$
J(\theta_0, \theta_1) = \frac{ 1 }{ 2*m }\sum_{i=0}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)})^2
$$
$J(\theta_0, \theta_1)$可以称之为误差代价函数，可以简称为代价函数。 最终问题就是求当$J(\theta_0, \theta_1)$ 取得最小值时，$\theta_0$和 $\theta_1$的值，变成了一个极值求解的问题。

$$
\underset{\theta_0,\theta_1}{minimize} \ J(\theta_0, \theta_1)
$$

> 也许你可以找出其他的误差代价函数，但是对于线性回归问题，平方和误差代价函数是最好的选择。

## 3. 几何意义
为了简化问题，我们先假设 $\theta_0$等于0，也就是直线经过原点，问题就简化成   
$$
J(\theta_1) = \ \frac{ 1 }{ 2*m }\sum_{i=0}^m (\theta_1x^{(i)} - y^{(i)})^2
$$
我们假设样本数据为{(1,1),(2,2),(3,3)},将样本数据带入上述函数
$$
J(\theta_1) = \ \frac{ 1 }{ 2*3 }[{ (\theta_1 - 1)^2}+{ (2*\theta_1 - 2)^2}+{ (3*\theta_1 - 3)^2}]
$$
经过计算得到
$$
J(\theta_1) = \ \frac{ 7 }{ 3 }{ (\theta_1 - 1)^2}
$$

>上面的计算还算简单，直接心算便可，当遇到复杂的多项式的计算的时候，可以借助相关的软件工具来帮忙加快计算，比如可以通过MuPAD(Matlab的一个应用)的simplify

为了便于理解画出对应的函数图

<img src="http://wx4.sinaimg.cn/mw1024/65cc0af7gw1f6tdlw2ndbj20q90hiwfi.jpg" width="80%" height="80%" />   

可以看到当 $\theta_1$取1时，J取得最小时，所以我要所求的回归函数是     
$$
h(x) = x
$$

我们再来回到之前的问题的求解，将广告费和销售额一个个的代入进行计算：     
$$
J(\theta_0, \theta_1) = \frac{ 1 }{ 2*m }\sum_{i=1}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)})^2   \\
J(\theta_0, \theta_1) = \frac{ 1 }{ 2*10 }*[ (\theta_0 + \theta_1*4 - 9)^2 + \\ (\theta_0 + \theta_1*8 - 20)^2+\\ (\theta_0 + \theta_1*9 - 22)^2+\\ (\theta_0 + \theta_1*8 - 15)^2+\\ (\theta_0 + \theta_1*7 - 17)^2+\\ (\theta_0 + \theta_1*12 - 23)^2+\\ (\theta_0 + \theta_1*6 - 18)^2+\\ (\theta_0 + \theta_1*10 - 25)^2+\\ (\theta_0 + \theta_1*6 - 10)^2+\\ (\theta_0$$
J(\theta_1) = \ \frac{ 7 }{ 3 }{ (\theta_1 - 1)^2}
$$ + \theta_1*9 - 20)^2] \\
J(\theta_0, \theta_1) =\frac{ 1 }{ 20 } (10*\theta_0^2+158*\theta_0*\theta_1-358*\theta_0+671*\theta_1^2-3014*\theta_1+3457) \\
$$    
图形化后是一个曲面如下图，所以最优的几何意义就是去面上在 $J(\theta_0, \theta_1)$ 轴上最小的点对应的 $\theta_0$ 和 $\theta_1$的值。  
<img src="http://wx4.sinaimg.cn/mw1024/65cc0af7gw1f6tprb61c1j20l70hp0wn.jpg" width="80%" height="80%" />   

## 4. 最小二乘法
在实际的计算中，比较常用的有最小二乘法和梯度下降法，这节先讲最小二乘法，这个方法其实在高中的数学课本中以公式的形式存在的,假设样本数据容量为n，线性方程为 `y = a + bx`，那么对应的a，b分别为    

$$  
\begin{cases}
b =\frac{\sum_{i=1}^nx_iy_i-n\bar{x}\bar{y}}{\sum_{i=1}^nx_i^2-n\bar{x}^2}\\
a=\bar{y}-\hat{b}\bar{x}
\end{cases}
$$

对于 $h_{(\theta)}(x) = \theta_0 + \theta_1x$ 公式则为：

$$
\begin{cases}
\theta_1 =\frac{\sum_{i=1}^nx^{(i)}y^{(i)}-\frac{1}{n}\sum_{i=1}^nx^{(i)}\sum_{i=1}^ny^{(i)}}{\sum_{i=1}^nx_i^2-\frac{1}{n}(\sum_{i=1}^nx^{(i)})^2}\\
\theta_0=\frac{1}{n}(\sum_{i=1}^nh(x^{(i)})-\theta_1\sum_{i=1}^nx^{(i)})
\end{cases}   
$$

下面我们来对公式进行推导，一般求函数极致有一个方法就是导数法，我们在求代价函数的极小值的时候，算出代价函数的导函数，然后令导函数为零，算出对应的值，还是先从最简单的开始，假设代价函数为：
$$
J(\theta_1) = \ \frac{ 7 }{ 3 }{ (\theta_1 - 1)^2}
$$

可以看到代价函数是一个一元二次函数，对 $\theta_1$ 进行求导，你可先按照整式的乘积算好了再求导，也可以直接利用复合函数的求导法则进行求导，这里按照复合函数的求导方式进行求导：    
$$
J^{'}(\theta_1) = \ \frac{ 7 }{ 3 }*2*{ (\theta_1 - 1)*1}\\
\qquad\Downarrow \\
J^{'}(\theta_1) = \ \frac{ 14 }{ 3 }{ (\theta_1 - 1)}
$$    
我们令到函数等于0可以算得当 $\theta_1$ 等于1的时候，代价函数取得最小值0。   
对于上面的二元的代价函数 $J(\theta_0, \theta_1) = \frac{ 1 }{ 2*m }\sum_{i=1}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)})^2$ 则需要分别对 $\theta_0$ 和 $\theta_1$ 求偏导：

$$
\begin{cases}
 \frac{\partial}{\partial \theta_0}J(\theta_0, \theta_1) = \frac{ 1 }{ 2*m }\sum_{i=1}^m 2*(\theta_0 + \theta_1x^{(i)} - y^{(i)})*1 \\
  \frac{\partial}{\partial \theta_1}J(\theta_0, \theta_1) = \frac{ 1 }{ 2*m }\sum_{i=1}^m 2*(\theta_0 + \theta_1x^{(i)} - y^{(i)})*x^{(i)}
\end{cases}
$$

化简一下：

$$
\begin{cases}
 \frac{\partial}{\partial \theta_0}J(\theta_0, \theta_1) = \frac{ 1 }{ m }\sum_{i=1}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)}) \\
  \frac{\partial}{\partial \theta_1}J(\theta_0, \theta_1) = \frac{ 1 }{ m }\sum_{i=1}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)})*x^{(i)}
\end{cases}
$$    

我们令偏导数都等于0,然后进行一步步的计算，计算过程其实很简单，主要用到了求和公式的一些特性罢了，详细的过程如下：

$$
\begin{cases}
 \frac{ 1 }{ m }\sum_{i=1}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)})=0 \\
 \frac{ 1 }{ m }\sum_{i=1}^m (\theta_0 + \theta_1x^{(i)} - y^{(i)})*x^{(i)} =0
 \end{cases}\\
\qquad\Downarrow \\
 \begin{cases}
\sum_{i=1}^m \theta_0 + \sum_{i=1}^m\theta_1x^{(i)} - \sum_{i=1}^my^{(i)}=0 \\
\sum_{i=1}^m \theta_0*x^{(i)} + \sum_{i=1}^m\theta_1x^{(i)}*x^{(i)} - \sum_{i=1}^my^{(i)}*x^{(i)} =0
  \end{cases} \\
\qquad\Downarrow \\
  \begin{cases}
 m*\theta_0 + \theta_1*\sum_{i=1}^mx^{(i)} - \sum_{i=1}^my^{(i)}=0 \\
 \theta_0*\sum_{i=1}^m x^{(i)} + \theta_1*\sum_{i=1}^mx^{(i)}*x^{(i)} - \sum_{i=1}^my^{(i)}*x^{(i)} =0
   \end{cases}\\
\qquad\Downarrow \\
   \begin{cases}
  \theta_0 = \frac{ 1 }{ m }(\sum_{i=1}^my^{(i)}-\theta_1*\sum_{i=1}^mx^{(i)})  \\
  \theta_0*\sum_{i=1}^m x^{(i)} + \theta_1*\sum_{i=1}^mx^{(i)}*x^{(i)} - \sum_{i=1}^my^{(i)}*x^{(i)} =0
    \end{cases}\\
\qquad\Downarrow \\
       \begin{cases}
      \theta_0 = \frac{ 1 }{ m }(\sum_{i=1}^my^{(i)}-\theta_1*\sum_{i=1}^mx^{(i)})  \\
      \frac{ 1 }{ m }(\sum_{i=1}^my^{(i)}-\theta_1*\sum_{i=1}^mx^{(i)})*\sum_{i=1}^m x^{(i)} + \theta_1*\sum_{i=1}^mx^{(i)}*x^{(i)} - \sum_{i=1}^my^{(i)}*x^{(i)} =0
        \end{cases}\\
\qquad\Downarrow \\
\begin{cases}
\theta_0 = \frac{ 1 }{ m }(\sum_{i=1}^my^{(i)}-\theta_1*\sum_{i=1}^mx^{(i)})  \\
\frac{ 1 }{ m }\sum_{i=1}^my^{(i)}*\sum_{i=1}^m x^{(i)} -\frac{ 1 }{ m }*\theta_1*\sum_{i=1}^mx^{(i)}*\sum_{i=1}^m x^{(i)} \\
+ \theta_1*\sum_{i=1}^mx^{(i)}*x^{(i)} - \sum_{i=1}^my^{(i)}*x^{(i)} =0
\end{cases}\\
\qquad\Downarrow \\
\begin{cases}
\theta_0 = \frac{ 1 }{ m }(\sum_{i=1}^my^{(i)}-\theta_1*\sum_{i=1}^mx^{(i)})  \\
\frac{ 1 }{ m }\sum_{i=1}^my^{(i)}*\sum_{i=1}^m x^{(i)}  - \sum_{i=1}^my^{(i)}*x^{(i)} =\theta_1*(\frac{ 1 }{ m }*\sum_{i=1}^mx^{(i)}*\sum_{i=1}^m x^{(i)} - \sum_{i=1}^mx^{(i)}*x^{(i)})
\end{cases}\\
\qquad\Downarrow \\
\begin{cases}
\theta_0 = \frac{ 1 }{ m }(\sum_{i=1}^my^{(i)}-\theta_1*\sum_{i=1}^mx^{(i)})  \\
\theta_1 = \frac{\frac{ 1 }{ m }\sum_{i=1}^my^{(i)}*\sum_{i=1}^m x^{(i)}  - \sum_{i=1}^my^{(i)}*x^{(i)}}{\frac{ 1 }{ m }*(\sum_{i=1}^mx^{(i)})^2 - \sum_{i=1}^m(x^{(i)})^2}
\end{cases}\\
$$  

## 5. 多元线性回归的最小二乘法   

上面的研究的都是一元的线性回归，但是实际应用到，一般都是存在多个自变量的，比如公司的销售额其实不仅仅和广告投入相关，还会受别的很多因素的影响，所以这个时候就出现了多元线性回归的情况了。

## 6. 梯度下降法
