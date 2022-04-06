---
title: TensorFlow2笔记-自定义callbacks
categories: 
    - 深度学习
    - TensorFlow2
tags: 
    - TensorFlow2
---



#### 通过CSVLogger保存训练数据

最近在神经网络训练的过程中，需要保存训练过程中的数据，并且再下次训练的时候能够接着上次训练的结果进行断点续训。所以通过Tensorflow2官网查询到对于model.fit相关的回调函数的编写方法。下面总结了一下，在Tensorflow中，对于参数保存以及断点续训的内容，在最后会给出一个示例代码，供大家参考。

>1. 保存训练数据

如何保存训练过程的数据，包括训练轮数（Epoch）,训练集acc，训练集loss，验证集acc，验证集loss。通过Tensorflow官网可以找到一个保存训练数据的回调函数，即tf.keras.callbacks.CSVLogger。使用方法很简单，只需要指定保存路径，然后将方法加入到model.fit的callbacks参数列表中。示例代码如下:
```
#参数说明
#append: 是否在指定文件基础上追加内容，
csv_logger = CSVLogger('training.log',append=False)
model.fit(X_train, Y_train, callbacks=[csv_logger])
```

>2. 保存与加载模型

保存模型可以借助tf.keras.callbacks.ModelCheckpoint()进行保存。接口说明如下:我们在使用过程中，大致只需要使用filepath,save_best_only,save_weight_only三个参数。其中filepath指明保存文件的路径，save_best_only说明是否只保存为佳模型，save_weight_only说明是否只保存模型权重。
```python
tf.keras.callbacks.ModelCheckpoint(
    filepath, monitor='val_loss', verbose=0, save_best_only=False,
    save_weights_only=False, mode='auto', save_freq='epoch',
    options=None, initial_value_threshold=None, **kwargs
)
```
我们保存了模型，就需要加载模型，加载模型就通过model.load_weights(filepath)进行模型的加载操作。

这里给出一个示例代码，供大家参考，具体需求可以根据代码修改。
```python

model = TestModel()
model.compile(....)

# 读取保存的模型权重
checkpoint_save_path = './checkpoint/Baseline.ckpt'
checkpoint_save_best_path = './checkpoint_best/Baseline.ckpt'
if os.path.exists(checkpoint_save_path + '.index'):
    print('------load the model------')
    model.load_weights(checkpoint_save_path)
    
# 这里只保存权重（这样在下一次训练开始的时候，就能够接着上一次的训练继续训练）
cp_callback_save = tf.keras.callbacks.ModelCheckpoint(filepath=checkpoint_save_path, save_weights_only=True)
# 这里只保存最优模型（这样在训练结束后，能够保存训练过程中的最优模型）
cp_callback_save_best = tf.keras.callbacks.ModelCheckpoint(filepath=checkpoint_save_best_path,save_weights_only=True,save_best_only=True)

model.fit(.....,callbacks=[cp_callback_save,cp_callback_save_best])

```

>3. 示例代码

前面介绍了参数和模型的保存，回到刚刚的问题，我们需要在上一次训练后继续之前的训练过程，并且保存数据参数。首先，如果要接着上一轮继续训练，那么就需要知道上一轮训练了多少轮，我们可以通过我们的参数数据文件，很容易得出我们训练了多少论，接着我们可以借助model.fit的initial_epoch指定起始轮数，这样就可以使得训练接着上一轮继续训练。参考代码如下：
```python
# 返回训练轮数,filename:训练数据保存文件路径
def get_init_epoch(filename):
    with open(filename) as f:
        f_csv = csv.DictReader(f)
        count = 0
        for row in f_csv:
            count = count+1
        return count

init_epoch = 0 # 起始轮次
if os.path.exists(filename):
    init_epoch = get_init_epoch(filename)
model = Test()
model.compile(...)


checkpoint_save_path = './checkpoint/Baseline.ckpt'
checkpoint_save_best_path = './checkpoint_best/Baseline.ckpt'
if os.path.exists(checkpoint_save_path + '.index'):
    print('------load the model------')
    model.load_weights(checkpoint_save_path)

cp_callback_save = tf.keras.callbacks.ModelCheckpoint(filepath=checkpoint_save_path, save_weights_only=True)
cp_callback_save_best = tf.keras.callbacks.ModelCheckpoint(filepath=checkpoint_save_best_path,save_weights_only=True,save_best_only=True)

# 通过设置append可以选择是否在原文件上添加还是重新创建增加内容
csv_logger = CSVLogger('training_log',append=True) 

model.fit(....,callbacks=[csv_logger,cp_callback_save_best,cp_callback_save])
```

