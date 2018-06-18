---
layout: post
title:  "Keras"
date:   2018-06-13 00:00:00 -0700
categories: Deep Learning AI
---

In this post, we explore some [#DeepLearning](https://twitter.com/search?q=%23DeepLearning) with the [Keras](https://keras.io/high-level) library for Python.

* [Prerequisites and setup are covered in this gist](https://gist.github.com/witt3rd/567d1e52f1e2da4f45ed72e436dc4576)
* [Sample code is maintained in this repo](https://github.com/witt3rd/keras)

# TensorFlow Backend and GPUs

The primary benefit of Keras is that it abstracts the various backends, such as TensorFlow, Theano, and CNTK.  It provides a "user friendly" API for dealing with deep neural network architectures commonly found in today's CNNs and RNNs.

We will focus on using TensorFlow as the preferred backend, taking advantage of the local machine's GPUs, if any.  To test this setup, from within your configured environment (see above):

```bash
source activate keras
cd keras/gpu-test
python main.py
```

This code runs a simply Python script that prints its device configuration:

```python
from tensorflow.python.client import device_lib

print(device_lib.list_local_devices())
```

On my MacBook Pro, I get:

```bash
name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
```

While on my Linux desktop with two GeForce GTX 1080 Ti GPUs , I get:

```
name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456

name: "/device:GPU:0"
device_type: "GPU"
memory_limit: 10921259828
physical_device_desc: "device: 0, name: GeForce GTX 1080 Ti, pci bus id: 0000:17:00.0, compute capability: 6.1"

name: "/device:GPU:1"
device_type: "GPU"
memory_limit: 10672223028
physical_device_desc: "device: 1, name: GeForce GTX 1080 Ti, pci bus id: 0000:65:00.0, compute capability: 6.1"

```

# XOR
A classic non-linear problem that motivates the multi-layer perceptron is the simple logical exclusive-OR (XOR), which is true when the one of the two inputs is true (but not both):
![from the paper]({{ site.url }}/assets/xor-nonlinear.gif)

[http://www.ece.utep.edu/research/webfuzzy/docs/kk-thesis/kk-thesis-html/node19.html](http://www.ece.utep.edu/research/webfuzzy/docs/kk-thesis/kk-thesis-html/node19.html)

## With NumPy

There is a nice implementation of this using nothing more than `numpy` described in [A Neural Network in 11 lines of Python (Part 1)](https://iamtrask.github.io/2015/07/12/basic-python-network/), which reduces to:

```python
X = np.array([ [0,0,1],[0,1,1],[1,0,1],[1,1,1] ])
y = np.array([[0,1,1,0]]).T
syn0 = 2*np.random.random((3,4)) - 1
syn1 = 2*np.random.random((4,1)) - 1
for j in xrange(60000):
    l1 = 1/(1+np.exp(-(np.dot(X,syn0))))
    l2 = 1/(1+np.exp(-(np.dot(l1,syn1))))
    l2_delta = (y - l2)*(l2*(1-l2))
    l1_delta = l2_delta.dot(syn1.T) * (l1 * (1-l1))
    syn1 += l1.T.dot(l2_delta)
    syn0 += X.T.dot(l1_delta)
```
(We have used a slightly different implementation in our sample.)

To run:
```bash
source activate keras
cd keras/xor-numpy
python main.py
```

## With Keras
This simple model can be translated into Keras following [](https://blog.thoughtram.io/machine-learning/2016/11/02/understanding-XOR-with-keras-and-tensorlow.html):
```python
import numpy as np
from keras.models import Sequential
from keras.layers.core import Dense

# the four different states of the XOR gate
training_data = np.array([[0, 0], [0, 1], [1, 0], [1, 1]], "float32")

# the four expected results in the same order
target_data = np.array([[0], [1], [1], [0]], "float32")

model = Sequential()
model.add(Dense(16, input_dim=2, activation='relu'))
model.add(Dense(1, activation='sigmoid'))

model.compile(loss='mean_squared_error',
              optimizer='adam',
              metrics=['binary_accuracy'])

model.fit(training_data, target_data, epochs=500, verbose=2)

print("prediction: {}".format(model.predict(training_data).round()))
```

To run this example:
```bash
source activate keras
cd keras/xor-keras
python main.py
```

# MNIST
