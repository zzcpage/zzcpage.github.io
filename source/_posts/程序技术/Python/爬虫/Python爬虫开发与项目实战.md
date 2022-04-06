---
title: Python爬虫与开发项目实战——第一回合
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---
##### 背景介绍

<font color="red" size=3>&nbsp;&nbsp;&nbsp;&nbsp;这一系列主要是对爬虫进一步深入了解,学习之前已经了解过爬虫，并编写过相应的代码，现在阅读书籍，进一步对爬虫的原理进行理解。大家可以通过该系列的开发过程，掌握爬虫的运用，以及进一步了解爬虫的原理。</font>

1. ###### 序列化

把内存中的变量变成可存储或可传输的过程，就是序列化。将内存中的变量序列化之后，可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上，实现程序状态的保存和共享。反过来，把变量内容从序列化的对象重新读取到内存，称为反序列化。
>也就是说，序列化就是保存了变量的一个快照(某个时刻的值)。反序列化就是根据保存的快照，将值赋值给变量，这样就回到了那个时刻(因为各个变量的值都一样)。
Python对序列化的支持：cPickle和pickle来实现序列化。一般都是先导入cPickle模块。实例代码如下:
```python
### 导入序列化模块
try:
    import cPickle as pickle
except ImportError:
    import pickle

"""
    pickle实现序列化主要使用dumps方法或dump方法，
    dumps方法可以将任意对象序列化成一个str，然后可以将这个str写入文件进行保存.
    dump方法可以将序列化后的对象直接写入到文件中
    
    pickle实现反序列化，主要通过loads或load方法，把序列化后的文件从磁盘上读取一个str，然后使用loads方法将str转化位对象
    或者直接使用load方法将文件直接反序列化位对象
"""
#dumps
d = dict(url="index.html",title="首页",content="首页")
str = pickle.dumps(d)
print(str)

#dump
f = open(r'dump.txt','wb')
pickle.dump(d,f)
f.close()

#通过load方法
f = open(r'dump.txt','rb')
d = pickle.load(f)
f.close()
print(d)
```

2. ###### 进程和多进程

python对多进程的方法：一种通过os模块的fork方法(适用于unix和linux操作系统)，一种通过multiprocessing模块(跨平台的实现方式)。这里主要采用Multiprocessing来创建多进程.
```python
#multiprocessing模块创建多进程

#通过Process类来描述一个进程对象，创建子进程时，只需要传入一个执行函数和函数参数，
#即可完成一个Process实例的创建，用strt()方法启动进程，用join方法实现进程间的同步

#子进程要执行的代码
def run_proc(name):
    print('Child process %s (%s) Running...'%(name,os.getpid()))

##进程池任务
def run_task(name):
    print('Task %s (pid=%s) is runing...'%(name,os.getpid()))
    time.sleep(random.random()*3)
    print('Task %s end.'%name)
#写数据进程执行的代码
def proc_write(q,urls):
    print('Process(%s) is writing...'%os.getpid())
    for url in urls:
        q.put(url)
        print('Put %s queue...'%url)
        time.sleep(random.random())
#读数据进程执行的代码
def proc_read(q):
    print('Process(%s) is reading'%os.getpid())
    while True:
        url = q.get(True)
        print('Get %s from queue.'%url)
def 多进程():
    # print('Parent process %s.'%os.getpid())
    # for i in range(5):
    #     p = Process(target=run_proc,args=(str(i),))#指定子进程要执行的方法，以及传递的参数
    #     print('Process will start.')
    #     p.start()
    # p.join()
    # print('Process end')

    # 通过进程池创建多个进程
    print('Current process %s.' % os.getpid())
    p = Pool(processes=3)  # 创建进程池，指定进程池中进程的个数，默认位CPU核数
    for i in range(5):
        p.apply_async(run_task, args=(i,))
    print('Waiting for all subprocess done ...')
    p.close()  # g关闭进程池，就不能继续向进程池中添加新的任务
    p.join()  # 使用join表示等待所有子进程结束

    # 进程通信 通过Queue或者Pipe(一般用于两个进程通信)实现进程通信
    # 通过Queue进行get和put操作，可以设置blocked和timeout两个属性，blocked是设定操作是否阻塞
    # timeout是设置操作的等待时间。
    q = Queue()  # 创建消息队列
    proc_write1 = Process(target=proc_write, args=(q, ['url_1', 'url_2', 'url_3']))
    proc_write2 = Process(target=proc_write, args=(q, ['url_4', 'url_5', 'url_6']))
    proc_reader = Process(target=proc_read, args=(q,))
    # 启动子进程proc_writer写入
    proc_write1.start()
    proc_write2.start()
    # 启动子进程读取
    proc_reader.start()
    # 等待写入结束
    proc_write1.join()
    proc_write2.join()
    # 由于读取是死循环，所以只能强行终止
    proc_reader.terminate()
```

