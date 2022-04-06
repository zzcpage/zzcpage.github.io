---
title: Python爬虫-中级
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

### [1.数据存储](#one)
### [2.动态文件抓取](#two)
### [3.Web端协议分析](#three)
### [4.数据存储](#one)
### [1.数据存储](#one)

###### 1.<a id="one"/> 数据存储

>前面介绍了关于数据存储的csv,txt,json方式，这里介绍如何采用数据库保存数据，主要是了解两个数据库，关系数据库和分布式数据库。
即MySQL和MongoDB。




###### 1.1 MySQL

>Python对MySQL的操作通过pymsql模块支持。
Python操作MySQL的代码如下所示：
```python
"""
    Python对MySQL的操作主要是由pymysql模块进行支持。
"""
# 主机ip(host),用户名(user),密码(passwd),数据库名称(db),端口(port),编码(charset)
# 打开数据库， pymysql.connect(host=,user=,passwd=,db=,port=,charset=)
db = pymysql.connect(
    host='localhost',
    user='test',
    passwd='123456',
    db='votemsy',
    charset='utf-8'
)

# 连接数据库成功后就可以操作数据库

# 创建一个游标对象
# 游标是系统为用户开设的一个数据缓冲区，存放SQL语句的执行结果
# 游标对象支持的数据库的操作
cursor = db.cursor()

#需要执行的sql语句
sql = ""
try:
    #执行一条SQL语句
    cursor.execute(sql)
    #执行多条SQL语句
    cursor.executemany(sql)

    #用来从结果中取一条记录，并将游标指向下一条记录
    result = cursor.fetchone()
    #用来从结果中取多条记录
    result = cursor.fetchmany(5)
    #获取所有记录列表
    results = cursor.fetchall()

    # 插入数据
    data = "'qiye',20"
    cursor.execute('INSERT INTO person (name,age) VALUES (%s)'%data)
    # 插入数据，占位符法
    cursor.execute('INSERT INTO person(name,age) VALUES (%s,%s)',('qiye',20))
    #执行多条插入语句
    cursor.executemany('INSERT INTO person(name,age) values ',[('qiye',20),('jack',20)])

    #查询数据
    cursor.execute('SELECT * FROM person')
    res = cursor.fetchall() #获取所有结果
    for line in res:
        print(line)
    cursor.execute('SELECT * FROM person')
    res = cursor.fetchone() #只获取一个结果
    print(res)
    
    #修改和删除数据
    cursor.execute('UPDATE person SET name=%s WHERE id=%s',('rose',1))
    cursor.execute('DELETE FROM person where id=%s',(0,))
    
    # 用来事务提交，只有commit之后，才会提交到数据库进行一系列的操作
    db.commit()
except Exception as e :
    # 由于在执行事务的过程中，出现错误，所以回滚，恢复原来的状态，不执行操作
    db.rollback()
finally:
    cursor.close() #关闭游标
    db.close() #关闭一个数据库连接

```


###### 1.2 MongoDB

MongoDB是基于分布式文件存储的数据库，用于为Web应用提供可扩展的高性能数据存储解决方案。MongoDB属于非关系数据库。

MongoDB的基本概念是文档，集合，数据库。

MongoDB中的表通过collection替代，MongoDB中的行通过document替换。MongoDB中的列通过field替换。
MongoDB自动将_id字段设置为主键。

>文档：文档是MongoDB中数据的基本单元（即BSON）,类似于关系数据库中的行。文档具有唯一标识_id.数据库可以自动生成。文档以key/value形式。例如: {"name":"qiye","age":24}

文档具有如下三个特性：
1. 文档的键值对是有序的，顺序不同文档亦不同。
2. 文档的值可以是字符串，整数，数组以及文档等类型。
3. 文档的键是用双引号标识的字符串。以——开头的键是保留的，建议不要使用。
4. 文档区分大小写以及值类型


>集合：集合也就是一组文档，类似于数据表。集合没有固定的结构，可以在集合中插入不同格式和类型的数据（和关系表的差异）。

集合命名不能是空字符串，不能有'\0'字符，不能以system.开头，不要包含$。

>数据库：一个MongoDB可以创建多个数据库，默认数据库是db，数据库存储在data目录。MongoDB的单个实例可以容纳多个独立的数据库。

MongoDB的数据类型如下所示：
![a11](/images/数据类型.PNG)

>插入语法：db.集合.insert(JSON格式数据)

>查询语法：db.集合.find()
![a12](/images/条件查询.PNG)

对于多条件查询(and和or).
and通过逗号隔开。例如：db.集合.find({"key1":{条件1},"key2":{条件2}})

or通$or来实现。例如：db.集合.find({
    $or:[
        {key1:value1},{key2:value2}
    ]
})

>更新文档：MongoDB通过update(),save()方法来更新集合中的文档。update: db.集合.update(
    query,
    update，{
        upsert:boolean,
        multi: boolean,
        writeConcern:document
    }
)
其中：query为update的查询条件，update:update的对象和一些更新的操作符等（类似于set后面的内容），upsert（可选，如果不存在update记录，是否插入新文档）, multi（可选，是否更新全部查找出来的记录），writeConcern(可选，异常抛出级别)。
eg:
db.python.update(
    {'title','python'},{$set:{'title','python爬'}}
)
db.python.update(
    {'title','python'},{$set:{'title':'python爬'}},{multi:true}
)

对于save()方法，通过传入的文档替换已有的文档。db.集合.save({
    document{
        writeConcern:document
    }
})

>删除文档：MongoDB提供了remove()方法来删除文档。

db.集合.remove(
    query, #删除的文档的条件
    {
        justOne:boolean, # 如果设置为true，则只删除一个文档。
        writeConcern:document
    }
)

删除所有title等于mongodb的文档
db.python.remove({'title':'Mongodb'})
如果没有查询条件，就相当于删除所有的文档。
python操作mongodb代码如下所示：
```python
#1. 建立连接
#pymogo模块使用MongoClient对象描述一个数据库客户端，创建对象的主要参数是host和port
#如下三种方式创建
client = pymongo.MongoClient() # 连接默认的主机IP和端口
# client = pymongo.MongoClient('localhost',27017) #显示指定IP和端口
# client = pymongo.MongoClient('mongodb://localhost:27017/') #采用URL格式进行连接

#2. 获取数据库,通过MongoClient的属性方式来访问数据库
db = client.test #方式一
# db = client['pa-pers'] #方式二

#3. 获取一个集合
# collection = db.books #方式一
collection = db['test_one'] #方式二

#插入文档操作
data = {
    'author':'mike',
    'text':'My first book',
    'tags':["爬虫","python","网络"],
    'date': datetime.datetime.utcnow()
}
data_id = collection.insert_one(data) # 插入一条语句,返回数据的_id值,如果文件内没有_id值，则会自动添加到一文件里
# data_id = collection.insert_many(data) # 插入多条,数据以列表形式[{},{}]
print(data_id)
# 查询语句，find_one
print(collection.find_one({'author':'mike'}))
# 通过_id查询
print(collection.find_one({'_id': ObjectId('619c4dc19c281df292e7e0dd')}))
# 通过find可以查询多个符合条件的文档,并且可以在括号中加入限制条件，查询多个符合条件的文档
for book in collection.find():
    print(book)
# 统计符合条件的数目
print(collection.count_documents({'author':'mike'}))

# 修改文档
collection.update_one({'author':'mike'},{"$set":{"text":"python book"}})
#删除文档
collection.delete_one({'author':'mike'}) # 如果要删除多个，delete_many

```

###### 2. <a id="two"/>动态网站抓取

动态网页主要涉及到的技术是Ajax（Asynchoronous JavaScript and XML）和动态Html。

>Ajax技术用于网页的局部刷新，不必刷新整个页面，只需要调整局部内容，达到想要的效果，用户体验得到提升。
AJAX使用SOAP,XML或者支持JSON的WebService接口，在客户端利用JavaScript处理来自服务器的响应。

SOAP:简单对象访问协议是交换数据的一种协议规范，是一种轻量的、简单的、基于XML（标准通用标记语言下的一个子集）的协议，它被设计成在WEB上交换结构化的和固化的信息。

>动态html（DHTML，Dynamic Html）,由HTML+CSS+JavaScript。

如何从动态html页面爬取数据？有如下两种方法：
>1. 直接从JavaScript中采集加载的数据



###### 爬取影评信息
网页地址(www.mitime.com)

>2. 直接采集浏览器中已经加载的数据

对于直接加载渲染后的页面，可以通过PhantomJS,Selenium进行爬取。PhantomJS是基于WebKit的服务端JavaScript API，全面支持Web而无需浏览器支持，运行快，支持各种Web标准，DOM处理，CSS选择器，JSON，Cancas和SVG。PhantomJS可以用于网页自动化，网络检测，网页截屏，无界面测试等。可以把PhantomJS看成一个无界面的浏览器。

Selenium: Selenium是一个自动化测试工具，支持各种浏览器，Selenium支持浏览器驱动，可以对浏览器进行控制。

Selenium可以说是网页爬取的大杀器，可以直接模拟操作浏览器页面。下面介绍关于Selenium的使用方法。
>1. 安装配置

对于Selenium的安装配置教程可以自行百度。这里我采用的是Firefox,所以只需要两步，1. 下载selenium,通过pip指令就行。2. 下载驱动器geckodriver。通过如下代码即可使用：
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
# executable_path就是下载的geckodriver所在的文件路径
driver  = webdriver.Firefox(executable_path='E:\codeEverment\python\geckodriver\geckodriver-v0.14.0-win64\geckodriver.exe')
driver.get('http://www.baidu.com')
```

>2. 元素查找

selenium的元素定位方法如下图所示：
![a22](/images/定位方法.PNG)

>3. 页面操作

如何给表单填写内容？我们可以定位到表单元素，然后通过元素.send_keys填入内容。找到按钮或链接通过元素.click()模拟点击事件。如果要清除填入的内容，通过元素.clear()可以清除内容。

对于下拉选项，可以通过WebDriver提供的一个叫Select方法进行选择。

对于元素拖拽，首先要找到源元素和目的元素，然后用ActionChains类可以实现。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
driver  = webdriver.Firefox(executable_path='E:\codeEverment\python\geckodriver\geckodriver-v0.14.0-win64\geckodriver.exe')
driver.get('http://www.baidu.com')
print(driver.title)
assert u'百度' in driver.title
elem = driver.find_element_by_name('wd')
elem.clear()
elem.send_keys(u'网络爬虫') # 给控件填写内容
elem.send_keys(Keys.RETURN) #这里是回车按钮
time.sleep(3)
driver.close()
# 执行js代码
# 将页面拉到最低端
driver.execute_script("window.scrollTo(0,document.body.scrollHeight);")
```

