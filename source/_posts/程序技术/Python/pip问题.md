
##### pip install dlib

下载dlib需要有一些必备的条件。按如下方式运行程序可以完美解决。
```python
pip install boost
pip install cmake
pip install dlib
```
测试方法：进入cmd，输入如下命令
```
python
import dlib
```

如果还是出现错误，可以直接通过whl文件进行下载。这里贴一个支持python3.7的whl文件，需要可以自取。链接: [https://pan.baidu.com/s/1pWKH9MnKQmPzKmaJk4-zeQ?pwd=4dsn](https://pan.baidu.com/s/1pWKH9MnKQmPzKmaJk4-zeQ?pwd=4dsn) 提取码: 4dsn。

下载完成后，通过cmd命令到whl下载目录，然后通过pip install dlib-19.17.99-cp37-cp37m-win_amd64.whl 即可完成下载。如果需要其他版本的whl文件，可以访问清华镜像[https://pypi.tuna.tsinghua.edu.cn/simple/dlib/](https://pypi.tuna.tsinghua.edu.cn/simple/dlib/)。