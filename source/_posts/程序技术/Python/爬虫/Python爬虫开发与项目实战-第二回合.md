---
title: Python爬虫开发与项目实战-第二回合
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

网络爬虫：是一种按照一定的规则，自动地抓取万维网信息的程序或者脚本。

网络爬虫可以分为：

>1. 通用网络爬虫

通过搜索引擎搜索关键词，然后从搜索引擎返回的数据中，爬取想要的数据。

>2. 聚焦网络爬虫

有针对性的爬取指定的网页链接。定向爬取相关页面。

>3. 增量式网络爬虫

也就是爬取新的数据，而对于之前爬取过的数据，不会再进行爬取。

>4. 深层网络爬虫

也就是爬取一些不能通过静态链接获取的，需要用户操作后才能访问获取的Web页面。

###### 网络爬虫结构

爬虫的一般流程：

1. 需要爬取页面的URL
2. 读取URL，获取到网页数据
3. 从网页数据提取有用数据
4. 抽取网页需要进一步爬取URL，进行再次爬取

![1.1](/images/爬虫流程.PNG)


###### Request库

```python
"""
Python对HTTP请求的支持：通过urllib2和urllib或者通过request模块。
一般通过request实现http请求。


    #发送get请求
    r = requests.get('')
    # 对于带有参数的url，可以通过建立一个map作为参数进行请求
    payload = {'keywords':'blog:qiyeboy','pageindex':1}
    r = requests.get('',params=payload) #通过map提供参数
    #发送post请求
    r = requests.post('')
    
"""
import requests
#chardet是用于检测文本编码
import chardet

def requestTemp():
    r = requests.get('www.baidu.com')
    print(r.content) # 返回字节类型的数据
    print(r.text) # 返回文本形式的html
    print(r.encoding) # 返回网页编码格式
    r.encoding = 'utf-8' # 可以自定义编码格式。然后再读取网页文本数据，这样就不会乱码
    print(r.text) # 这是在utf-8编码下的文本数据
    #通过chardet进行解码
    r.encoding = chardet.detect(r.content)['encoding']

    ### 我们可以对请求头headers进行处理,即对请求设置请求头,设置Cookie
    ### 对于请求头，我们可以通过f12查看请求头格式
    user_agent = 'Mozilla/4.0'
    headers = {'User-Agent':user_agent} #设置请求头
    #自定义Cookie
    cookies = dict(name='qiye',age='10')

    r = requests.get('www.baidu.com',headers=headers,cookies=cookies)

    # 获取响应的Cookie值
    for cookie in r.cookies.keys():
        print(r.cookies.get(cookie))

    ### 获取返回状态编码，判断请求是否成功
    if r.status_code == requests.codes.ok :
        print(r.status_code)
        print(r.headers.get('content-type'))
    else:
        r.raise_for_status() #通过raise_for_status可以抛出一个异常。

    ###自动处理Cookie方法 , 通过session，每次都可以将Cookie值带上
    s = requests.Session()
    r = s.get("wwww.baidu.com",allow_redirects=True)
    datas = {'name':'qiyi','passwd':'123'}
    # 通过Session机制，可以保证每次都加上了cookie的值，进行请求
    r = s.post('wwww.baidu.com',data=datas,allow_redirects=True)
    # 通过allow_redirects可以设置是否允许重定向

    # 通过r.history可以获取到历史信息，也就是访问成功之前的所有请求跳转信息
    print(r.history)

    #设置超时参数，Timeout
    r = requests.get('www.baidu.com',timeout=2)

    #代理设置，使用代理Proxy,可以为任意请求方法通过设置proxies参数来配置单个请求
    proxies = {
        "http":"http://0.10.1.10:3128",
        "https":"http://10.10.1.10:1080",
        #"http":"http://user:pass@10.10.1.10:3128" #这是代理中身份认证的用户名和密码，来设置代理
    }
    requests.get("www.baidu.com",proxies=proxies) #设置代理ip
```


###### 网页解析

我们通过requets可以下载网页中的文本，那么我们怎么通过下载的文本获取到想要的信息呢？这里就需要使用到文本解析技术了，常用的方法有很多，我们这里采用BeautifulSoup库进行解决。BeautifulSoup具备的解析器如下所示：

BeautifulSoup将HTML文档转换成一个复杂的树形结构，每个节点都是一个python对象，所有对象可以归纳为四种。

>1. Tag
>2. NavigableString
>3. BeautifulSoup
>4. Comment

BeautifulSoup对网页的解析，主要是搜索指定标签，遍历标签元素，提取标签内容。对于解析文本的方法，可以参考如下代码，本质上就是调用API定位到要爬取到的数据信息。