显示等待的API：
![a24](/images/显示等待.PNG)


###### 3.<a id="three"></a> Web端协议分析

这里主要是关于网页登录POST分析，和验证码的解决方法。一般通过form表单填写账号密码，然后进行获取更多有效数据。

>通过POST请求登录

一般我们都会通过构造表单数据，进行post请求。但是通常我们不仅仅提交的是账号，密码，还需要分析页面的具体提交数据，’
然后分析对应的数据，进行构建

```python
"""
这里主要演示了，如何通过session构建表单，然后进行表单提交。
"""
# coding: utf-8
import re
import requests

def get_xsrf(session):
    '''
    _xsrf是一个动态参数从网页中获取
    :param Session:
    :return:
    '''
    index_url = 'http://www.baidu.com'
    index_page = session.get(index_url)
    html = index_page.text
    return ""

session = requests.session()
_xsrf = get_xsrf(session)
post_url = 'htttp://www.zhihu.com/login/phone_num'
postdata = { # 构造post参数，这需要分析登录过程传递的参数，然后进行构建，通常不仅仅包含
    #账号密码选项，还有许多附加项
    '_xsrf':_xsrf,
    'password':'xxxxxxx',
    'phone_no':'xxxxxxx',
    'remember_me':'true'
}
login_page = session.post(post_url,data=postdata)
login_code = login_page.text
print(login_page.status_code)
print(login_code)
```
> 加密数据分析

通常在网页传输的数据都会进行数据加密，然后添加一系列附加的参数到POST请求中，而且还有验证码。
所以这时候需要对网页进行分析。

>1. 监听网络数据，分析传送参数

通常这一过程，我们会反复登录，然后记录传送的参数以及cookie中值的变化。

>2. 分析参数的获取方式

当我们完成传送参数的分析过程的时候，就需要进一步解析参数的生成方法，然后在程序中进行生成参数，然后构建data,进行post请求。通过我们获取参数的方法有两种。一是根据网络请求分析，查看是否有参数通过API进行获取，例如有的网页的Token，Public_key等是通过API进行获取，通常我们可以在网络请求中查看。二是根据JS文件获取生成方式，通常通过API获取参数还是需要通过JS获取API需要传递的参数，通常我们从JS中获取参数是通过网页文件中搜索参数名，然后从众多包含参数名的JS文件中，分析出参数在哪个JS文件中生成，并提取出相应的生成方法。

