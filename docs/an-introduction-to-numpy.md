# NumPy 简介

> 原文：<https://dev.to/gautamkrishnar/an-introduction-to-numpy>

[![Numpy](img/67c9e39b63b8f18cd4c7763d3779152c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cjZ3iPpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mKy85Kr.png)

数值 python 或简称 NumPy 是在 python 中执行科学计算的最佳模块之一。它广泛用于数据科学以及使用 python 的图像处理。当我开始使用 python 学习机器学习和数据科学时，我最近学会了如何在我的项目中有效地使用这个模块。我对 NumPy 的特性感到惊讶，我发现使用 NumPy 非常有趣。使用几个小时后，我就爱上了它。

## 什么是 NumPy

[Numpy 的 GitHub readme](https://github.com/numpy/numpy/blob/master/README.md) 将其定义为:
NumPy 是用 Python 进行科学计算所需的基础包。该包装包含:

*   一个强大的 N 维数组对象
*   复杂的(广播)功能
*   集成 C/C++和 Fortran 代码的工具
*   有用的线性代数、傅立叶变换和随机数功能。

它源自旧的数字代码库，可以用来替代数字代码。它还增加了 numarray 引入的功能，可以用来替代 numarray。

简单地说，NumPy 是一个开源的 python 库，它允许我们用 python 进行科学计算。它有超能力神奇地支持令人生畏的向量和矩阵计算。NumPy 包的核心是 ndarray 对象。这封装了同质数据类型的 n 维数组，并针对性能进行了许多优化。创建数组与 Python 内置的列表数据结构形成对比。

## 为什么是 NumPy

正如我前面所说，NumPy 的创建是为了克服 python 的列表数据结构的限制。我们可以使用 python 列表代替 NumPy 来执行各种计算，如矩阵乘法、矢量积等。使用 NumPy 代替列表不仅可以提高代码的性能，还可以减少代码的行数。在这篇博客中，我将比较列表数据类型和 numpy。

#### 安装 NumPy

您可以使用 pip 来安装 NumPy。如果没有安装 python 和 pip，可以从这里下载。安装后，使用下面的命令安装 NumPy:

```
pip install numpy 
```

Enter fullscreen mode Exit fullscreen mode

现在 NumPy 将安装在您的机器上。

#### 数量 v/s 列表

Python 有一个强大的内置数据类型，称为列表。它拥有强大的功能，几乎可以用于任何高级科学应用程序，但与 NumPy 的数组数据类型相比，它仍然有局限性。让我们比较一下 python 列表和 NumPy 数组。在这个例子中，我将使用 python shell。只需输入 python 命令来启动 shell。

您可以使用下面的代码创建一个 NumPy 数组:

```
>>> import numpy as np
>>> a = np.array([1,2,3,4])
>>> a
array([1, 2, 3, 4])

You can create a new python list containing same elements using the code below:

>> b = [1, 2, 3, 4]
>>> b
[1, 2, 3, 4]

lets print both of them to the console:

>>> for element in a: # Numpy Array
 ...     print(element)
 1
 2
 3
 4
 >>> for element in b: # Python List
 ...     print(element)
 1
 2
 3
 4 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，NumPy 数组的工作方式与 list 完全相同。我们可以简单地使用一个循环来打印它的元素。尽管两者看起来一样，但是 python 列表和 NumPy 数组还是有一些不同。我们可以简单地使用下面的代码向列表中添加一个新元素:

```
>>> b.append(5)
>>> b
[1, 2, 3, 4, 5]
>>> b += [6,7]
>>> b
[1, 2, 3, 4, 5, 6, 7] 
```

Enter fullscreen mode Exit fullscreen mode

我们不能在 NumPy 数组中做同样的事情，它会抛出一个错误:

```
>>> a.append(2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'numpy.ndarray' object has no attribute 'append'
>>> a += [5,6]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operands could not be broadcast together with shapes (4,) (2,) (4,) 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这两种方法在 NumPy 数组的情况下都不适用。列表使用加号运算符进行连接，但是 NumPy 数组使用加号运算符的方式不同。所以让我们来看看。让我们使用 NumPy 和 python 列表:
找到一个数组的元素总和

```
>>> b2 = [] # Temporary List >>> for element in b: # Using List ...     b2.append(element + element)
>>> b2
[2, 4, 6, 8, 10, 12, 14]

>> a + a # Using NumPy array array([2, 4, 6, 8]) 
```

Enter fullscreen mode Exit fullscreen mode

很简单对吧！😀

#### NumPy 在行动

NumPy 数组将加号运算符(+)视为元素加法运算符。我们还可以用它来添加两个不同的数组，甚至可以用它来执行一个数组的标量加法。NumPy 数组将乘法运算符(*)视为矩阵乘法运算符。在 NumPy 数组中，大多数操作符都是按元素来操作的。让我们看看 NumPy 数组的超能力😀:

```
>>> a # NumPy Array array([1, 2, 3, 4])
>>> a2 = np.array([4,5,6,7]) #New NumPy array 
>>> a + a2 # Matrix addition array([ 5,  7,  9, 11])
>>> a + 3 # Addition with a scalar array([4, 5, 6, 7])

>>> a * a2 # element wise multiplication array([ 4, 10, 18, 28])

>>> a * 3 # Multiplication with a scalar array([ 3,  6,  9, 12])
>>> a ** 3 # Power operator array([ 1,  8, 27, 64], dtype=int32)
>>> a.sum() # Sum of elements in a 45 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，矩阵运算在 NumPy 数组中轻而易举。我们不再需要使用烦人的循环来执行这些操作😉。如果你有一个 N 维矩阵，你可以使用 NumPy 来执行所有这些操作。NumPy 为您提供了一个抽象层，内置了所有可以有效执行这些操作的东西。对于 2D 矩阵，让我们检查这些操作:

```
>>> a = np.array([[1,2,3],[4,5,6],[7,8,9]])
>>> a
array([[1, 2, 3],
       [4, 5, 6],
       [7, 8, 9]])
>>> a2 = np.array([[10,11,12],[13,14,15],[16,17,18]])
>>> a2
array([[10, 11, 12],
       [13, 14, 15],
       [16, 17, 18]])

>>> a[0] # access each row array([1, 2, 3])

>>> a[0][1] # access element i=0 j=1 2
>>> a[0,1] # access element i=0 j=1 2

>>> a * 2 # Scalar Multiplication array([[ 2,  4,  6],
       [ 8, 10, 12],
       [14, 16, 18]])

>>> a + a2 # Addition array([[11, 13, 15],
       [17, 19, 21],
       [23, 25, 27]])