```python
"""
正则表达式：
\b: 匹配单词的开始或结束
^:  匹配字符串的开始
$:  匹配字符串的结束
\w: 匹配字母，数字，下划线或汉字
\s: 匹配任意空白字符
\d: 匹配数字
. : 匹配除换行符以外的任意字符

字符串转义: 通过\进行转义
数量匹配：
*：零次或多次
+： 一次或多次
？：零次或一次
{n}: n次
{n,}: n次或更多次
{n,m} 重复n-m次

分支条件：
正则表达式通过 | 表示或的关系。
"""

"""
    这里采用BeautifulSoup(美味汁)进行文本解析
"""
from bs4 import BeautifulSoup
import requests
import re
r = requests.get("http://www.baidu.com")
r.encoding = 'utf-8'
html = r.text
# 可以直接打开，不知道解析器，一般采用lxml解析器
soup = BeautifulSoup(html,'lxml',from_encoding='utf-8')

def 获取对象属性():

    #Tag 对象
    #Tag对象就像html标签一样，直接通过soup.标签名进行对象的提取
    #要获取tag对象的属性，通过标签.name获取标签的名称 ， 并且可以设置标签的名称
    #如果需要获取标签的属性，如href和class之类的属性，则通过标签.get(属性名)

    print(soup.a) # 根据标签获取
    print(soup.a.name) #获取标签名称
    print(soup.a.get('href')) # 获取属性名称
    print(soup.a.attrs) # 获取标签中的所有属性

    # 如果要修改标签中的属性，怎么获取也同样可以怎么设置

    # BeautifulSoup用NavigableString类来包装Tag中的字符串，通过标签.string就可以获取到标签内部的文字
    print(soup.a.string)
    print(type(soup.a.string))

    #BeautifulSoup对象表示的是一个文档的全部内容。


def 遍历():
    ### 遍历操作， beautiful soup 对文档树的遍历
    """
    1. contents
    2.  children
    """
    # contents属性，可以将tag的子节点列表输出
    print(soup.head.contents)
    # children属性返回的是一个生成器。可以对Tag的子节点进行循环
    # 也就是通过children可以迭代遍历节点的所有子节点.
    for child in soup.head.children:
        print(child)
    # 如果要递归遍历所有标签的孙子结点，则通过desendants属性，对所有tag的子孙节点进行循环
    # 标签的内容也属于标签的子节点
    for child in soup.head.descendants:
        print(child)

    # 获取结点的内容

    # stirng , 如果标记唯一就返回标记的内容，而如果标记不唯一，可能返回None
    print(soup.a.string)

    # strings属性主要用于tag中包含多个字符串的情况，可以循环遍历
    for string in soup.strings:
        print(string)

    # stripped_strings 可以去掉输出字符串包含的空格或空行
    for string in soup.stripped_strings:
        print(string)

    # 获取父节点，通过parent属性
    print(soup.a.parent)

    # 获取节点的所有父辈节点，通过parents属性
    for parent in soup.a.parents:
        if parent is None:
            print(parent)
        else:
            print(parent.name)

    # 获取节点的兄弟节点,空白或换行也可也被视作一个节点
    # next_sibling获取下一个兄弟节点
    print(soup.a.next_sibling)
    # previous_sibling获取上一个兄弟节点
    print(soup.a.previous_sibling)
    # 通过next_siblings或者previous_siblings可以对当前兄弟节点迭代输出
    for sibling in soup.a.next_siblings:
        print(sibling)

    # 获取节点的前后节点。前后节点是不区分层次结构的前后关系，如<div><a><div>,div的后一个节点就是a
    # next_element,previous_element,
    print(soup.a)
    print(soup.a.next_element)

    # 如果想遍历所有前后节点，通过next_elements和previous_elements进行遍历
    for element in soup.a.next_elements:
        print(element)

# 搜索文档树，搜索指定的内容
def 搜索():

    # find_all()方法，搜索当前tag的所有tag子节点，判断是否满足搜索条件
    # find_add(name,attrs,recursive,txext,**kwargs)
    # name参数可以查找所有名字为name的标记,返回列表.一般用这个来找指定的标签
    # name参数可以是单独的字符，也可以是字符列表。
    # 可以自定义过滤器，用于匹配指定规则的标签
    print(soup.find_all('a'))
    print(soup.find_all(['a','b']))
    def hasClass_Id(tag):
        return tag.has_attr('class') and tag.has_attr('id')
    print(soup.find_all(hasClass_Id)) # 寻找满足匹配规则的标签
    # 多条件过滤标签
    # 可以在find_all()中根据属性搜索指定的标签，并且可以将正则表达式作为搜索条件
    print(soup.find_all('a',href=re.compile('elsie'),id='12',class_='sister'))

    #如果要限制搜索数目，则通过limit参数进行限制
    print(soup.find_all('a',limit=5))

    # 限制只搜索直接节点，而不搜索子孙节点，设置recursive = False
    print(soup.find_all('a',limit=5),recursive=False)

    # CSS选择器
    # 通过元素的CSS属性定位元素的位置
    # 根据name属性通过.class值 , 根据id属性通过#id值
    # 返回类型为list
    #找到所有a标签
    soup.select('a')
    #找到a标签，id为1
    soup.select('a#1')
    #根据name查询
    soup.select('.classs')
    #通过判断是否存在某个属性进行查找
    soup.select('a[href]')
    #通过属性值查找 ， test可以是待查找的字符串，可以通过正则表达式查询
    #href^= "" , href$= , href*= , 进行正则判断。
    soup.select('a[href="test"]')

```