实例代码如下(以百度网盘为例)：
```python
"""
分析百度网盘的登录：
https://passport.baidu.com/v2/api/?login  #账号验证链接

staticpage: https://pan.baidu.com/res/static/thirdparty/pass_v3_jump.html

charset: UTF-8
tpl: netdisk
subpro: netdisk_web
apiver: v3
codestring:
safeflg: 0
u: https://pan.baidu.com/disk/home
isPhone:
quick_user: 0
logintype: basicLogin
logLoginType: pc_loginBasic
idc:
loginmerge: true
crypttype: 12
mkey:
countrycode:
fp_uid:
fp_info:
loginversion: v4
supportdv: 1
mem_pass: on
detect: 1
alg: v3
tt: 时间戳

POST1：
gid: A7A60F7-2233-48D5-9CD2-B51EB721ADDF

username: 2891112980@qq.com
password: OcYb7XL/QONk5nADRh+a8cPYNbCvxlMxmncxXomf94YxHenzYWxVJUnM60bNhLt6mYmL+oXC4QvAVw9ujoREPP/YrFDvJNI87jgBwE6doPvn8/9+H8gATohKY68SaIoF+G9tMh/9VIOk5OuwrmpwzvZNQAY/V7gPAjCGmTKWPu4=
rsakey: F6qaltAhSGSTrr3LYZoF0fDJkX11XTI6
ppui_logintime: 33785

# ds通过viewlog可以获取
#https://passport.baidu.com/viewlog?callback=jQuery11020116633250707733_1637907449065&ak=1e3f2dd1c81f2075171a547893391274&as=b052f9ad&fs=aJ2zH%2BxmPIQryyPrpIPqtXmH72tk2XARV%2F09o05kx%2FNTGdkoP%2Bzms8NSTm0jdEzP%2BhyPK1KdjXobjyUVxQ9OAuiZZmmEtQpubsfgTL8NYZWy5Sj0%2Bp5a20bTVZFZ70YbQcqZadFjrJ3ompkIg8E%2BYKbzQiEl0HRdRM6o429%2BhEeW3SwAMvEl04XHGbic4703ZlUREQzEeQ5v4b840qnfHziBVVGS9jihhHliV1iPFo6%2Brdl0G1YGREEm%2FvCvr3H15pDBP346ucrwUvqF23DN2WxkjdG68I%2FlW8rMEoTmx2sRpgiVDW4FwR7GYgOinomA7XKjKIV44rACzwS5Cd53wrP%2FCxFhAH91X%2BlFr%2F5Nye6OxX7u7B5FeVe4OnMt%2BrE0cYUqWLzqAls1loOLNoKaGNKpxIjOup%2BA%2B4sulrz1K89itYMKw7MQeNERfCI32hPmJ%2FSAJz36rVBtWO1aMUq66Vu7D8QrNIQ6Ko2L1vq3UQGqO0PRKSObWKZ4leHIf7wzJAMMD2K5cIVq1iLD2k7u5RmP8TI8EYsbxA0V4X8rwdAjAnFOgE4ch%2ByNyJfCeB0FPWs6VPPb9Opd37f6ofMcxWxkjdG68I%2FlW8rMEoTmx2smMDXKV2I%2F%2FSoxAYPYNoZT7XKjKIV44rACzwS5Cd53wuq17oB3hRDHI%2F5FD8ApIUyOxX7u7B5FeVe4OnMt%2BrE0pO2CUHf3wzY2O1nRsJt429KpxIjOup%2BA%2B4sulrz1K8%2Fgsgko9%2FybfRXZevgkJ2FTJ%2FSAJz36rVBtWO1aMUq66TWu3mrKMIRru7O6yI6%2B2TDqQXhdGcWeeWbzEILv75p3aopf9L%2Fmg3TG32fE46ACdLe9EtBCfLdqwpsieMHokZYjAnFOgE4ch%2ByNyJfCeB0FTmTqlzbiE0dSKyXoaNVBrXPmvUO8LF0TrlU912rIKlNqHIWEzcVbD5iGbnqKODdJh2NjhpbaJhRLDM6hXKbFY1SOq8SbKq03JqsbsJTIds6txmXY3cvIjmhaLph06iufE2oUGZsm4FwM9L7uTYsCXEhwGeYx1aILjYgChhgGUkTgVEgyn9h0l5p8hDZ7MKqOqYtXA1fmKQ0NEKMmzZd8eYFcgQPNQxncOm63JKnGItlgXNqFT31JBhv5lC7ybrYm5BE4sZtiEjgFwbXnaseNyqG1B2V5SJLYgUj69M46RJ09vxsCO54v7u3N3p1RjZlHwgU0OC16voeLsV3dm54Q2686dcWl%2BMBcvN6nLD3oN4wZ0sE582gll4wCKKEU3HrI&tk=4006KvNubQMyUKPA8V02ndLZ4ZdO%2BQjmAT7GxVnjL4Rwk6ewi356PNOQKePl0ypYx1c7Dpi5RQawPXeIhjtrwJmhbK%2B7%2FP98piTEVpjpMwlZ0Es%3D&_=1637907449068
ds: II4Fg+RtgS2K3+RJnfEduZufjjdwD4QhYQhW04uf/VRDc+fFv1h7ViPEKzfVS15/YEt/aPddzytkFKhGf1Uo/XJm6pthogRlVaD8YeC68vvNq44fKPFACQkFXnHXZ8IGNQ8yrxON2Up0DT3jOOlmAxcKlI5PS3IaOp/jshcyHK+rJaRTTxZ4IXSCKZA+yQAEcAjLtuNMmB1RjOGr5SoG6fPAF+/5zUe4JogUCHg4IFDAaLsFP9Nev6IkG0mnTXJlwdIJSe6ZaTCOplnmGC0mouUychSC9/KIyV2TJiN7kJCUQKyqbp3VAbpz+e9xfaFeqtyPv+wOE1buN6QpyyJ8NvJ4edqWVwch1ebshP8gVPM5tNlcd/YWPT2n6VFtxn5ejme5wI1wDnpCBtbXY3C0+hIJaqJ9M94QNQvK4Swn6kGkrLKCgBbkaOfOdnfdMXmUrIOkIn8vCcbx8htOM9d5kNn8DmHNt4rtCcXphfQSNvf4didhsiAzwUU0ZQ1G53CTs7xMe9NqmzIRYgs/OVr4WIqK7r5cRjuBtcWtZAGKqrOWEO9QokQM225kP47RB25Vap6P4obUOTl88p4eNEGefEhqfDr7ZBqZ8p65Ht76ndP1+q8N6dpCh7CR5hv96mkIw1jfYWlQaZvgjNF/3uxqDRSnLLf9LGJ6O2qdR6IdN3XHB5DGmpdnOiu9PWA6J1qSAfeZOiPBXIsoDM+5yTqMwthRIWZK7zJ4B2vfQrqcqYgW3C9w6KglvuIEaNBZBnjrA/QuA1PU8ZMCijyrRN+3jnS1VX6lBoxVFG9oQnRLdyTaNFQbcq1o6zdcRh/zCQMcBSvZK8tKboXFXDK6oN0zYaSBdx3rJVzzEdkAL1gA8t0nqYsFbdQIQjeFi5RjIrjvDknLZ82xw0zoid7DyZ7qYTbIGxjdYK0QdKXH/u6GM446LCkG2wnSgTEHtBWSJIacz3haMH0277hPZeyGtjstPhqrm3v6wFYqSqa0feecGEGXUlFkq1vfEb/q6CQUEoRzZREiW6pEVnFUIzNG/8mqfISDRwYFqTOjNn5HAEho8qf0G9G3zi6FZgGQ8Jq4HJ9F6I4QEagJYde/A5KmPqZNgSxJc/j/vXzQ7I565b/1F6A=

# tk 通过viewlog可以获取
tk: 3806qsn4na7kF+g6FX58o+wfHp0rkicKoGxH7zGNq7/Nc12RpVbL51wDca2EsymyAXLwDS2oDrrP90vY/QvHD4ykyS18NmHsVx9q95E2iL5h0T4=

dv: tk0.53300068179592071637896306454@ssr0MBvDUOnmgCECw2C3ptJldEClhFtpdFQJtVI3wWM6COtyEyvJzgnkEOnmgCECw2C3ptJldEClhFtpdFQJtVI3wWM6COtyUitizgnkCOnmgCECw2C3ptJldEClhFtpdFQJtVI3wWM6COvkrgn2zwtHzzupohD3Q9EClFJlhDCpX~JldlI-AHDRpKG9zjny7gFk3iukqOArpvol8hDAdFCptEJyoFJiAyGJ8vB6lVukU-nSAXtr0DBvD7gukn~t9zyvkrlupohD3Q9EClFJlhDCpX~JldyQ68KLJoXvDUzukn-vmz~tkIlupohD3Q9EClFJlhDCpX~JldyQ68KLJoXnDq-nHzytDIOtDrgnmgCECw2C3ptJldEClhFtpdFIiA1M6V~FkrznkrOnyB~ukC~nSrOArpvol8hDAdFCptEJyoFJitlBRleQ2zwtSIOny7yukClvDUOArpvol8hDAdFCptEJyoFJitlBRleQpQHBJhzGJ8XBggLzgFHhpZWhFrhn9zHuk3-CrYQ5OzuSCynyqznkBjnDIwtD3HnkIgtSnivk3-nyq-tkC~hrYL2o~I2nbuHdzB6jNBRpeG2CNB-dKuHglMRoVGRVNG6E_ArnnmzzukrHvkqOtyr-ukrlnyBOvkB~ukrlnyBOnDCyt1zjnSE_

fuid: FOCoIC3q5fKa8fgJnwzbE67EJ49BGJeplOzf+4l4EOvDuu2RXBRv6R3A1AZMa49I27C0gDDLrJyxcIIeAeEhD8JYsoLTpBiaCXhLqvzbzmvy3SeAW17tKgNq/Xx+RgOdb8TWCFe62MVrDTY6lMf2GrfqL8c87KLF2qFER3obJGn1imUD9LtLDAxtUQVt00ywrFuY99+6ElzcETNlw/C1xZYD5SLH8W4d36DzPMGV4JvslIL5jj8EphieYalb6sMBbKVYW5I2LDDIlDR71IUk1mpk3Y5oUBZH/dZ3rKHJ0IrTnF2Wvu65NpvARErKLH28zZNnk8BWIbmbBo8AkexVQD4TDTFi/kJX2nM5fjwUAQ7erld8wjGrZonj8gE2rhKeVRKCfkBUbih20ajKSj+uMVvFor2HqteRD2yQbHfKX9I9NLYjYxH1YiKtJ/LZAt+jDVAzZK7r2I/XoTgOvpK/GRXi7OMMoCL+ptOvZYbHZs2EmNX6yJZWrVbkhpE/nUJFiBoWKq2H4RxmaiTcdrlpoJQCEBGWNtTsmA4KXyjd88UZ+jjahyvNzJARDVgTFkgMHmv/tVUjVExnyBvjSrtrPoNvLDs45y1J1BPSrpDADEz2WH0+/bGxWLp7WNEcuhl9zrEQCVXVz1ypT0UIooITdod7rWTX1LABi9llmt7bm09SsvyWN9Io4FIQt/daeMGulvDHd3RTujFKqjbyri7OLazNV00oTLvGOs+5nIyBMdEkYaTFoTKPDDVtdCO/a2IhGao+HBbcA3Sf1GWmuogDLSCOxH8nX4hJuc0/kKmVlWYouh6qHeKBM7Z7nT+yF0MXReV/blmoigtQ9A6GiwCQZffiSKUOp1VsS+Ly9+Iupn8y+IS13BgMJv25Rgmo9qqpVhLAbfjO5WTzIDI7m0pWOYtgRQxRCPxURvzJkLg7aW9Jm4l4Bb/STxNSv23Ru6jHCU7CX0YAw0dSweJL1vvynMwJmCcF9FlItQPQ5C9mG3VH8xRyN02ywrifxP8IF10Hg3b+Vi67h2TKPZAaOJzerzLANGXGTGd0Djy9B9kQWIoYRDDc4ujl2xJR5AN3q8OeLV68X8zvK+Yv4VQDA+ZZkVu82rr07bPfAFb6iDElQPL0gOE5uGejgPswFkbgH6k2WEZafSOKRDnPOYABKWeLHdXZe2bA0DO+1FlR8qb+PmiFcZVouo5DCvlQhjRhvpItjUh2/yNfyHjnbZ1A/hRRI7BFVDtu1KaJzjx51nTN1+yRNkC71fasq+HI3zFAuiUsfI5v5JSd2kRNZE7s6bQrA5yMI31SfUDgxDrsd6lPtUU=

traceid: F3DFF901

callback: parent.bd__pcbs__7pdpae

time: 1637896340

sig: NHhDWmhlSkpXWlpoVlpTb1hZajZwaXhyYlRtSmVUM1lCRzBXa1dhOFRPWmtMZnRHd2Vmd29rdTZlUzhHKzl6eA==

elapsed: 2

shaOne: 00b653720afbeae6ff66615907b0e59e85a35757

rinfo: {"fuid":"1d5264f920930df8d682b5cbac99c9e5"}


POST2：

#通过gid获取token
#https://passport.baidu.com/v2/api/?getapi&token=&tpl=netdisk&subpro=netdisk_web&apiver=v3&tt=1637908334880&class=login&gid=4D970C9-672F-4002-BCD8-D71F629D9C8A&loginversion=v4&logintype=basicLogin&traceid=&time=1637908335&alg=v3&sig=ZHpJazJocTlTeHNMNG5LUlZjdE8yTXNLMUNjMGk3QmVTUzdDajF2Y2Q0M3pjSDBNL1k4ZHN1VHJzY0g0QWdrbw%3D%3D&elapsed=10&shaOne=00c2bc34cb293b92b355a01af5b33dcb0c7c0e19&callback=bd__cbs__elghk8
token: 134400c212148a1ed97cd3e14a64dec4
#
#通过loginv4_tangram_c32acce.js可以 看到gid的生成方法
# 通过F12查找可以得到gid的生成方法，如下所示
# "xxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(/[xy]/g, function(e) {
#                     var t = 16 * Math.random() | 0,
#                         n = "x" === e ? t : 3 & t | 8;
#                     return n.toString(16)
#                 }).toUpperCase()
gid: 14893FE-D2FB-4BBC-9D6F-353A905BD43D
username: 18370446979

#可以通过rsa加密
#通过F12找到password的生成方法
# password: function() { e._getRSA(function(t) { e.RSA = t.RSA, e.rsakey = t.rsakey }) }
password: qnmL7K9NK7pvaHhlmV/FVFjJEJsUUCmPs+aRr0jWLPSe9y033E9268hTdWCZoGGSz3fV2Q5BN7szAXd8vpH/I4BzNI0Jd72MXb1pkNEXeUnuxEy7xDPGshCWThTIID2PcCydW0dWrs/GrVjTsiwKFrOuig8uWSeJ5F5RVS2ioMc=


rsakey: kC02XvnT7I6uMux9cYbgVbsVqcat7gCK
#在loginv4_tangram_c32acce.js文件可以找到ppui的生成方法，没啥影响，可以用相同的ppui


ppui_logintime: 59338

ds: DJv1I2ZMYFt4VNJGrEieRzX+fTxEGqp5KEKwP82Eqs0OwC/0nDO0AaGbSnOx4gLBgEwFUlmknWtM8w70bR6651jBTWR3RrN6M8athvLv0qjIh/uqJXSiudqxzvNbI8JrbRQi/L6z0yapH8OzFzgtjkCbQnKIIseJ5YN7pXQNzzfsLmwRSboSxSIMh7H+mNWlm7Jv6bipWb2Ef+mULPz+HJcsYAuYX7ZSRej5qKjg4LilTqk77vHywOzTdYX43QwjBa95tTsSQl3UdoyTyTSoP3+MI39NMnpfvc31ubL1aYBzIwtGTKBGQ6r9IMPO1/apOChCY8Ukvh5YsbuEPVZhHNrWGbwQxmPZGfwnHLxk+a4fe8Swz2GQYZhJWXhGX1zomphghvSfI0Xr0C3ofNTFL5YQ71CiRAzbbmQ/jtEHblVptfsMcO06PhUuRCioIIrvPK3f5pSnKweaOreL/stiDNRDmTc/TFc/zLbPufGyD5xEZm3ptEBPg9JO1kZVGX9hEjqGlJPTvnmgyiTZbqqYMGuU+teFEfSezySS7QbzHrNdJWklVjPbX4MaXF0vQwV3QvJ2BYaIJXbUbmi2N+MCb6uuhcCUzRXcvQcCFN+nCi8JV0rZbi8qRA6qogqITSmcoX+C2ebWRCqd1Bta5oZQDFnlNY8P1//P9TI67UuT8f3rpPwKHtCV94QXnYOPyXhxoFShURm0wEXyWfe6k74eCKJzF9WHKgqjvWNBU4oxh51ptfsMcO06PhUuRCioIIrvRnbBncAl/l2oYpW1Tl+s9vX6rw3p2kKHsJHmG/3qaQjDWN9haVBpm+CM0X/e7GoNFKcst/0sYno7ap1Hoh03dccHkMaal2c6K709YDonWpJOuWUh5Btk18pp2ofQaQue++3sNK8B3l22xvtu69xAcBbcL3DoqCW+4gRo0FkGeOsD9C4DU9TxkwKKPKtE37eOdLVVfqUGjFUUb2hCdEt3JNo0VBtyrWjrN1xGH/MJAxwFK9kry0puhcVcMrqg3TNhpIF3HeslXPMR2QAvWADy3SepiwVt1AhCN4WLlGMiuO/EHjpcfKHlM/uGqUpTxlzFg6wRAvrcujI33KeY139x4zosKQbbCdKBMQe0FZIkhpybzWnTPAJbZ2HlksG4LzZfZZxOv3Rz8OzebBbGZY2cKHzOIWo49PxA+Q/fgOWSMCg+0HmDAKY5njRPYRTBGiWmrU3+ErtPH77j3x0d0oaItNhgp6PrWJ92dgGBZTxabg4LrS+iGvesD1QIbG1EpOUu6EGco5bp1s9+m48R0KveFA==

tk: 5886+r0RsngU7eH85qinjvKq/Ov8cTqi3758E9sFclzWzRrsTzYvBtjqRuohenHcu9EkNtVwd1ZSl1bUdta7LJyUF+N0t3lElPs4Qk2Y4E3v6mM=

dv: tk0.076573147928699071637897079952@ssm0MCo4JL8mcC6CyrCStv9Xw6CXhUvtwUR9v3JSyEOGCLvj6ZvT-c8k6L8mcC6CyrCStv9Xw6CXhUvtwUR9v3JSyEOGCLvjKeo9-yoH--0tDh4SRH6CXU9Xh4CtlZ9XwXJiAe4atYFH-~ok6cUk2-8e--0tDh4SRH6CXU9Xh4CtlZ9XwXJiAe4atYFH-x8k6yUkSi0kqLA2toDXph4AwUCtv69jDU9~AjF9poKGX30k7eo4hltm0otok7-0k8~ve-Xv12e0tDh4SRH6CXU9Xh4CtlZ9XwjRGpYI9Dlo46e0k8Xve-Xvj8Z0tDh4SRH6CXU9Xh4CtlZ9XwjRGpYI9Dlo4KX0k5x8e-Xok6y0tDh4SRH6CXU9Xh4CtlZ9XwaO~pYUkSi8e-e8j8Lv16e8mcC6CyrCStv9Xw6CXhUvtwUR9v3JSyEOGAlokKe0kSX0k2e812e0r-_CllI~lI~vtMEhImi8H-e0kSiBmpRsL-01q~v1C~8j2ZvjSeokKyo4q~84Kjvj7yvjq~o4SX87__imVIrDZJr8P0ew-KGxgKatWFrCgKiwY0ecXOaD3Fa3gFG6_zmp8m--0k2ev18Lvj2i0k2X8jKLokKZ0k2X8jKL84CjvB-x816_

fuid: FOCoIC3q5fKa8fgJnwzbE67EJ49BGJeplOzf+4l4EOvDuu2RXBRv6R3A1AZMa49I27C0gDDLrJyxcIIeAeEhD8JYsoLTpBiaCXhLqvzbzmvy3SeAW17tKgNq/Xx+RgOdb8TWCFe62MVrDTY6lMf2GrfqL8c87KLF2qFER3obJGn1imUD9LtLDAxtUQVt00ywrFuY99+6ElzcETNlw/C1xZYD5SLH8W4d36DzPMGV4JvslIL5jj8EphieYalb6sMBbKVYW5I2LDDIlDR71IUk1mpk3Y5oUBZH/dZ3rKHJ0IrTnF2Wvu65NpvARErKLH28zZNnk8BWIbmbBo8AkexVQD4TDTFi/kJX2nM5fjwUAQ7erld8wjGrZonj8gE2rhKeVRKCfkBUbih20ajKSj+uMVvFor2HqteRD2yQbHfKX9I9NLYjYxH1YiKtJ/LZAt+jDVAzZK7r2I/XoTgOvpK/GRXi7OMMoCL+ptOvZYbHZs2EmNX6yJZWrVbkhpE/nUJFiBoWKq2H4RxmaiTcdrlpoJQCEBGWNtTsmA4KXyjd88UZ+jjahyvNzJARDVgTFkgMHmv/tVUjVExnyBvjSrtrPoNvLDs45y1J1BPSrpDADEz2WH0+/bGxWLp7WNEcuhl9zrEQCVXVz1ypT0UIooITdod7rWTX1LABi9llmt7bm09SsvyWN9Io4FIQt/daeMGulvDHd3RTujFKqjbyri7OLazNV00oTLvGOs+5nIyBMdEkYaTFoTKPDDVtdCO/a2IhGao+HBbcA3Sf1GWmuogDLSCOxH8nX4hJuc0/kKmVlWYouh6qHeKBM7Z7nT+yF0MXReV/blmoigtQ9A6GiwCQZffiSKUOp1VsS+Ly9+Iupn8y+IS13BgMJv25Rgmo9qqpVhLAbfjO5WTzIDI7m0pWOYtgRQxRCPxURvzJkLg7aW9Jm4l4Bb/STxNSv23Ru6jHCU7CX0YAw0dSweJL1vvynMwJmCcF9FlItQPQ5C9mG3VH8xRyN02ywrifxP8IF10Hg3b+Vi67h2TKPZAaOJzerzLANGXGTGd0Djy9B9kQWIoYRDDc4ujl2xJR5AN3q8OeLV68X8zvK+Yv4VQDA+ZZkVu82rr07bPfAFb6iDElQPL0gOE5uGejgPswFkbgH6k2WEZafSOKRDnPOYABKWeLHdXZe2bA0DO+1FlR8qb+PmiFcZVouo5DCvlQhjRhvpItjUh2/yNfyHjnbZ1A/hRRI7BFVDtu1KaJzjx51nTN1+yRNkC71fasq+HI3zFAuiUsfI5v5JSd2kRNZE7s6bQrA5yMI31SfUDgxDrsd6lPtUU=
traceid: B67AC501

#baidu.phoenix._setIconsStatus
#只是标识，不参与校验
"bd__cbs__" + Math.floor(Math.random() * 2147483648).toString(36)
callback: parent.bd__pcbs__kk48fu

time: 1637897139

alg: v3

sig: cFJLQ0ZzMWo2VmFaaUVXMFJQbk94QUhuUWFLeEVkNFllM2tjOVhFQ1hOcEg2UC9PUUltR2l2SnZOcFpQNi9FTw==

elapsed: 5

shaOne: 00a72749f19999931e2653a554991baf3f874f7b

rinfo: {"fuid":"1d5264f920930df8d682b5cbac99c9e5"}

"""

"""
    一般我们对登录附加信息的获取，都会通过多个POST请求进行比较，得出不变的数据和变动的数据，
    然后进行分析处理。
    1. 获取表单提交参数
    2. 确定参数获取方式->可以通过F12的内容查找，然后搜索指定参数名，找到对应的代码，分析数据如何生成的
    3. 构建post请求，进行登录
"""
import base64
import json
import re
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5
import PyV8 #pyv8引擎，可以直接执行js代码，不用转换为python语言
from urllib import quote
import requests
import time
if __name__ == '__main__':
    s = requests.Session()
    s.get('http://yun.baidu.com')
    js = '''
    function callback(){
        return "bd__cbs__" + Math.floor(Math.random() * 2147483648).toString(36) ; 
    }
    function gid(){
    return "xxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(/[xy]/g, function(e) {
                    var t = 16 * Math.random() | 0,
                        n = "x" === e ? t : 3 & t | 8;
                    return n.toString(16)
                }).toUpperCase()
    }
    '''
    ctxt = PyV8.JsContext()
    ctxt.enter()
    ctxt.eval(js)
    ### 获取gid
    gid = ctxt.locals.gid()
    ### 获取callback
    callback = ctxt.locals.callback()
    ### 获取token,根据需要的参数，传入相应参数，返回对应的token
    tokenUrl = "#https://passport.baidu.com/v2/api/?getapi&token=&tpl=netdisk&subpro=netdisk_web&apiver=v3&tt=1637908334880&class=login&gid=4D970C9-672F-4002-BCD8-D71F629D9C8A&loginversion=v4&logintype=basicLogin&traceid=&time=1637908335&alg=v3&sig=ZHpJazJocTlTeHNMNG5LUlZjdE8yTXNLMUNjMGk3QmVTUzdDajF2Y2Q0M3pjSDBNL1k4ZHN1VHJzY0g0QWdrbw%3D%3D&elapsed=10&shaOne=00c2bc34cb293b92b355a01af5b33dcb0c7c0e19&callback=bd__cbs__elghk8"
    token_response = s.get(tokenUrl)
    pattern = re.compile(r'"token"\s*:\s*"(\w+)"')
    match = pattern.search(token_response.text)
    if match:
        token = match.group(1)
    else:
        raise Exception
    #### 获取callback
    callback2 = ctxt.locals.callback()
    #### 获取rsakey和pubkey
    rsaUrl = ""
    response = s.get(rsaUrl)
    key = response.text #匹配搜索key
    pubkey = response.text
    ####加密password
    password = '' #自己的密码
    pubkey = pubkey.replace('\\n','\n').replace('\\','')#处理pubkey
    rsakey = RSA.importKey(pubkey) #百度网盘通过的是RSA加密
    cipher = PKCS1_v1_5.new(rsakey)
    password = base64.b64decode(cipher.encrypt(password)) #加密
    ###获取callback
    callback3 = ctxt.locals.callback()
    data = {
        "charset": 'UTF - 8',
        "tpl": "netdisk",
        "subpro": "netdisk_web",
        "apiver": "v3",
        "codestring":"",
            "safeflg": 0,
        "u": 'https: // pan.baidu.com / disk / home',
        "isPhone":"",
        "quick_user": 0,
        "logintype": "basicLogin",
        "logLoginType": "pc_loginBasic",
        "idc":"",
        "loginmerge": True,
        "crypttype": 12,
        "mkey":"",
        "countrycode":"",
        "fp_uid":"",
        "fp_info":"",
        "loginversion": "v4",
        "supportdv": 1,
        "mem_pass": "on",
        "detect": 1,
        "alg": "v3",
        "gid":gid,
        'callback':callback3,
        'token':token,
        'password':password
    }
    #构建好了表单数据，然后就可以进行页面登录了
    post1_response = s.post('https://passport.baidu.com/v2/api/?login',data=data)
    #如果页面还有其它信息需要验证，在后面进行处理即可
    #比如需要滑块验证等

```