#### 如果你需要自定义回调函数，可以参考下列资料


所有回调函数都将 keras.callbacks.Callback 类作为子类，并重写在训练、测试和预测的各个阶段调用的一组方法。回调函数对于在训练期间了解模型的内部状态和统计信息十分有用。

回调函数方法概述
全局方法
on_(train|test|predict)_begin(self, logs=None)
在 fit/evaluate/predict 开始时调用。

on_(train|test|predict)_end(self, logs=None)
在 fit/evaluate/predict 结束时调用。

Batch-level methods for training/testing/predicting
on_(train|test|predict)_batch_begin(self, batch, logs=None)
正好在训练/测试/预测期间处理批次之前调用。

on_(train|test|predict)_batch_end(self, batch, logs=None)
在训练/测试/预测批次结束时调用。在此方法中，logs 是包含指标结果的字典。

周期级方法（仅训练）
on_epoch_begin(self, epoch, logs=None)
在训练期间周期开始时调用。

on_epoch_end(self, epoch, logs=None)
在训练期间周期开始时调用。

基本示例
让我们来看一个具体的例子。

```python
class CustomCallback(keras.callbacks.Callback):
    def on_train_begin(self, logs=None):
        keys = list(logs.keys())
        print("Starting training; got log keys: {}".format(keys))

    def on_train_end(self, logs=None):
        keys = list(logs.keys())
        print("Stop training; got log keys: {}".format(keys))

    def on_epoch_begin(self, epoch, logs=None):
        keys = list(logs.keys())
        print("Start epoch {} of training; got log keys: {}".format(epoch, keys))

    def on_epoch_end(self, epoch, logs=None):
        keys = list(logs.keys())
        print("End epoch {} of training; got log keys: {}".format(epoch, keys))

    def on_test_begin(self, logs=None):
        keys = list(logs.keys())
        print("Start testing; got log keys: {}".format(keys))

    def on_test_end(self, logs=None):
        keys = list(logs.keys())
        print("Stop testing; got log keys: {}".format(keys))

    def on_predict_begin(self, logs=None):
        keys = list(logs.keys())
        print("Start predicting; got log keys: {}".format(keys))

    def on_predict_end(self, logs=None):
        keys = list(logs.keys())
        print("Stop predicting; got log keys: {}".format(keys))

    def on_train_batch_begin(self, batch, logs=None):
        keys = list(logs.keys())
        print("...Training: start of batch {}; got log keys: {}".format(batch, keys))

    def on_train_batch_end(self, batch, logs=None):
        keys = list(logs.keys())
        print("...Training: end of batch {}; got log keys: {}".format(batch, keys))

    def on_test_batch_begin(self, batch, logs=None):
        keys = list(logs.keys())
        print("...Evaluating: start of batch {}; got log keys: {}".format(batch, keys))

    def on_test_batch_end(self, batch, logs=None):
        keys = list(logs.keys())
        print("...Evaluating: end of batch {}; got log keys: {}".format(batch, keys))

    def on_predict_batch_begin(self, batch, logs=None):
        keys = list(logs.keys())
        print("...Predicting: start of batch {}; got log keys: {}".format(batch, keys))

    def on_predict_batch_end(self, batch, logs=None):
        keys = list(logs.keys())
        print("...Predicting: end of batch {}; got log keys: {}".format(batch, keys)) 

```