>>> a * a2 # Multiplication array([[ 10,  22,  36],
       [ 52,  70,  90],
       [112, 136, 162]])

>>> a - a2 # Subraction array([[-9, -9, -9],
       [-9, -9, -9],
       [-9, -9, -9]])

>>> inva = np.linalg.inv(a) # a inverse >>> inva
array([[ -4.50359963e+15,   9.00719925e+15,  -4.50359963e+15],
       [  9.00719925e+15,  -1.80143985e+16,   9.00719925e+15],
       [ -4.50359963e+15,   9.00719925e+15,  -4.50359963e+15]])

>>> np.linalg.det(a) # Determinant of a 6.6613381477509402e-16

>>> np.diag(a) # Diagonals of a array([1, 5, 9])

>>> np.trace(a) # Sum of diagonals 15

>>> x = np.linalg.eig(a) # Eigen values and eigen vectors of a >>> x
(array([  1.61168440e+01,  -1.11684397e+00,  -1.30367773e-15]), array([[-0.23197069, -0.78583024,  0.40824829],
       [-0.52532209, -0.08675134, -0.81649658],
       [-0.8186735 ,  0.61232756,  0.40824829]]))
>>> x[0] # eigen value array([  1.61168440e+01,  -1.11684397e+00,  -1.30367773e-15])
>>> x[1] # eigen vectors array([[-0.23197069, -0.78583024,  0.40824829],
       [-0.52532209, -0.08675134, -0.81649658],
       [-0.8186735 ,  0.61232756,  0.40824829]])

>>> a ** 2 # Power array([[ 1,  4,  9],
       [16, 25, 36],
       [49, 64, 81]])

>>> a2.T # Transpose of a2 array([[10, 13, 16],
       [11, 14, 17],
       [12, 15, 18]])

>>> a.mean() # mean 5.0

>>> a.var() # variance 6.666666666666667

>>> a = np.matrix([[1,2,3],[4,5,6],[7,8,9]])
>>> a # NumPy Matrix type matrix([[1, 2, 3],
        [4, 5, 6],
        [7, 8, 9]]) 