3. ###### 多线程

```python
#多线程
def thread_run(urls):
    print('Current %s is running ...'%threading.current_thread().name)
    for url in urls:
        print('%s ------>>> %s'%(threading.current_thread().name,url))
        time.sleep(random.random())
    print('%s ended.'% threading.current_thread().name)

#继承创建线程类
class MyThread(threading.Thread):
    def __init__(self,name,urls):
        threading.Thread.__init__(self,name=name)
        self.urls =urls
    def run(self):
        print('Current %s is running ...' % threading.current_thread().name)
        for url in self.urls:
            print('%s ------>>> %s' % (threading.current_thread().name, url))
            time.sleep(random.random())
        print('%s ended.' % threading.current_thread().name)
if __name__ == '__main__':
    print('in')
    """"
        多线程，线程类似于执行多个不同的程序，多线程可以将允许时间长的任务放到后台处理。
        Python对多线程的支持，thread和threading，一般我们使用threading模块
    """
    print('%s is running...'%threading.current_thread().name)
    t1 = threading.Thread(target=thread_run,name='Thread_1',args=(['url_1','url_2','url_3'],))
    t2 = threading.Thread(target=thread_run, name='Thread_2', args=(['url_4', 'url_5', 'url_6'],))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print('%s ended.'%threading.current_thread().name)

    #自定义线程类
    print('%s is running...' % threading.current_thread().name)
    t1 = MyThread(  name='Thread_1', urls=(['url_1', 'url_2', 'url_3']))
    t2 = MyThread(  name='Thread_2', urls=(['url_4', 'url_5', 'url_6']))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print('%s ended.' % threading.current_thread().name)

    ##线程同步
    ##一般通过Thread的Lock和RLock实现简单的线程同步，两个对象都有acquire和release方法
    mylock = threading.RLock() #创建一个锁
    num = 0
    class myThread1(threading.Thread):
        def __init__(self,name):
            threading.Thread.__init__(self,name=name)
        def run(self):
            global num
            while True:
                mylock.acquire()
                print('%s locked,Number :%d'%(threading.current_thread().name,num))
                if num >=4 :
                    mylock.release()
                    print('%s released, Number: %s'%(threading.current_thread().name,num))
                    break
                num += 1
                print('%s released Number %s'%(threading.current_thread().name,num))
                mylock.release()
    thread1 = myThread1('Thread_1')
    thread2 = myThread1('Thread_2')
    thread1.start()
    thread2.start()

```

4. 协程

```python
#各个协程执行的任务
def run_task(url):
    print('Visis --> %s'%url)
    try:
        data  = urllib3.connection_from_url(url=url).urlopen(url=url,method="GET").data
        print('%s bytes received from %s.'%(len(data),url))
    except Exception as e:
        print(e)


if __name__ == '__main__':
    print('in')
    # 协程又称微线程(纤程),用户级的轻量级线程
    #协程能够保留上一次调用时的状态
    #Python对写出的支持通过gevent库，Python通过yield提供对协程的基本支持但是不完全，所以使用
    #gevent更加好
    #gevent实际上是greenlet在实现切换工作。如果出现io阻塞的时候，gevent会自动切换到没有阻塞的代码执行
    #所以gevent一直保持greenlet在允许
    urls = ['https://github.com/','https://www.python.org/','http://www.baidu.com/'] # 各个协程访问的网址
    greenlets = [gevent.spawn(run_task,url) for url in urls] # 这里将各个协程加入到greenlets中
    gevent.joinall(greenlets=greenlets) #进行执行各个协程

```

5. 分布式进程

分布式进程：分布式也就是将计算任务分布到多个计算机上进行运算，然后将结果返回。分布式进程也就是指，将Process进程分不到多台机器，利用多台机器的性能，完成复杂的任务。
        
对于分布式进程，通过multiprocessing模块的managers子模块，将多线程分布到多台机器上。

一般我们通过分布式处理任务，将某块的任务分配给某个机器执行，然后某个功能模块给其他模块执行。将任务分成多个计算机集群进行处理，提高速度。例如爬取图片，可以一个计算机专门爬取图片链接，然后多个计算机专门根据爬取到的图片链接下载图片。
将中间处理的结果，让其他机器进程都能访问的过程称为本地队列的网络化。过程如下所示：
![a5](/images/分布式.PNG)

两个实现代码

