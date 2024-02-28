		Keras是一个用Python编写的高级神经网络，它能够以Tensorflow、CNTK或者Theano作为后端运行

​		Keras的核心数据结构是model，一种组织网络层的方式。最简单的模型是Sequential顺序模型，它由多个网络层线性堆叠。对于更复杂的结构，应该使用Keras函数式API，它允许构建任意的神经网络图。

# Sequential模型

```
from keras.models import Sequential

model = Sequential()
```

​		可以简单地使用 .add() 来堆叠模型：

```
from keras.layers import Dense

model.add(Dense(units=64, activation='relu', input_dim=100))
model.add(Dense(units=10, activation='softmax'))
```

​		在完成了模型地构建后，可以使用 .compile() 来配置学习过程：

```
model.compile(loss='categorical_crossentropy', optimizer='sgd', metrics=['accuracy'])
```

# Keras layers API

​		定义：状态（权重）和一些计算的组合。

​		一个layer封装了一个状态属性（layer的权重）和输入到输出的一种转换。

​		layer实例是可调用的，很像一个函数。与函数不同的是，layers有一个状态属性，当layer在训练时期接收数据时更新，并且存在layer.weights中

##        Layer激活函数

### 		relu方法

```
model.add(layers.Dense(64, activation=activations.relu))
```

等价于

```
from tensorflow.keras import layers
from tensorflow.keras import activations

model.add(layers.Dense(64))
model.add(layers.Activation(activations.relu))
```

等价于

```
model.add(layers.Dense(64, activation='relu'))
```

