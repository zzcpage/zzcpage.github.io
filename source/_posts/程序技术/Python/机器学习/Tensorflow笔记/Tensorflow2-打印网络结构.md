---
title: TensorFlow2笔记-打印网络结构
categories:
    - 深度学习
    - TensorFlow2
tags:
    - TensorFlow2
---

我们通常需要借助网络中各个层的输出shape来确定我们的网络模型是否设置正确，这通常出现在确定卷积网络各层卷积后的shape与预期结果一致的时候。所以我们在这里介绍一下如何在TensorFlow中打印网络的结构。

在这里主要介绍两种打印网络模型结构的方法，一种是通过Tensorboard，另一种是借助model.summary()。

#### 通过Tensorboard打印网络模型

通过Tensorboard打印网络模型，只需要通过给model.fit设置一个回调函数即可。例如：
```python

 log_dir = "./logs/fit/" + datetime.datetime.now().strftime("%Y%m%d-%H%M%S")
 tensorboard_callback = tf.keras.callbacks.TensorBoard(log_dir=log_dir, histogram_freq=1)
model.fit(.....,callbacks=[tensorboard_callback])
```

在运行代码的时候，通过tensorboard打开对于的logs文件，然后就可以在浏览器页面-Graph查看到网络的模型，上面通常标明了在模型内部的数据流动的形状大小。

#### 通过model.summary()打印模型

假设我们有一个自定义模型model,我们可以通过定义一个get_model()方法，来帮助我们输出网络各个层的输出结果，参考代码如下：
```python

class Test(Model):
    '''
        卷积核大小4*4 , 使用adam , 学习率10^-5,小批量大小80
        每个模型包括4个卷积层和两个完全连接层
        每次卷积操作之后，依次执行批标准化处理、ReLU非线性激活、最大池和退出。
        采用ReLU非线性激活，最大池
    '''

    def __init__(self):
        super(Test,self).__init__()
      
    def call(self, x):
        return x

    def test_model(self):
        x = Input(shape=(32,32,1)) # 这里设置你网络的一个输入的形状大小，比如我的是32*32通道数为1的数据。
        return Model(inputs=[x],outputs=self.call(x))

if __name__ == '__main__':
    model = Test()
    model.test_model().summary()

```