>验证码问题

从上面的POST表单构建引出的一个新的问题，就是关于验证码的问题，通常验证码有三种形式，一种是在post表单中的图片的验证码，一种是根据图片点击相应的区域，一种是滑块验证。

>>IP代理

同一个IP频繁访问网页，可能会导致IP被封禁或者需要输入验证码验证是否是真人。解决的方法可以通过加大爬虫的延时，让网页觉得你就是真人在浏览网页，毕竟通过爬虫访问网页的频率过快，容易被识别，然而通过减慢爬虫时延带来的另一个问题就是效率的降低。更好的一种做法的更换IP进行访问。

IP代理的方式可以通过设置request的proxies参数进行设置。
```python
 # 代理设置，使用代理Proxy,可以为任意请求方法通过设置proxies参数来配置单个请求
    proxies = {
        "http": "http://0.10.1.10:3128",
        "https": "http://10.10.1.10:1080",
        # "http":"http://user:pass@10.10.1.10:3128" #这是代理中身份认证的用户名和密码，来设置代理
    }
    requests.get("www.baidu.com", proxies=proxies)  # 设置代理ip
```
我们已经知道通过request可以设置代理IP，那么如何获取代理IP？有如下几种方式：
>1. VPN
国内和国外很多厂商提供VPN服务，可以分配不同的网络线路，并可以自动更换IP，实时性很高，速度很快。但是价格一般较高，适合商用。

>2. IP代理池
一些厂商将很多IP做成代理池，提供API接口，允许用户使用程序调用。稳定的IP代理池费用也是较高的，所以不适合个人学习使用。