taskManager.py
```python
import queue
from multiprocessing.managers import BaseManager
from multiprocessing import freeze_support

#服务进程

#任务个数
task_number = 10
#定义收发队列
task_queue = queue.Queue(task_number)
result_queue = queue.Queue(task_number)

def get_task():
    return task_queue
def get_result():
    return result_queue

#创建类似的QueueManager
class QueueManager(BaseManager):
    pass
def win_run():
    #Windows下绑定调用接口
    QueueManager.register('get_task_queue',callable=get_task)
    QueueManager.register('get_result_queue',callable=get_result)
    #绑定端口并设置验证口令，Windows下需要填写IP地址
    manager = QueueManager(address=('127.0.0.1',8001),authkey='qiye'.encode('utf-8'))
    #启动
    manager.start()
    try:
        #通过网络获取任务队列和结果队列
        task = manager.get_task_queue()
        result = manager.get_result_queue()
        #添加任务
        for url in ['ImageUrl_'+str(i) for i in range(10)]:
            print('put task %s...'%url)
            task.put(url)
        print('try get result...')
        for i in range(10):
            print('result is %s '%result.get(timeout=10))
    except:
        print('Manager error')
    finally:
            #一定要关闭，否则会报管道未关闭的错误
            manager.shutdown()
if __name__ == '__main__':
    #Windows多线程可能有问题，进行缓解
    freeze_support()
    win_run()
```

taskWorker.py
```python
import time
from multiprocessing.managers import  BaseManager

#处理进程

#创建类似的QueueManager
class QueueManager(BaseManager):
    pass
#第一步使用Queuemanager注册用于获取Queueu的方法名称
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')
#第二部连接到服务器
server_addr =  '127.0.0.1'
print('Connect to server %s...'%server_addr)
#端口哦和验证口令
m = QueueManager(address=(server_addr,8001),authkey='qiye'.encode('utf-8'))
#从网络链接
m.connect()
#第三步获取Queue的对象
task = m.get_task_queue()
result = m.get_result_queue()
#第四步，从task队列获取任务，把结果写入到result队列
while(not task.empty()):
    image_url = task.get(True,timeout=5)
    print('run task download %s...'%image_url)
    time.sleep(1)
    result.put("%s--->success"%image_url)
#处理结束
print('worker exit .')
```

6. 网络编程

两台计算机之间的通信，实际上是两台计算机，端口之间的通信。当在浏览器浏览网页的时候，实际上就是本地计算机的一个端口进程和服务器的某个端口进程建立了连接，并进行通信。

一般通过Socket（套接字）,描述通信。Socket由ip+端口组成。Python提供了两个Socket模块。
Socket：提供了标准的BSD Sockets API
SocketServer: 提供了服务器中心类，可以简化网络服务器的开发。

>Socket类型
套接字格式为：socket(family,type[,protocal]),使用给定的地址族，套接字类型，协议编号（默认为0）来创建套接字。
套接字类型如下所示：
![a6](/images/Socket类型.PNG)

>Socket常用函数
![a61](/images/Socket2.PNG)

>TCP编程

TCP-Server.py
```python

# 网络编程包含两个部分：服务端和客户端
# TCP是面向连接的通信方式
# 主动发起连接的叫做客户端
# 被动响应连接的叫服务端


# 创建TCP服务端
"""
1. 创建Socket,绑定Socket到本地IP与端口
2. 开始监听连接
3. 进入循环，不断接收客户端的连接请求
4.接收传来的数据，并发送给对方数据
5.传输完毕后，关闭Socket
"""
import socket
import threading
import time
def dealClient(sock,addr):
    # 第四步：接收传来的数据，并发送给对方数据
    print('Accept new connection from %s:%s...'%(sock,addr))
    sock.send(b'Hello,I am server!')#发送数据
    while True:
        data = sock.recv(1024) #接收数据
        time.sleep(1)
        if not data or data.decode('utf-8') == 'exit':
            break
        print('---->>>%s!'%data.decode('utf-8'))
        sock.send(('Loop_Msg:%s!'%data.decode('utf-8')).encode('utf-8'))
    #关闭Socket
    sock.close()
    print('Connection from %s:%s closed.'%(sock,addr))
if __name__ == '__main__':
    #第一步：创建基于IPv4和TCP协议的Socket
    #Socket绑定IP（127.0.0.1为本机IP）与端口
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    s.bind(('127.0.0.1',9999))
    #第二步：监听连接
    s.listen(5)#最大连接数量
    print('Waiting for connection...')
    while True:
        #第三步: 接收一个新连接
        sock,addr = s.accept()
        #创建新线程来处理TCP连接
        t = threading.Thread(target=dealClient,args=(sock,addr,))
        t.start()

```
TCP-Client.py
```python

#TCP客户端

"""
1.创建Socket，连接远端地址
2.连接后发送数据和接收数据
3.传输完毕后，关闭Socket
"""
import socket
#初始化Socket
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
#连接目标的ip和端口
s.connect(('127.0.0.1',9999))
#接收消息
print('--->>>'+s.recv(1024).decode('utf-8'))
#发送消息
s.send(b'Hello,I am Client')
print('--->'+s.recv(1024).decode('utf-8'))
s.send(b'exit')
#关闭Socket
s.close()
```

