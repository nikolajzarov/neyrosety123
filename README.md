import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'


import numpy as np
import matplotlib.pyplot as plt
from tensorflow import keras
from tensorflow.keras.datasets import mnist
from tensorflow.keras.layers import Dense, Flatten


(x_train, y_train), (x_test, y_test) = mnist.load_data()


x_train = x_train / 255
x_test = x_test / 255


y_train_cat = keras.utils.to_categorical(y_train, 10)

y_test_cat = keras.utils.to_categorical(y_test, 10)


size_val = 10000
x_val_split = x_train[:size_val]
y_val_split = y_train_cat[:size_val]


x_train_split = x_train[size_val:]
y_train_split = y_train_cat[size_val:]


model = keras.Sequential([
    Flatten(input_shape=(28, 28, 1)),
    Dense(128, activation='relu'),
    Dense(10, activation='softmax')
])
myAdam = keras.optimizers.Adam(learning_rate=0.1)
myOpt = keras.optimizers.SGD(learning_rate=0.1, momentum=0.0, nesterov=True)


model.compile(optimizer=myOpt,
              loss='categorical_crossentropy',
              metrics=['accuracy'])

from sklearn.model_selection import train_test_split
x_train_split, x_val_split, y_train_split, y_val_split = train_test_split(x_train, y_train_cat,
                                                                          test_size=0.2)

model.fit(x_train_split, y_train_split, batch_size=32, epochs=5,
          validation_data=(x_val_split, y_val_split))

print('Python %s on %s' % (sys.version, sys.platform))

print("Проверка:")
model.evaluate(x_test, y_test_cat)