>现在很多领域都需要用到代理IP，用到的领域越来越广，如爬虫、投票、抢购等等，那么具体代理ip有什么用?能做些什么呢?
代理ip具体的作用：
1.可以冲破原始IP的访问限制，可以访问国外站点。
2.可以访问一些单位或团体的内部资源，如某大学FTP(但是前提是，该代理地址在该资源允许访问的范围时可以进行访问)，使用教育网内地址段免费代理服务器，就可以用于对教育 网开放的各类FTP下载上传，以及各类资料查询共享等服务。
3.可以突破中国电信的IP封锁：中国电信用户有很多网站是被限制访问的，这种限制是人为的，所以不能访问时可以换一个国外的代理服务器进行访问。
4.可以提高访问速度：通常代理服务器都设置一个较大的硬盘缓冲区,当外界有信息通过的时候,将其保存到缓冲区内,当其他用户也访问相同信息的时候， 可以直接将缓冲区中的信息传给用户，提高访问的速度。
5.隐藏真实IP：上网者也可以通过这种方法隐藏自己的IP，免受攻击。
代理ip又分透明代理，高匿代理，S5代理等等
举例：
1.突破访问限制如：代理到国外的IP即可访问受限制的Google!
2.用代理IP做网络爬虫!
3.刷访问量，刷点击，优化网站的流量!
4.做百度SEO,提升网站的排名!
5.刷网络投票!
6.批量挂机!如：QQ或YY!
7.批量注册，如：注册邮箱，论坛账号之类的!

>3. ADSL宽带拨号
也就是拨号上网。ADSL每次断开重新连接时会分配新的IP地址，爬虫可以利用这个原理更换IP。但是更换IP需要断开重连，所以效率并不高，适用于实时性不高的场景。
Windows下通过rasdial操作可以进行拨号，Python通过os.system来执行命令行语句，进行拨号操作。代码如下所示：
```python
''''
作为示例进行演示，如何通过ADSL进行拨号和断开
'''
import os
import time

g_adsl_account = {
    "name":"adsl",
    "username":"xxxxxxxx",
    "password":"xxxxxxxx"
}

class Adsl(object):

    def __init__(self):
        self.name = g_adsl_account['name']
        self.username = g_adsl_account['username']
        self.password = g_adsl_account['password']
    #修改asdl
    def set_adsl(self,account):
        self.name = account['name']
        self.username = account['username']
        self.password = account['password']
    '''
        拨号连接
    '''
    def connect(self):
        cmd_str = "rasdlal %s %s %s"%(self.name,self.username,self.password)
        os.system(cmd_str)#调用windows的控制台，通过rasdlal进行拨号
        time.sleep(5)
    '''
        断开连接
    '''
    def disconnect(self):
        cmd_str = "rasdial %s / disconnect"%self.name
        os.system(cmd_str)
        time.sleep(5)
    '''
        重新拨号
    '''
    def reconnect(self):
        self.disconnect()
        self.connect()
```

如果我们想要找到一些免费的IP代理，可以在各个网站寻找免费的IP，然后对IP去重，检测代理有效性等操作，存放到数据库中，通过接口获取免费的IP，构建自己的IP池。

>>图片验证码识别

我们在浏览器登录的时候，常常需要输入图片验证码，这里给出几种方法解决图片验证码的问题。
>1. Cookie绕过登录，我们在浏览网站的时候，常常会保留登录信息，下次登录就不要进行登录，通过Cookie即可登录。所以我们可以获取登录后的Cookie，然后设置Cookie，绕过登录。可以获取多个Cookie，构建Cookie池，绕过登录验证。示例代码如下。
```python
"""
    通过已经登陆过的Cookie进行通过Cookie进行登录。
    我们可以在模拟登录成功后，将session的值保存到本地，后续可以通过cookie进行登录。
"""
import pickle
import requests
from requests.cookies import cookiejar_from_dict
def save_session(session):
    #将session写入文件,session.txt
    with open('session.txt','wb') as f:
        pickle.dump(session.headers,f) #写入头
        pickle.dump(session.cookes.get_dict(),f)#写入Cookie值
        print('将sesssion写入文件:session.txt')

def load_session():
    #加载session
    with open('session.txt','rb') as f:
        headers = pickle.load(f)
        cookies = pickle.load(f)
        return headers,cookies
session  = requests.Session()
session.headers = 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36'
dicts = dict()
dicts['BDORZ'] = 'FFFB88E999055A3F8A630C64834BD6D0'
dicts['PSINO'] = '7'
dicts['BDRCVFR[hpwYTbhBfiY]'] = '9xWipS8B-FspA7EnHc1QhPEUf'
dicts['BDUSS_BFESS'] = 'XFOcmlGQVhEdUZjSnJiWmJmbVgza35YV0RvZktEUEdkUXgyYkJ4S01uOER0OHRoSVFBQUFBJCQAAAAAAAAAAAEAAADprZrnsrvLxrnp1MYAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMqpGEDKqRhW'
dicts['BDUSS'] = 'XFOcmlGQVhEdUZjSnJiWmJmbVgza35YV0RvZktEUEdkUXgyYkJ4S01uOER0OHRoSVFBQUFBJCQAAAAAAAAAAAEAAADprZrnsrvLxrnp1MYAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMqpGEDKqRhW'
dicts['BAIDUID_BFESS'] = 'E8BBAF114F9097C4849493D68A677EC2:FG=1'
dicts['HMACCOUNT_BFESS'] = '13391551711E4651'
dicts['MCITY'] = '-%3A'
dicts['BAIDUID'] = 'A02BD18D5032DA5E3DA0339468C7AE42:FG=1'
dicts['__yjs_duid'] = '1_2b5acc51d9eef8efeada14364c2710b71633588836061'
dicts['c_dl_fref'] = 'https://blog.csdn.net/kaida1234/article/details/89553115'
dicts['Hm_lpvt_6bcd52f51e9b3dce32bec4a3997715ac'] = '1638500494'
dicts['c_page_id'] = 'default'
dicts['c_first_page'] = 'https%3A//i.csdn.net/'
dicts['log_Id_pv'] = '1556'
dicts['delPer'] = '0'
dicts['TY_SESSION_ID'] = '1e66f339-636d-4633-845e-6afde0a1d0f8'
dicts['PSTM'] = '1633401440'
dicts['dc_sid'] = '2cee0d889d6dbf50e0ba642d35372f04'
dicts['dc_session_id'] = '10_1638499930521.206489'
dicts['log_Id_click'] = '664'
dicts['c_segment'] = '12'
dicts['c_first_ref'] = 'cn.bing.com'
dicts['ab_sr'] = '1.0.1_MzcxMWQ4NzQzNGYyZWRmZmJhNTA2NTdiNDY5Yjc2M2I3NTg2MThlZjg4OGRhZWVjNGExYmEwYzljODc5ZmExNmJhM2RkYWQxOTI5NzcyZjhiZWM5MDExNmU3ODZjNTcxMGY1MDg0ZTA1MmE3MTc4MWZjYTcxYjQ0ODg5OGVjY2JiZDgyZjlkYjk5Nzg4M2Q4OTVkYzRiMDIwZWJkM2FmOQ=='
dicts['firstDie'] = '1'
dicts['log_Id_view'] = '4444'
dicts['c_dl_um'] = '-'
dicts['AU'] = '5E7'
dicts['c_dl_rid'] = '1638195457309_383576'
dicts['H_PS_PSSID'] = ''
dicts['uuid_tt_dd'] = '10_37481785600-1633532465546-630652'
dicts['Hm_up_6bcd52f51e9b3dce32bec4a3997715ac'] = '%7B%22islogin%22%3A%7B%22value%22%3A%221%22%2C%22scope%22%3A1%7D%2C%22isonline%22%3A%7B%22value%22%3A%221%22%2C%22scope%22%3A1%7D%2C%22isvip%22%3A%7B%22value%22%3A%220%22%2C%22scope%22%3A1%7D%2C%22uid_%22%3A%7B%22value%22%3A%22weixin_43387852%22%2C%22scope%22%3A1%7D%7D'
dicts['c_dl_prid'] = '1638100826971_946707'
dicts['Hm_lvt_e5ef47b9f471504959267fd614d579cd'] = '1637833430'
dicts['Hm_ct_6bcd52f51e9b3dce32bec4a3997715ac'] = '6525*1*10_37481785600-1633532465546-630652!5744*1*weixin_43387852'
dicts['BT'] = '1633859763611'
dicts['HMACCOUNT'] = '13391551711E4651'
dicts['Hm_lvt_6bcd52f51e9b3dce32bec4a3997715ac'] = '1638500140,1638500349,1638500394,1638500403'
dicts['p_uid'] = 'U010000'
dicts['UN'] = 'weixin_43387852'
dicts['__gads'] = 'ID=20d2edb2ecff0dd8-2294b66f51cc00dd:T=1633682976:RT=1633682976:S=ALNI_MZ4Wd5yAdGLxFPr5rHe4LQE0aLwJw'
dicts['BIDUPSID'] = 'FBAA203ACF6D45F80BC31F3396736AD0'
dicts['dc_tos'] = 'r3ir2m'
dicts['c_ref'] = 'https%3A//i.csdn.net/'
dicts['ssxmod_itna'] = 'Yq+xBDuQG=YUGkDzxAhYo=mxWqY5Y3K3qoiQD/YBmDnqD=GFDK40oE7bD7mKn5BAuDeWxrGhuqWKFixPmaAiDOh31A+=0iEdDU4i8DCTrPoD4fKGwD0eG+DD4DWDmW7DnxAQDjxGpycuTXBDi3Dbg=Di4D+zd=DmqG0DDUH/4G2D7Uy8ivyWld52ubMiir4YB=DjqTD/+qFMWRFsa5VWjnTu44DC2v1oi519poqYAiWbqGybKGunqXV4uR1pq0Z3j5vKG4oBrdoBiPet0q3ngxU6GDPARCztP4KD++P7g+dBr5DGRwx3wDxD'
dicts['UserToken'] = '885ba7040ab64148b8d10b4450fcc279'
dicts['UserNick'] = '%E5%BD%92%E6%9D%A5%E7%A9%BA%E7%A9%BA'
dicts['BDRCVFR[C0p6oIjvx-c]'] = 'Ble67U-OKLffjRLnjc3nW6kg1IxpA7E'
dicts['ssxmod_itna2'] = 'Yq+xBDuQG=YUGkDzxAhYo=mxWqY5Y3K3qoiG9toFxBwm47pxUxBa4Hq7GFGfoiDhPizeG7q5h7Mt7khdzCG2O7=IYjr6oTN9ebmgXvejbDDQt5Uljyh7yEuBiEMSIhcGRVeRAB+Er04T2YpCHxTHtYBrV15C0eviq+WkT8vmqGKrtFG=Mo7sk/ntYiGQI87s1SYx1=TpeYcD5OALP3OQgpKTRGpC8bLiPSMTSCodV1ddSeu1P99Niqq8dQDd8+mcI+I5FHuv=yFBy=Ui7ZguyP6CkyK9C79iTqo4RqQd7EgTbEExB=XSjr88yXeYel5TGD1iDPnnD87ZjDQAGXQH+juTKGtnYnRahRAEKiPL+T8qkYT8m+Rfrue493By+ydLytmbuKaH8EG7wRfwD1eVGar8usImSzTL8TEB2qTwYBx4U5lGftyDTa9bB0sRWl748nyt6uW0xEDBAYAm8Hxm5fDx//oaf2ZYB4txDKdNdYzlm+0Ghg+xMcWCAXV138h8iDaP8DzG482Wr72i44D7=DYIOeD='
dicts['c_pref'] = 'https%3A//cn.bing.com/'
dicts['c_utm_medium'] = 'distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link'
dicts['UserName'] = 'weixin_43387852'
dicts['c_dl_fpage'] = '/download/weixin_38695452/12856957'
dicts['UserInfo'] = '885ba7040ab64148b8d10b4450fcc279'
#不能直接将字典对象设置为cookies ，需要通过requests库的cookiejar_from_dict()方法
#把字段对象转换为cookiejar对象
session.cookies = cookiejar_from_dict(dicts)
r = session.post('https://blog.csdn.net/')
print(r.status_code)
print(r.text)
save_session(session)

# print(session.headers)
# print(session.cookies.get_dict())
# session.headers,session.cookies = load_session() #将cookie设置为保存的cookie
```
>2. 借助图片识别技术识别图片文字。一种常用的是通过tesseract-ocr进行识别，一种借助百度提供的文字识别技术进行识别处理。