>UDP编程

UDP-Server.py
```python
# UDP 服务器端
"""
UDP是面向无连接的协议。使用UDP协议时，不需要建立连接，只需要直到对方的IP地址和端口号，就可以直接发数据包。
并不关心能够到达目的端。对于不要求可靠到达的数据，就可以使用UDP协议。
"""
"""
服务端创建过程：
    1. 创建Socket，绑定指定的ip和端口
    2. 直接发送数据和接收数据
    3. 关闭Socket
"""
import socket

# 创建Socket,绑定指定IP和端口
# SOCK_DGRAM指定了这个Socket的类型是UDP，绑定端口和TCP示例一样
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.bind(('127.0.0.1', 9999))
print('Bind UDP on 9999...')
while True:
    # 直接发送数据和接收数据
    data, addr = s.recvfrom(1024)
    print('Received from %s:%s. ' %(addr,data.decode('utf-8')))
    s.sendto(b'Hello,!', addr)

```

DUP-Client.py

```python

#UDP的客户端
# UDP客户端，创建Socket即可与服务器数据交换。
import socket
s = socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
for data in [b'Hello',b'World']:
    #发送数据
    s.sendto(data,('127.0.0.1',9999))
    #接收数据
    try:
        print(s.recv(1024).decode('utf-8'))
    except:
        pass
s.close()
```

>小结

两个计算机之间的通信是端口进程之间的通信。端口属于应用层。对于应用层之间的数据，通过运输层提供相应的数据。运输层的协议，有TCP和UDP传输协议。

对于TCP协议，是面向连接的，所以在使用tcp协议传送数据的时候，需要保证两个通信的主机建立了连接，然后再进行数据的传输。
>
>   所以对于TCP而言，服务器和客户端的连接步骤
>对于TCP服务器：
1. 创建Socket，绑定到对应端口
2. 监听端口，检测是否有连接
3. 如果有客户端连接，接收客户端连接，可以获得客户端的套接字
4. 建立连接后，可以循环监听接收客户端发送的数据，也可发送数据给客户端
5. 会话完毕，就可以关闭连接

> 对于TCP客户端
> 1. 创建Socket
> 2. 连接到服务器，然后建立连接
> 3. 发送，接收数据
> 4. 会话完毕，关闭连接

对于UDP协议，是面向无连接的，所以在使用UDP协议的时候，并不需要确定双方是否建立了连接，直接向对应的（地址，端口号），发送数据即可，不关心是否发送成功。同理，接收数据也是直接接收即可。因为发送的时候就是直接发到对应主机，然后接收直接接收即可。

> 对于UDP服务器
> 1. 创建Socket,绑定端口
> 2. 监听端口，查看是否有数据
> 3. 接收到数据（data,addr），可以发送给数据到指定的addr

>对于UDP客户端
>1. 创建Socket
>2. 向服务器(addr,post)发送数据
>3. 可以监听自己的计算机，是否有其他计算机发送的数据

>总结

从上面TCP/UDP客户端和服务器的创建过程可以比较出，TCP需要确定连接，而对于UDP而言不需要确定是否连接成功。

7. WEB前端基础

>W3C标准

W3C即万维网联盟，W3C标准是一系列标准的集合。

一个网页由三部分组成：结构(Structure),表现(Presentation),行为(Behavior)。对应

结构化标准语言：XHTML，XML
表现标准语言：CSS
行为标准：对象模型(如W3C DOM)，ECMAScript等。

HTML（超文本标记语言），也就是是一种标签语言。

CSS(层叠样式表): 用于解决内容和表现的分离。CSS主要由选择器+属性构成。选择器用于指定渲染元素，属性用于指定渲染效果。

JavaScript（轻量级脚本语言）

XPath: 一门在XML文档中查找信息的语言。

JSON：JavaScript对象表示法，用于存储和交换文本信息。

HTTP协议(超文本传输协议)，用于从www服务区传输超文本到本地浏览器的传送协议。HTTP协议永远都是客户端发起请求，服务器会送响应。

HTTP协议是一个无状态协议，同一个客户端的这次请求和上次请求没有对应关系。

HTTP状态码：

200--请求成功
301--资源网页被永久转移到其他URL
404--请求的资源不存在
500--内部服务器错误

1开头一般表示服务器收到请求，需要请求者继续执行操作
2开头一般表示请求操作成功
3开头一般表示重定向
4开头一般表示客户端错误
5开头一般表示服务器错误


>Cookie和Session都用于保存状态信息，Cookie保存在客户端，Session保存在服务器端。

Cookie：服务器给每个Session分配一个唯一的JSESSIONID，并通过Cookie发送给客户端，当客户端发起新的请求的时候，将在Cookie头中携带JSESSIONID。这样服务器就能够找到这个客户端对应的Session。

![a7](/images/Cookie.PNG)


   



