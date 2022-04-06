---
title: Python爬虫开发与项目实战-深入篇
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

#### 1. 增量式爬虫

增量爬虫就是爬取新的数据，而不对旧的数据重新爬取。所以增量爬虫的核心在于快速去重，判断哪些数据是已经爬取过的，哪些是新产生的。

>去重方案
>>1. URL去重
>>>URL去重对一些网址可能适用，而对于一些网站可能不适用，需要具体问题具体分析。
>>>URL去重的几种方案
>>>1. 关系数据库去重：每当需要请求一个URL时候，通过SQL查询语句进行查询，判断是否重复，但是如果数据量变的很大的时候，关系数据库查询效率会变得很低，所以不推荐。
>>>2. 缓存数据库去重：常用的缓存数据库的Redis,去重方式是使用其中的Set数据类型，
类似于Python中的Set，也是一种内存去重方式，但是它可以将内存中的数据持久化到硬盘中，应用较为广泛，推荐使用。
>>>3. 内存去重：
>>>>1. 将URL存储到HashSet中，也就是借助Python中的Set数据结构，但是内存消耗大。
>>>>2. 将URL经过MD5或者SHA-1等单向哈希算法生成摘要，然后存储到HashSet中，这样旧可以节省内存，
>>>>3. 采用Bit-Map方法，建立一个BitSet，将单个URL经过一个哈希函数，映射到某一位。这种方法内存消耗最少，但是冲突概率高，容易误判。
>>>通过内存去重受制于内存大小和掉电易失的特性。

相对较好的去重方案：内存去重方案（建议采用第二中，通过摘要算法保存单一信息）+缓存数据库 。 

>BloomFilter算法
布隆过滤器(BloomFilter)是一种多哈希函数映射的快速查找算法。BloomFilter利用位数组简洁的表示一个集合，能判断一个元素是否属于这个集合。BloomFilter可能会把不属于这个集合的元素误认为属于这个集合，所以BloomFilter不适用于零错误的场景，适用于允许低错误率的应用场合。

>>BloomFilter算法流程
>>1. 创建一个m位的位数组,将所有位初始化为0
>>2. 选择K个不同的哈希函数，每个哈希函数的结果为0-(m-1)
>>3. 如果输入x,则将x分别作为k个哈希函数的输入，并把结果对应的位数置为1
>>4. 如果要判断x是否被记录过，只需要将x分别作为k个哈希函数的输入，然后检查每个结果对应位是否为1，如果有一个不为1，就没有被记录过。

>BloomFilter的关在在于K个哈希函数的选择问题

选择K个哈希函数是比较麻烦的，一种常用的方法是选择一个哈希函数，然后送入K个不同的参数(这就等同于K个哈希函数，就不简简单单以输入x作为输入)。

>Python中BloomFilter

可以在GitHub中找到一个开源项目python-bloomfilter[网址](https://github.com/qiyeboy/python-bloomfilter)，下载,然后在解压目录运行 python setup.py install完成安装。
Scrapy中自带了去重方案，同时支持RFPDupeFilter来完成去重。

需要在settings中设置DUPEFILTER_CLASS字段，指定过滤器类的路径。
DUPEFILTER_CLASS="过滤器类路径"

```python
import hashlib

from pybloom import ScalableBloomFilter # 引入布隆过滤器
# Scrapy自带去重方案，支持RFPDupeFilter完成去重
from scrapy.dupefilters import RFPDupeFilter
from w3lib.url import canonicalize_url


class URLBloomFilter(RFPDupeFilter):
    def __init__(self,path=None):
        self.urls_sbf = ScalableBloomFilter(mode=ScalableBloomFilter.SMALL_SET_GROWTH)
        RFPDupeFilter.__init__(self,path)

    # 这里在检测URL是否重复
    def request_seen(self,request):
        fp = hashlib.shal()
        fp.update(canonicalize_url(request.url))
        url_shal = fp.hexdigest()
        # 通过布隆过滤器判断url是否重复
        if url_shal in self.urls_sbf:
            return True
        else:
            self.urls_sbf.add(url_shal)

```

#### 2. 分布式爬虫与Scrapy

分布式简单的说就是把任务分配给多台机器同时处理，与之对应的是单机爬虫，单点部署，单点操作等。分布式的关键是如何将各个机器进行关联，调度，常用的方式是借助消息队列将各个主机进行关联。

#### 3. 实战项目:Scrapy分布式爬虫

Python安装Redis：pip install redis



#### 4. 人性化的PySpider爬虫框架