通过图像识别技术来获取文字信息，通常需要对图像进行预处理来提高识别准确率。这里给出一些处理流程。
```mermaid
    graph LR
        原始图片 --> 图片放大
        图片放大 --> 灰度化
        灰度化 --> 二值化
        二值化 --> 去除边框
        去除边框 --> 降噪
        降噪 --> 结束
```
示例代码如下所示：
```python
#图像处理方法
import cv2
import matplotlib.pyplot as plt
import numpy as np
#1.放大图片
#DPI≥ 300的图片有更好的识别效果
img_org = cv2.imread('R-C.png')
h,w = img_org.shape[:2]
h,w = h*2,w*2 #按比例放大两倍
img_l = cv2.resize(img_org,(w,h))
plt.subplot(121),plt.imshow(cv2.cvtColor(img_org,cv2.COLOR_BGR2RGB))
plt.subplot(121),plt.imshow(cv2.cvtColor(img_l,cv2.COLOR_BGR2RGB))
plt.show()
#2. 图像灰度化，通过cvtColor灰度化图像
img_org = cv2.imread('R-C.png')
img_gray = cv2.cvtColor(img_org,cv2.COLOR_BGR2GRAY) # 灰度化
plt.subplot(121),plt.imshow(cv2.cvtColor(img_org,cv2.COLOR_BGR2RGB))
plt.subplot(121),plt.imshow(cv2.cvtColor(img_org,cv2.COLOR_BGR2RGB))
plt.show()
#3.图像二值化
#二值化将图片转换为黑白图像，二值化可以通过cv2.threshold()方法，该方法会返回两个参数，第一个为阈值，第二个为转化后的图像
"""
cv2.threshold(src,thresh,maxval,type,dst=None)
其中thresh为阈值，maxval为高于阈值的处理，dst为二值化处理方法选择的参数。
dst指定的不同在于处理逻辑的不同。

"""
img_org = cv2.imread('R-C.png')
img_gray = cv2.cvtColor(img_org,cv2.COLOR_BGR2GRAY) # 灰度化
# 像素点高于127的设置为255，不高于127的设置为0
_,img_bin = cv2.threshold(img_gray,127,255,cv2.THRESH_BINARY)#二值化
plt.subplot(131),plt.imshow(cv2.cvtColor(img_org,cv2.COLOR_BGR2RGB))
plt.subplot(132),plt.imshow(cv2.cvtColor(img_gray,cv2.COLOR_BGR2RGB))
plt.subplot(133),plt.imshow(cv2.cvtColor(img_bin,cv2.COLOR_BGR2RGB))
plt.show()
#4.去除边框,当存在边框的时候去除,
def clearBorder(img):
    h,w = img.shape[:2]
    for y in range(0,w):
        for x in range(0,h):
            if(y<2 or y>w-2):
                img[x,y] = 255
            if x < 2 or x >h-2:
                img[x,y] = 255
    return img

#5. 降噪
# 噪声是图像中亮度或颜色的随机变化，降低噪声有利于准确率的提升，噪声去除的方法有很多
# 这里以线降噪为例
def interfaceLine(img):
    h,w = img.shape[:2]
    for y in range(1,w-1):
        for x in range(1,h-1):
            count = 0
            if np.all(img[x,y-1])>245:
                count = count+1
            if np.all(img[x,y+1])>245:
                count = count+1
            if np.all(img[x-1],y)>245:
                count = count+1
            if np.all(img[x+1],y)>245:
                count = count+1
            if count > 2 :
                img[x,y] = 255
    return img
```

通过tesseract-ocr进行识别，需要下载tesseract-oct,并且下载两个包。
```python
pip install pytesseract
pip install pillow
```
示例代码如下所示：
```python
def pyteFunction():
    image = Image.open('test.PNG')

    # 设置tesseract的安装路径,这里设置你下载的tesseract-ocr的安装路径
    pytesseract.pytesseract.tesseract_cmd = r'C:\Program Files (x86)\Tesseract-OCR\tesseract.exe'

    code = pytesseract.pytesseract.image_to_string(image)
    print(code)
```

