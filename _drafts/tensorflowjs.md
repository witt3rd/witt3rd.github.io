---
layout: post
title:  "TensorFlowJS"
date:   2018-06-15 00:00:00 -0700
math:   true
categories: Deep Learning AI
---

$$a^2 + b^2 = c^2$$

> experiments with the [TensorFlowJS](https://js.tensorflow.org) [#DeepLearning](https://twitter.com/search?q=%23DeepLearning) JavaScript library

## Introduction

Having now [played with Keras](https://witt3rd.github.io/deep/learning/ai/2018/06/13/keras.html) a bit in Python, which is used for "serious" deep learning work, we next look at [TensorFlowJS](https://js.tensorflow.org) JavaScript library, which can be used in Node or in the browser.

There are generally three areas of interest:

- training a model to solve for a particular task
- applying a model for its task
- _transfer learning_, where ...

While all can be performed with TensorFlowJS (and we'll look at examples of each below), training for large-scale problems is unlikely to be done in a web browser, but might be done using Node/JavaScript (since the heavy lifting is still done by the GPU or even the cloud). Consuming a model or updating a model from user feedback are certainly likely to be done using the browser.

## Prerequisites and Setup

Please refer to [this gist section](https://gist.github.com/witt3rd/567d1e52f1e2da4f45ed72e436dc4576#tensorflowjs) for latest installation details.

## Sample Code

Sample code is maintained in the [tensorflowjs repo](https://github.com/witt3rd/tensorflowjs)

## XOR (again)

When learning something new, it is often valuable to base it on something trivial and/or familiar, so that you can focus what is new. (To paraphrase a random YouTube chat room comment during [The Coding Train: Live Stream #141](https://www.youtube.com/watch?v=_wC4sn5qKQw).)

The [Keras](https://github.com/witt3rd/keras/tree/master/xor_keras) version (Python) of this problem was solved by:

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

The TensorFlowJS _layers_ API is intended to mirror the Keras API, so the translation is quite straightforward:

## Conclusion

## License

MIT &copy; 2018 [Donald Thompson](http://witt3rd.com)