```

Enter fullscreen mode Exit fullscreen mode

除了 NumPy 数组之外，NumPy 还有一个矩阵类型。官方文档推荐使用矩阵类型进行矩阵运算。NumPy 对于多维矩阵也有很好的表现。除了这些标准操作之外，NumPy 还有其他几个函数可以让你的程序更加简单。让我们来看一些例子:

```
>>> a = np.array([1,2,3])
>>> a
array([1, 2, 3])
>>> np.sqrt(a)
array([ 1.        ,  1.41421356,  1.73205081])
>>> np.sin(a)
array([ 0.84147098,  0.90929743,  0.14112001])
>>> np.cos(a)
array([ 0.54030231, -0.41614684, -0.9899925 ])
>>> np.tan(a)
array([ 1.55740772, -2.18503986, -0.14254654])
>>> np.log(a)
array([ 0.        ,  0.69314718,  1.09861229])
>>> np.exp(a)
array([  2.71828183,   7.3890561 ,  20.08553692]) 
```

Enter fullscreen mode Exit fullscreen mode

所以 NumPy 简单地把数组当作一个向量或者一个数学对象。要对列表进行操作，需要使用 for 循环。由于 for 循环速度较慢，因此与 NumPy 数组相比，它可能需要更多时间来执行各种操作。让我们对向量和矩阵做一些运算:

```
>>> a
array([[1, 2, 3],
       [4, 5, 6],
       [7, 8, 9]])
>>> a2
array([[10, 11, 12],
       [13, 14, 15],
       [16, 17, 18]])
>>> a.dot(a2) # Dot product a.a2 array([[ 84,  90,  96],
       [201, 216, 231],
       [318, 342, 366]])
>>> a2.dot(a)# Dot product a2.a array([[138, 171, 204],
       [174, 216, 258],
       [210, 261, 312]])
>>> np.dot(a,a2) # Dot product a.a2 array([[ 84,  90,  96],
       [201, 216, 231],
       [318, 342, 366]])
>>> a = np.array([2,3])
>>> a2 = np.array([4,5])
>>> maga = np.linalg.norm(a) # magnitude of a >>> maga2 = np.linalg.norm(a2) @ magnitude of a2
>>> angle = np.arccos(a.dot(a2) / (maga * maga2)) # angle between a and a2 >>> angle # in radian 1.1352271440633694 
```

Enter fullscreen mode Exit fullscreen mode

要生成用于测试的随机矩阵，只需使用:

```
>>> z = np.zeros(10) # Generates zero array >>> z 
array([ 0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.,  0.])

>>> np.zeros((5,5)) # 5*5 Zero matrix array([[ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.,  0.]])

>>> np.ones((5,5)) # 5*5 Unit matrix array([[ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.,  1.]])

>>> np.random.random((5,5)) # random 5*5 array with elements < 0 array([[ 0.95798455,  0.24020745,  0.62194033,  0.93840616,  0.40785382],
       [ 0.01294948,  0.7228686 ,  0.67448551,  0.20403856,  0.046528  ],
       [ 0.63331545,  0.89097084,  0.01754348,  0.17084474,  0.32112247],
       [ 0.97881143,  0.83247286,  0.65629919,  0.21386575,  0.72251318],
       [ 0.20167738,  0.24018638,  0.85572554,  0.7706282 ,  0.80284553]])

>>> np.random.randn(5,5) # Random 5*5 matrix array([[ 0.16603893,  1.14554164,  0.40170708,  0.52864275,  1.50740231],
       [ 0.13218522, -0.20418907, -1.09940842, -1.25180194,  0.6859655 ],
       [ 0.09053258,  1.11002797,  0.1455936 , -0.33915414,  0.25604553],
       [ 0.96807902, -0.03155716, -0.79001785,  0.4567955 , -1.93929055],
       [-1.38540075, -1.82320053,  0.02358358, -1.13975953, -1.23515682]]) 
```

Enter fullscreen mode Exit fullscreen mode

综上所述，尽管与 lists 数据类型相比，NumPy 数组包含几个高级函数，但它不能被认为是 python 列表的替代品。如果你想在一个数组上做一些数学运算，NumPy 是非常有用的，如果我们使用一个链表而不是 NumPy 数组，我们需要使用一个循环遍历每一个元素，这将大大降低程序的整体性能。使用 NumPy 不仅可以提高程序的性能，还可以为代码添加高级功能。

访问官方文档了解更多:[https://docs.scipy.org/doc/numpy/](https://docs.scipy.org/doc/numpy/)

欢迎对本文提出任何建议。如果你发现任何错误，请不要忘记评论这篇文章😀

最初发表于:[gautamkrishnar.com](http://www.gautamkrishnar.com/)