通过百度API调用文字识别服务。可以访问[https://ai.baidu.com/](https://ai.baidu.com/)，然后搜索文字识别，创建一个文字识别应用，创建成功后，根据提供的APP_KEY和Secret_Key即可调用API，获取到服务。示例代码如下所示：

```python
#通过调用百度API进行验证码的识别
def identify_Verification_code(API_Key, Secret_Key, Verification_code):

    host = 'https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=' + API_Key + '&client_secret=' + Secret_Key
    response = requests.get(host)
    access_token = response.json()['access_token']


    request_url = "https://aip.baidubce.com/rest/2.0/ocr/v1/general_basic"
    # 二进制方式打开图片文件,Verification_code是要识别的验证码的名字
    f = open(Verification_code, 'rb')
    img = base64.b64encode(f.read())

    params = {"image": img}
    access_token = access_token
    request_url = request_url + "?access_token=" + access_token
    headers = {'content-type': 'application/x-www-form-urlencoded'}
    response = requests.post(request_url, data=params, headers=headers)
    shibie_result = response.json()['words_result'][0]['words']
    print(shibie_result)

#通过调用百度提供的接口来进行识别验证码
def baiduAPI():
    API_Key = '#' #应用的key
    Secret_Key = '#' #应用的Secret_Key
    Verification_code = 'R-C.PNG' #图片地址
    identify_Verification_code(API_Key, Secret_Key, Verification_code)
```

>>滑动验证码

滑动验证码涉及到图片拼接方面的知识。要想解决可以有以下思路，一是通过selenium模拟行为，解决滑动验证码，二是组建Cookie池绕过验证码。

通过selenium解决滑动验证码的流程：
>1. 获取图片（不带缺口的图片，带缺口的图片）
>2. 识别缺口位置（设置一个对比阈值，遍历两张图片，找出相同位置像素RGB差距，超过此阈值的像素点，此像素点的位置就是缺口的位置）
>3. 计算滑动距离
>4. 模拟运动

>>>www>m>wap

www是PC浏览器看到的网站，m和wap是移动端，大部分智能手机用的是m站，少部分旧手机用的还是wap。一般wap爬取较为简单，我们可以通过修改User-Agent模拟不同终端发送出请求，请求不同的页面。
如何设置不同的User-Agent,我们可以在浏览器扩展选项中下载相关的User-Agent的应用。以Edge为例：
下载图中的扩展应用。
![A33](/images/user-agent.PNG)
按照如下操作，选择需要模拟的浏览器，然后通过Verify User-Agent Setting可以查看User-Agent，以Iphone为例.
![A34](/images/step.PNG)
获取到的请求头：
```javascript
    Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1
```

>>小结

对于网页爬取登录问题和验证码问题，需要对具体的网页进行分析，是通过API解析还是通过Cookie池登录，以及登录过程中的验证码破解问题。

#### 4. <a id="four"></a> 终端协议分析
一个应用不仅仅具有PC端而且还具有移动客户端。所以我们在进行爬虫开发的时候，不仅仅可以从PC端进行爬取，而且还可以从移动客户端进行爬取。我们可以借助User-Agent来伪装自己，进行数据的爬取。

>### 1. PC端数据抓包分析

将爬虫伪装成PC客户端，可以对PC客户端进行抓包分析。PC抓包软件有Wireshark,Http Analyzer等。Wireshark擅长各类网络协议分析，比较重型。而Http Analizer更注重于对Http/Https协议的分析。Http Analizer可以针对某个进程进行抓包。

>>#### Wireshark

Wireshark的使用方法很简单，只需要设置过滤规则进行监听即可。
简单的过滤规则，如指定监听的网址ip.addr == 121.40.103.238(这个地址是虾米音乐网地址，[https://www.xiami.com/](https://www.xiami.com/))。然后点击开始监听。
则Wireshark就会监听和121.40.103.238的数据。通过cmd，ping这个地址即可。

![a41](/images/wireshark.PNG)

>>#### http Analyzer

http Analyzer的使用方法也很简单，点击开始监听即可。可以通过选择需要监听的进程进行监听。通过type可以选择需要过滤的数据类型。

通过抓包进行分析请求，进行数据爬取一般都是挺复杂的，涉及到逆向PC客户端软件和分析算法的能力。所以如果想要通过抓包进行爬取，需要了解相关技术进行爬取。

>### 2. App抓包分析

移动端的抓包分析，以Android App为例。对Android应用抓包，通过下载安卓模拟器，将应用安装到模拟器中，然后进行抓包分析。

这里采用wireshake进行抓包分析，wireshake的过滤示例如下：

>过滤域名包含baidu.com的数据
>http.host contains "baidu.com"
>过滤指定ip地址
>ip.addr == xxx.xxx.xxx.xxx


![a44](/images/wiresharke-1.PNG)
![a45](/images/wiresharke-2.PNG)



###### 5.<a id="five"></a> 初窥Scrapy爬虫框架

Scrapy爬虫框架操作简单，是比较流行的爬虫解决方案，所以在这里对Scrapy进行学习。
Scraoy使用Twisted这个异步网络来处理网络通信，并且包含了各种中间件接口，可以灵活的完成各种需求。

>#### 1. Scrapy各大组件

Scrapy的各个组件其实和之前学过的简单爬虫的各个模块相类似。

>>1. Scrapy 引擎(Engine)

Scrapy引擎负责控制数据流在系统的所有组件中流动，并在相应动作发生时触发事件。
这类似于爬虫调度器的作用。
>>2. Scrapy 调度器(Scheduler)

Scrapy调度器从引擎接收Request并将它们入队，以便之后引擎请求request时提供给引擎。这类似于URL管理器的作用。

>>3. Scrapy 下载器(Downloader)

Scrapy下载器负责获取页面数据并提供给引擎，而后提供给Spider。这类似于html下载器作用。

>>4. Spider

Spider是Scrapy用户编写用于分析Response并提取Item(即获取到的Item)或额外跟进的URL的类。每个Spider负责处理一个特定(或一些)网站。类似于html解析器作用。

>>5. Item Pipeline

Item Pipeline负责处理被Spider提取出来的Item。典型的处理有清理验证及持久化(例如存储到数据库中)。类似于数据存储器的作用。

>>6. Downloader middlewares（下载器中间件）

下载器中间件是在引擎及下载器之间的特定钩子(specific hook),处理Downloader传递给引擎的Response.
其提供了一个简便的机制，通过插入自定义代码来扩展Scrapy功能。

>>7. Scrapy中间件(Spider middlewares)

Scrapy中间件是在引擎及Spider之间的特定钩子(specific hook),处理Spider的输入(response)和
输出(Items及requests)。其提供了一个渐变的机制，通过插入自定义代码来扩展Scrapy功能。



![a58](/images/Scrapy.PNG)

![a59](/public/images/Spyder流程.PNG)


>#### 2. Scrapy的安装

这里介绍一下Windows下如何安装Scrapy(Python 3.7版本)。
如果不是Python3.7版本的，需要下载对应的Pywin32。

>#### 1. Pywin32下载

[下载地址](https://github.com/mhammond/pywin32/)

各个Pywin32的版本如图所示：
![a58](/images/pywin32.PNG)

找到3.7版本，64位的安装包。
![a59](/images/pywin32-1.PNG)

运行安装包后，通过命令行测试，如果没报错，则安装成功。
![a590](/images/pywin32-2.PNG)

安装成功后，需要通过pip下载pywin32，或者直接通过pycharm安装pywin32
```python
pip install pywin32
```

>#### 2. pyOpenSSL下载

[下载地址](https://github.com/pyca/pyopenssl) , 或者通过pycharm下载 pyOpenSSL,或者通过 pip install pyOpenSSL

>#### 3. lxml下载

通过pycharm下载，或者pip install lxml

>#### 4. Scrapy下载

通过pycahrm下载，或者通过pip install Scrapy

如果要查看是否安装成功，通过pip list命令可以查看已经安装的库。

>#### 3. Scrapy应用

>>1. 创建一个新的Scrapy项目

命令行切换到指定文件夹，运行命令 scrapy startproject xxxx ,即可创建名为xxxx的项目。

![a533](/images/scrapy-4.PNG)

如果出现"ImportError: DLL load failed: 找不到指定的程序"错误提示，或者出现python.exe无法找到入口....pythoncom37.dll。

首先下载：
```python
ImportError: DLL load failed: 找不到指定的程序
```

然后在电脑搜索pythoncom37.dll。观察无法找到入口的路径，一般都是在C盘的System32下，然后将搜索到的pythoncom37.dll所在的文件夹的内容(选择lib目录下的pywin32下的pythoncom37.dll文件夹里面的两个dll文件放到system32下)，复制到System32下，覆盖即可。

![a554](/images/scrapy-7.PNG)


创建成功后的目录结构如图所示：

![a556](/images/scrapy目录结构.PNG)


>>>#### 1. 创建爬虫模块

爬虫模块都放置于spiders文件夹中。爬虫模块用于从单个网站或多个网站爬取数据的类。包含初始页面的URL，网页链接，分析网页内容，提取数据。
需要创建一个Spider类，继承scrapy.Spider类。
需要定义下面三个属性：
name:区别Spider，名字必须唯一。
start_urls:Spider启动时进行爬取的入口URL列表。
parse():Spider的一个方法，被调用时，负责解析返回数据，提取数据。


启动爬虫模块：通过命令行，切换到项目根目录下，运行
scrapy crawl xxxx , 其中xxxx为Spider类的name属性。


>>>#### 2. 选择器

Scrapy选择器构建于lxml库之上。也可也通过Beautifulsoup库解析。

Selector的用法：Selector对象有四个基本方法
>1. xpath(query):传入XPath表达式query，返回该表达式所有节点的selector list列表

>2. css(query):传入CSS表达式query，返回该表达式所对应的所有节点的selector list列表

>3. extract():序列化该节点为Unicode字符串并返回list列表

>4. re(regex):根据传入的正则表达式对数据进行提取，返回Unicode字符串列表。

在spider类中的parse方法中，传入的一个参数为response,通过Selector(response)
即可创建一个Selector对象。
示例代码如下所示
```python
class CnblogsSpider(scrapy.Spider):
    name = "cnblogs" #爬虫名称
    allowed_domains = ["cnblogs.com"] #允许的域名
    start_urls = [
        "http://www.cnblogs.com/qiyeboy/default.html?page=1"
    ] # Spider启动时进行爬取的入口URL列表。



    #解析函数
    def parse(self, response):
        # #Selector使用
        # selector = Selector(response)
        # #调用xpath
        # selector.xpath()
        # #调用css
        # selector.css()
        # #调用re
        # selector.re()
        # #调用extract
        # selector.extract()

        papers = response.xpath(".//*[@class='day']")
        for paper in papers:
            url = paper.xpath(".//*[@class='postTitle']/a/@href").extract()[0]
            title = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            time = paper.xpath(".//*[@class='dayTitle']/a/text()").extract()[0]
            content = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            print(url,title,time,content)
```

>>>#### 3. 命令行工具

这里介绍一下Scrapy命令行功能。

创建一个项目
>scrapy createproject xxxx
运行一个项目
>scrapy crawl name（spider的name属性）
运行单个spider模块
>scrapy runspider xxxx.py
列出当前项目所有Spider
>scrapy list
快速创建spider模板
>scrapy genspider -l
>scrapy genspider -d basic
>scrapy genspider -t basic example example.com
将项目部署到Scrapy服务
>scrapy deploy

>>>#### 4. 定义Item

Item用于保存爬取到的数据，类似于字典类型，在之前的目录结构中，有个items.py文件用来定义存储数据的Item类。这个类需要继承scrapy.Item。示例代码如下所示：
```python
import scrapy

class SpiderItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    pass

class CnblogspiderItem(scrapy.Item):
    # define the fields for your item here like
    # 类似于字典类型，可以直接通过[]和get方法获取
    url = scrapy.Field()
    time = scrapy.Field()
    title = scrapy.Field()
    content = scrapy.Field()

if __name__ == '__main__':
    item = CnblogspiderItem(title='爬虫',content='爬虫开发')
    print(item['title'])
    print(item.get('title'))
    print(item.keys())
    print(item.items())
    item2 = CnblogspiderItem(item) #Item复制
    #dict和item转化
    dict_item = dict(item)
    print(dict_item)
    item = CnblogspiderItem({'title':'爬虫','content':'开发'})
    print(item)
```

我们可以在原有的Item基础之上，添加更过的字段用于扩展Item。

```python
class CnblogspiderItem(scrapy.Item):
    # define the fields for your item here like
    # 类似于字典类型，可以直接通过[]和get方法获取
    url = scrapy.Field()
    time = scrapy.Field()
    title = scrapy.Field()
    content = scrapy.Field()
class newCnblogspiderItem(CnblogspiderItem):
    body = scrapy.Field()
    title = scrapy.Field(CnblogspiderItem.fields['title'],body=body)
```

如果要实现翻页功能,将提取出来的URL，构建新的Request对象，并指定解析方法。：
```python
    #解析函数
    def parse(self, response):
        # #Selector使用
        # selector = Selector(response)
        # #调用xpath
        # selector.xpath()
        # #调用css
        # selector.css()
        # #调用re
        # selector.re()
        # #调用extract
        # selector.extract()

        papers = response.xpath(".//*[@class='day']")
        for paper in papers:
            url = paper.xpath(".//*[@class='postTitle']/a/@href").extract()[0]
            title = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            time = paper.xpath(".//*[@class='dayTitle']/a/text()").extract()[0]
            content = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            item = CnblogspiderItem(url=url,title=title,time=time,content=content)
            yield item #将数据返回
        next_page = Selector(response).re(u'<a href="(\S*)">下一页</a>')
        if next_page:
            #Request对象中的URL为请求链接，callback为回调方法，回调方法用于指定由
            #谁来解析此项Request请求的响应
            yield scrapy.Request(url=next_page[0],callback=self.parse)
```

>>>#### 5. 定义Item Pipeline

通过Item Pipeline可以对数据进行持久化存储，通过Item Pipeline可以对Item进行处理。
Item Pipeline主要有如下几个作用：
1. 清理HTML数据
2. 验证爬取数据的合法性，检查Item是否包含某些字段
3. 查重并丢弃
4. 将数据结果保存到文件或数据库中

Item Pipeline组件是一个独立的Python类，必须实现proocess_item方法
>process_item(self,item,spider)
其中itme是被爬取的item，Spider对象代表着爬取该Item的Spider

示例代码如下所示:
```python
class CnblogspiderPipeline(object):
    
    def __init__(self):
        self.file = open('parpers.json','wb')
    def process_item(self,item,spider):
        if item['title']:
            #将数据保存到文件中
            line = json.dumps(dict(item))+"\n"
            self.file.write(line)
            return item 
        else:
            #DropItem是异常类型，如果不存在就爆出异常，进行丢弃
            raise DropItem("Missing title in %s"%item)

```

编写好的Item Pipeline需要在settings.py(在目录结构中可以找到)中，将类添加到ITEM_PIPELINES变量中。
示例代码如下所示：其中的key就是Item Pipeline的类路径，value是自定义数字，当执行Item Pipeline时，按数字大小从低到高依次执行Item Pipeline。通常定义在0-1000范围内。
```python
ITEM_PIPELINES = {
   'spider.pipelines.SpiderPipeline': 300,
}
```

>>>#### 6. 定义Item Pipeline内置数据存储

#### 内置文本格式下载方式

Scrapy提供了一些简单的存储方式。生成一个带有爬取数据的输出文件(feed)。Scrapy的输出自带各种序列化格式。
>json
JsonItemExporter
>JSON lines
JsonLinesItemExporter
>CSV
CsvItemExporter
>XML
XmlItemExporter
>Pickle
PickleItemExporter
>Marshal
MarshalItemExporter

使用方法：通过命令行使用，例如scrapy crawl xxx -o xxx.csv

#### 内置图片和文件下载方式

Scrapy提供了可重用的Item Pipeline(MediaPipeline分为FilesPipeline和ImagesPipeline)，如果要使用ImagesPipeline需要下载pillow模块(pip install pillow)。这类Pipeline具有如下特性。
1. 避免重新下载最近下载过的数据
2. 指定存储的位置和方式
对于ImagesPipeline还具有额外的特性：
1. 将所有下载的图片转换为通用的格式(JPG)和模型(RGB)
2. 缩略图生成
3. 检测图像的宽/高，确保它们满足最小限制
对于要下载的Item会在内部保存一个内部对了，避免多次下载几个Item共享的同一个图片。

对于FilesPipeline的工作流程：
1. 在一个爬虫里，抓取一个Item，将文件URL，放入file_urls组内。
2. Item从爬虫内返回，进入Item Pipeline
3. 当Item进入FilesPipeline,file_urls组内的URL将被Scrapy的调度器和下载器安排下载。
4. 当文件下载完后，另一个字段files将被更新到结构中。这个组将包含一个字典列表，其中包括下载文件的信息。信息包括：下载文件的信息比如下载路径，源抓取地址，图片校验码。如果下载失败，会记录下载错误信息。
   
对于ImagesPipeline的工作流程：
1. 从一个爬虫中，抓取一个Item，把图片的URL放图images_url组内。
2. 项目从爬虫内返回，进入Item Pieline
3. 当Item进入ImagesPipelin,images_urls组内的URL将被Scrapy的调度器和下载器安排下载。
4. 当文件下载完成之后，另一个字段(images)将别更新到结构中。信息包括：下载图片的信息比如下载路径，源抓取地址，图片校验码。如果下载失败，会记录下载错误信息。

#### 使用FilesPipeline:

>1. 在settings.py文件的ITEM_PIPELINES添加一条'scrapy.pipelines.files.FilesPipeline':1
>2. 在item添加两个字段，比如

file_urls = scrapy.Filed()
files = scrapy.Filed()
>3. 在settings.py中添加下载路径FILES_STORE，文件url所在的itme字段FILE_URLS_FILED，和文件信息所在item字段FILES_RESULT_FIELD。例如：
```python
FILES_STORE = 'G:\\python'
FILES_URLS_FIELD = 'file_urls'
FILES_RESULT_FIELD = 'files'
FILES_EXPIRES = 30 # 设置文件过期时间(天)
```

#### 使用ImagesPipeline:

>1. 在settings.py文件的ITEM_PIPELINES添加一条'scrapy.pipelines.files.ImagesPipeline':1
>2. 在item添加两个字段，比如
image_urls = scrapy.Filed()
images = scrapy.Filed()
>3. 在settings.py中添加下载路径IMAGES_STORE，文件url所在的itme字段IMAGES_URLS_FILED，和文件信息所在item字段IMAGES_RESULT_FIELD。IMAGES_THUMBS制作缩略图，并设置图片大小和尺寸。如果需要过滤特别小的图片可以使用IMAGES_MIN_HEIGHT和IMAGES_MIN_WIDTH来设置图片的最小高和宽。例如：
```python
IMAGES_STORE = 'G:\\python'
IMAGES_URLS_FIELD = 'file_urls'
IMAGES_RESULT_FIELD = 'files'
IMAGES_THUMBS = {
    'small':(50,50),
    'big':(270,270)
}
IMAGES_EXPIRES = 30 # 设置文件过期时间(天)
```

在之前的示例基础上，在setting.py设置如下：
```python
ITEM_PIPELINES = {
   'spider.pipelines.CnblogspiderPipeline': 300,
   'scrapy.pipelines.images.ImagesPipeline':1
}
IMAGES_STORE = 'G:\\python\spider\spider'
IMAGES_URLS_FIELD = 'cimage_urls'
IMAGES_RESULT_FIELE = 'cimages'
IMAGES_EXPIRES = 30
IMAGES_THUMBS = {
   'small':(50,50),
   'big':(270,270)
}
```
然后在CnblogspiderItem添加两个字段cimage_urls和cimages。
修改spider代码，用于下载图片
```
 def parse(self,response):
        papers = response.xpath(".//*[@class='day']")
        for paper in papers:
            url = paper.xpath(".//*[@class='postTitle']/a/@href").extract()[0]
            title = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            time = paper.xpath(".//*[@class='dayTitle']/a/text()").extract()[0]
            content = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            item = CnblogspiderItem(url=url,title=title,time=time,content=content)
            request = scrapy.Request(url=url,callback=self.parse_body) #调用Request方法，并设置解析函数
            request.meta['item'] = item #将item暂存
            yield request
        next_page = Selector(response).re(u'<a href="(\S*)">下一页</a>')
        if next_page:
            #Request对象中的URL为请求链接，callback为回调方法，回调方法用于指定由
            #谁来解析此项Request请求的响应
            yield scrapy.Request(url=next_page[0],callback=self.parse)

    def parse_body(self,response):
        item = response.meta['item']
        body = response.xpath(".//*[@class='postBody']")
        item['cimage_urls'] = body.xpath('.//img//@src').extract()# 提取图片链接
        yield item
```

这里容易看出ImagePipeline的执行流程，首先是将图片url放入到image_urls中，然后由Scrapy下载，最后保存到指定的路径。

#### 自定义FilesPipline和ImagesPipeline

如果要自定义FilesPipline和ImagesPipeline则需要继承FilesPipeline或者ImagesPipeline，重写
get_media_requests和item_completed()方法。

```python
在工作流程中，管道会得到图片的URL并从项目中下载。需要重写get_media_requests方法.并对各个图片URL返回一个Request。
def get_meida_requests(self,item,info):
```

```python
class  MyImagesPipeline(ImagesPipeline):

    # 管道通过get_media_requests接收到图片的URL并从项目中进行下载
    # get_media_request会返回图片URL对应的Request
    # 对于返回的结果results会以二元素的元组列表形式传送到item_completed方法
    # 返回格式
    # success: 布尔值，成功返回True，是啊比返回False
    # url:图片下载的url
    # path:图片存储路径
    # checksum:图片内容的MD5 hash

    def get_media_requests(self, item, info):
        for image_url in item['image_urls']:
            yield scrapy.Request(image_url)

    # 当所有图片请求完成时，item_completed方法将被调用
    # results是get_media_requests下载完成之后的结果。
    # item_completed需要返回一个输出。这个输出会被送到随后的ItemPipelines(在Scrapy中，item的执行顺序会根据id大小依次调用)
    #
    def item_completed(self, results, item, info):
        image_paths = [x['path'] for ok,x in results if ok]
        if not image_paths:
            raise DropItem("Item contains no images") #丢弃项目DropItem
        item['image_paths'] = image_paths 
        return item
```

>>>#### 7. 启动爬虫

>1. 命令行方式
scrapy crawl spider_name 
>2. 使用CrawlProcess类
CrawlProcess类内部会开启Twisted reactor，配置log,设置Twisted reactor自动关闭。
 
```python
if __name__ == '__main__':
    #初始化CrawlerProcess参数
    procerss = CrawlerProcess({
        'USER-AGENT':'Mozilla/4.0 (compatible; MSIE 7.0 ; Windows NT 5.1)'
    })
    procerss.crawl(CnblogsSpider) #运行爬虫
    procerss.start()
```

带启动参数的CrawlProcess类。
```python

    procerss = CrawlerProcess(get_project_settings())
    procerss.crawl(CnblogsSpider)  # 运行爬虫
    procerss.start()

```

>3. 使用CrawlerRunner

CrawlerRunner：在spider结束后，必须自行关闭Twisted reactor 需要在CrawlerRunner.crawl所返回的对象中添加回调函数。
```python
  configure_logging({'LOG_FORMAT':'%(levelname)s：%(message)s'})
    runner = CrawlerRunner()
    d = runner.crawl(CnblogsSpider)
    d.addBoth(lambda _:reactor.stop())
    reactor.run()
```
>>>#### 8. 强化爬虫

这里主要是介绍关于Scrapy的调试方法和异常，控制运行状态等内容。

>##### 1. 调试方法

#### Parse命令

检查Spider输出的最基本方法(Parse命令)，可以在函数层上检查spider各个部分的效果。
使用方法为scrapy parse --spider==spider_name -c parse -d 2<item_url>
例如 scrapy parse --spider==cblogs -c parse -d"https://www.baidu.com"

#### Scrapy shell

通过Scrapy shell 可以查看spider某个位置中被处理的response，以确定期望的response是否到达特定位置。
在spider类中，添加scrapy.shell.inspect_response方法。当程序运行到inspect_response方法时，会暂停，并切换进shell中，方便进行调试。如果要退出终端可以ctrl+d进行退出。代码如下所示：
```python
 def parse(self,response):
        papers = response.xpath(".//*[@class='day']")
        #添加scrapy shell

        from scrapy.shell import inspect_response
        inspect_response(response,self)

        for paper in papers:
            url = paper.xpath(".//*[@class='postTitle']/a/@href").extract()[0]
            title = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            time = paper.xpath(".//*[@class='dayTitle']/a/text()").extract()[0]
            content = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            item = CnblogspiderItem(url=url,title=title,time=time,content=content)
            request = scrapy.Request(url=url,callback=self.parse_body) #调用Request方法，并设置解析函数
            request.meta['item'] = item #将item暂存
            yield request
        next_page = Selector(response).re(u'<a href="(\S*)">下一页</a>')
        if next_page:
            #Request对象中的URL为请求链接，callback为回调方法，回调方法用于指定由
            #谁来解析此项Request请求的响应
            yield scrapy.Request(url=next_page[0],callback=self.parse)

    def parse_body(self,response):
        item = response.meta['item']
        body = response.xpath(".//*[@class='postBody']")
        item['cimage_urls'] = body.xpath('.//img//@src').extract()# 提取图片链接
        yield item

```

#### loggin

通过运行后的记录查看爬虫的运行状态

#### 编译器

借助编译器提供的Debug进行断点调试，查看程序运行情况。

>##### 2. 异常

之前已经使用过以恶异常也就是抛弃Item,DropItem。下图是关于Scrapy异常的介绍。
![566](/images/scrapy-e1.PNG)

![567](/images/scrapy-e2.PNG)

>##### 3. 控制运行状态

通过telnet访问Scrapy终端。
talnet localhost 6023
进入终端后，通过est()可以查看scrapy运行情况。
通过engine/pause()暂停运行，通过engine.unpause()继续运行。
通过engine.stop()停止运行。
配置telnet，在setting.py中配置IP和端口。
TELNETCONSOLE_PORT:6023 #设置为0或者None动态分配端口。
TELNETCONSOLE_HOST:'127.0.0.1' #y也就是本地地址ip




###### 7.<a id="seven"></a> 实战项目：Scrapy爬虫

