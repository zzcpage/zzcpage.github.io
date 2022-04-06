---
title: Python爬虫-Scrapy进阶
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

### [1.Spider模块](#one)
### [2.Item Loader](#two)
### [3.请求与响应](#three)


##  深入Scrapy爬虫框架

### <a id="one"></a>1. Spider模块

Spider模块是定义爬虫的动作及分析网页结构的地方，我们容易看出，在这里给出了解析网页获取元素，并进行是否继续爬取下一个网页的操作(也就是爬虫的动作)。Spider的执行流程
>1. 从入口URL初始化Request并设置回调函数。这个Reuquest下载完毕返回Response，并作为参数传送给回调函数，Spider初始的Request是通过调用start_requests()方法获取。start_requests()读取start_urls中的URL，并以parse为回调函数生成Request。也就是说初始的URL，只需要在start_urls加入，系统会自动的获取response，并以parse()为解析函数。

>2. 在回调函数分析Response，返回Item对象，dict,ruquest或者一个包括三者的可迭代容器。其中返回的Request对象会经过Scrapy处理，下载相应内容，并调用设置相应的解析函数。例如：
```python
 request = scrapy.Request(url=url,callback=self.parse_body) #调用Request方法，并设置解析函数

```

>3. 在解析函数内，可以使用页面解析技术，对页面元素进行解析，可以用BeautifuleSoup等等技术。通过response可以获取到响应的内容。将分析的数据生成item

>4. 由spider返回item,可以经过Item Pipeline被存到数据库或使用Feed exports存入到文件中。

##### Spider类的成员变量

```python
   '''
    @:param name 定义spider名字的字符串，名字必须唯一。可以生成多个相同的spider实例
    通常可以用网站域名命名spider

    @:param allowed_domains: 包含了spder允许爬取的域名列表。
    当OffsiteMiddleware组件启用时，域名不在列表中的URL不会被跟进。

    @:param statr_urls:URL列表，当没有配置statr_requests9）f方法的时候，spider会从该列表开始进行爬取。也就是说爬虫开始爬取的
    URL就是从start_urls中获取。

    @:param custom_setting：该设置是一个dict,当启动spider时，该设置将会覆盖项目级的设置。也就是
    说可以在这里对spider单独定义。

    @:param crawler 该属性在初始化class后，由类方法from_crawler()设置。并且链接了
    本spider实例羽Crawler对象。
    '''
    name = 'myspider'
    allowed_domains = ["www.baidu.com"]
    start_urls = [
        "https://www.baidu.com"
    ]
    custom_settings = {}
    crawler = ""

```
##### Spider类的方法

```python
    # 常用的Spider方法
    # 该方法必须返回一个可迭代对象，对象包含spider用于爬虫的第一个request。
    # 也就是说 start_requests是项目启动的开始，是根据start_url作为项目启动URL
    # 如果没有设置start_requests方法，就会默认从start_urls的url生成Request。
    # 如果需要定制最初爬取的Request对象，可以重写方法。
    # 例如通过POST登录
    # 总结来说：strt_request就是整个程序的入口，如果不指定就是直接从start_ruls中获取url，以parse()为回调函数进行解析。
    def start_requests(self):
        return [scrapy.FormRequest("http://www.example.com/login",formdata={
            'user':'john','pass':'secret'
        },callback=self.login)]

    # start_requests对url请求后的响应，会通过login进行处理
    def login(self,response):
        pass


    def make_requests_from_url(self, url):
        '''
        接受一个URL并返回用于爬取的Request对象
        :param url:
        :return:
        '''
        pass

    def parse(self, response, **kwargs):
        '''
        用于解析网页内容，一般作为初始URL解析的回调函数
        :param response:
        :param kwargs:
        :return:
        '''

    def close(spider, reason):
        '''
        当Spider关闭时，该函数被调用。可以用来在spider关闭时，释放占用的资源。
        :param reason:
        :return:
        '''
```

Scrapy除了Spider类作为基类进行扩展，还提供了CrawlSpider，XMLFeedSpider,CSVFeedSpider和SitemapSpider等类来实现不同的爬虫任务。

###### CrawlSpider

CrawlSpider类常用于爬取一般的网站。其中定义了一些规则(rule)来提供跟进链接功能。
CrawlSpider提供了新的属性rules。rules包含一个或多个Rule对象的集合。每个Rule对爬取网站的动作定义了特定的规则。如果多个Rule匹配相同的链接，则先定义的被调用。
CrawlSpider提供的初始URL解析方法，parse_start_url(response)。该方法返回一个Item对象或者一个Request对象或者包含二者的对象。使用示例如下所示：

```python
class MyCrawlSpider(CrawlSpider):
    name = 'crawlSpider'
    allowed_domains = ["cnblogs.com"]#域名
    start_urls = [
        "http://www.cnblogs.com/qiyeboy/default.html?page=1"
    ]
    # Rule原型
    # scrapy.contrib.spiders.Rule(link_exactor,callback=None,cb_kwargs=None,
    # follow=None,process_links=None,process_request=None)
    rules = (
        Rule(LinkExtractor(allow=("/qiyeboy/default.html\?page=\d{1,}",)),
                    follow=True,
                    callback='parse_item'
                           ),
    )
    # LinkExtractor对象的构造
    # allow: 用于匹配满足正则表达式的链接
    # deny: 排除正则表达式匹配的链接，优先级高于allow
    # allow_domains：允许的域名，可以是list或str
    # deny_domains:排除的域名
    # restrict_xpaths:提取满足Xpath选择条件的链接。
    # restrict_css:xxxCSSxxx的链接
    # tags: 提取指定标签下的链接。
    # unique:链接是否去重
    # process_value:值处理函数。
    
    def parse(self, response, **kwargs):
        papers = response.xpath(".//*[@class='day']")
        for paper in papers:
            url = paper.xpath(".//*[@class='postTitle']/a/@href").extract()[0]
            title = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            time = paper.xpath(".//*[@class='dayTitle']/a/text()").extract()[0]
            content = paper.xpath(".//*[@class='postTitle']/a/text()").extract()[0]
            item = MyCrawlSpider(url=url, title=title, time=time, content=content)
            request = scrapy.Request(url=url, callback=self.parse_body)  # 调用Request方法，并设置解析函数
            request.meta['item'] = item  # 将item暂存
        yield request

    def parse_body(self,response):
        item = response.meta['item']
        body = response.xpath(".//*[@class='postBody']")
        item['cimage_urls'] = body.xpath('.//img//@src').extract()# 提取图片链接
        yield item
```

###### XMLFeedSpider

XMLFeedSpider被设计用于通过迭代各个节点来分析XML源。迭代器可以从Iternodes,XML,HTML中选择。在XMLFeedSpider中，需要定义下列类属性来设置迭代器及标记名称。
1. iterator

用于确定使用哪个迭代器string,默认为iternodes，可选项有(1. iternodes, 2. html , 3. html)

2. itertag
   
   itertag为一个包含开始迭代的节点名string

3. namespaces

    称为命名空间，由(prefix,url),元组(tuple)所组成的list。这里定义了在文档中会被spider处理可用的namespace,prefix和url会被自动调用。由register_namespace()方法生成namespace。

示例代码如下所示：
```python
class MyXMLFeedSpider(XMLFeedSpider):

    name = "myxmlfeed"
    allowed_domains = ['cnblogs.com']
    start_urls = ["https://feed.cnblogs.com/blog/u/269038/rss"]
    namespaces = [{'n','http://www.sitemaps.org/schemas/sitemap/0.9'}]
    iterator = 'html' # 用于定义解析方式
    itertag = 'entry'
    #XMLFeedSpider方法
    def adapt_response(self, response):
        '''
        这个方法在页面解析前和页面下载后之间被调用。可以用于修改Response内容，并再返回。
        :param response:
        :return:
        '''
        return  response

    def parse_node(self, response, selector):
        '''
            当节点符合itertag时，该方法被调用。接收到的response以及相对应的Selector作为参数传递给该方法。
            需要返回一个Item对象或Request对象，或包含二者的可迭代对象
        :param response:
        :param selector:
        :return:
        '''
        print(selector.xpath('id/text()').extract()[0])
        print(selector.xpath('title/text()').extract()[0])
        print(selector.xpath('summary/text()').extract()[0])

    def process_results(self, response, results):
        '''
        在页面解析后，数据返回前进行处理。主要是对返回数据的最后处理。修改Item的内容
        :param response:
        :param results:
        :return:
        '''

        return [response,results]

```


### <a id="two"></a>2. Item Loader模块

>##### Item Loader是什么？

Item Loader提供了一种边界的方式填充抓到的Items。Item Loader可以直接对Item分析，并提取出想要的数据保存到容器中，而Item则是机械的根据键值对对应，返回数据。所以Item Loader更加灵活，高效。

Item Loader负责数据的收集，处理和填充。Item Loader包含两个重要的组件：输入处理器(input processors)和输出处理器(output processors)。

1. Item Loader的每个字段都包含了一个输入处理器和输出处理器。
2. 输入处理器接收到response后，通过add_xpath,add_css,add_value等方法提取数据，并将数据保存到Item Loader中。
3. 收集完成数据之后，通过ItemLoader.load_item()方法来填充并返回Item对象。load_item()方法内部先调用输出处理器来处理收集到的数据，结果保存到最终的Item中。

>##### Item Loader使用方法

在Item中声明输入输出处理器
```python
# 在Item中声明输入和输出处理器
def filter_price(value):
    if value.isdigit():
        return value

class Product(scrapy.Item):

    name = scrapy.Field(
        input_processor=MapCompose(remove_tags),
        output_processor=Join(),
    )

    price = scrapy.Field(
        input_processor=MapCompose(remove_tags,filter_price),
        output_processor=TakeFirst(),
    )

    stock = scrapy.Field()
    last_updated = scrapy.Field(serializer=str)
```

在Item Loader类中声明类似field_in和field_out的属性。

```python
# ItemLoader
class ProductLoadr(ItemLoader):
    default_output_processor = TakeFirst()
    # 声明输入输出处理器
    #输入处理器
    name_in = MapCompose(unicode.title)
    #输出处理器
    name_out = Join()
    price_in =  MapCompose(unicode.price)
    price_out = Join()
    stock_in = MapCompose(unicode.stock)
    stock_out = Join()
    last_updated_in = MapCompose(unicode.last_updated)
    last_updated_out = Join()

```

>##### Item Loader Context

Item Loader Context是一个任意的键值对字典。能够被Item Loader中的输入输出处理器所共享。
可以用于调整输入输出处理器的行为。
使用Item Loader Context就是为了能够提高代码复用，便于扩展。如下代码所示，可以在原有的Item Loader基础上，对属性进行解析。如果对于不同的解析，只需要设置解析方法即可，增加了可复用性。就不需要再单独设置一个Loader ，可以复用Loader。

对于输入处理器，我们可以借助方法进行扩展。对于输出处理器，通常是再Item字段元数据进行声明。

```python
# Item Loader Context
# 通过接收loader_context,能告诉Item loader自己能够接收Item Loader context ,
# 所以方法被调用的时候能将当前的active Context传递给该方法
def parse_length(text, loader_context):
    unit = loader_context.get('unit', 'm')
    # 获取长度
    parsed_length = len(unit)
    return parsed_length
class ProductLoadr(ProductLoader):
    # 定义修改context
    length_out = MapCompose(parse_length, unit='cm')

```
>##### Item Loader 内置的处理器

##### 1. MapCompose

输入处理器，将多个方法的执行结果按顺序组合产出最终的输出。
```python
#MapCompose
#和Compose类似，但是这个方法对输入是：每个元素单独传入第一个函数处理，然后将结果又作为
#整个迭代对象传入到后面的函数进行处理。
#如果输入是None会被自动忽略
#相当于这个方法会对每个元素单独处理
def filter_world(x):
    return None if x =='world' else x
proc = MapCompose(filter_world,unicode.upper)
print(proc([u'hello',u'world',u'this',u'is',u'scrapy']))


```
##### 2. Identity

最简单的处理器，不做任何处理，直接返回原来的数据，无参数。
```python
#Identity ：简单的处理器，不做任何处理，直接返回原来的数据
identity = Identity()
print(identity(["1231","321"]))
```
##### 3. TakeFirst

返回第一个非空值，常用于单值字段的输出处理器，无参数。
```python
# TakeFirst:输出处理器
# TakeFirst用于返回第一个非空值，常用于单值字段的输出处理。
proc = TakeFirst()
print(proc(['', 'one', 'two', 'three']))
```

##### 4. Compose

```python
#Compose
#用于将给定的多个方法的组合构造处理器
#每个输入值传递到第一个方法，然后将结果传递到第二个方法以此类推，最后一个方法的返回值作为输出
#如果需要当遇到None值时候停止处理，可以通过传递stop_on_one=False设定。
proc = Compose(lambda v:v[0],str.upper)
print(proc(['hello','world']))
```

##### 5. Join

```python
#Join
#返回用分隔符separator连接后的值，separator默认为空格，不接受loader contexts/
proc = Join()
print(proc(['one','two','three']))

```

##### 6.SelectJmes

指定json_path查询并返回值，需要jmespath的支持，每次只接收一个输入。
```python
#SelectJmes
#指定json_path查询并返回值，需要jmespath的支持
proc = SelectJmes("foo")
print(proc({'foo':'bar'}))
```

>#### <a id="two"></a>3.Item Pipeline模块
完整的Item Pipeline的demo:
```python
# 完整的Item Pipeline
class MongoPipeline(object):
    coolection_name = 'scrapy_items'
    def __init__(self,mongo_uri,mongo_db):
        self.mongo_uri = mongo_uri
        self.mongo_db = mongo_db

    #crawler是一个Crawler对象
    #从Crawl属性中，创建一个pipeline示例，Crawler对象能够接触所有Scrapy的核心组件如seetings，singnals。
    @classmethod
    def from_crawler(cls,crawler):
        return cls(
            mongo_uri=crawler.settings.get('MONGO_URI'),
            mongo_db=crawler.settings.get('MONGO_DATABASE','items')
        )
    # 参数spider表示一个Spider对象，表示被开启的Spider
    # 当spider被开启的时候，这个方法会被调用
    def open_spider(self,spider):
        self.client = pymongo.MongoClient(self.mongo_uri)
        self.db = self.client[self.mongo_db]

    #当spider被关闭的时候，这个方法会被调用
    def close_spider(self,spider):
        self.client.close()

    def process_item(self,item,spider):
        self.db[self.coolection_name].insert(dict(item))
        return item

```

### <a id="three"></a>3. 请求与响应

>#### 1. Request对象

1. Request对象

Request对象相当于一个HTTP请求。通常在Spider产生，传递给下载器，最后返回一个响应。
Request(url[,callback,method='GET',headers,body,cookies,meta,encoding='utf-8',priority=0,dont_filter=False,errback])。
url:请求链接。callback:指定用于解析请求响应的方法，如果没有指定，默认使用spider的parse方法。
method:HTTP请求方式。body:请求的body。headers:请求头。cookies（dict or list）：请求的cookie信息。
encoding:请求的编码。priority:请求的优先级，默认为0。dont_filter:标明该请求不应由调度器过滤，适用于多次执行相同请求。
errback:如果请求出现异常，该方法将被调用。
meta的参数如下所示：
![31](/images/spider/request-meta.PNG)
示例代码：
```python
request_with_cookies = scrapy.Request(url='http://www.example.com',
                                              cookies={'currency':'USD','country':'UY'})
#使用字典列表发送
request_with_cookies = scrapy.Request(url='http://www.example.com',
                                     cookies=[{'name':'currency','value':'USD',
                                               'domain':'example.com','path':'/currency'}])
#meta(dont_merge_cookies属性)可以用于当请求发送后，不将返回的cookie信息和现有cookie合并

request_with_cookies = scrapy.Request(url='http://www.example.com',
                                     cookies=[{'name': 'currency', 'value': 'USD',
                                               'domain': 'example.com', 'path': '/currency'}],
                                     meta={'dont_merge_cookies':True})
```

2. FormRequest

```python
#FromRequest提供了一个类方法from_response
#from_response(response[,formname=None,formnumber=0,formdata=None,formxpath=None,
# clickdata=None,dont_click=False])
# response: 一个包含HTML表单的响应页面，formname:表单的name属性，formnumber:用于指定第几个表单
# formdata(dict):用于填充表单中属性的值
# formxpath:通过xpath定位表单，第一个匹配的将会被操作
# 示例
scrapy.FormRequest.from_response(
   response,
   formdata={'username': 'john', 'password': 'secret'},
   callback=self.after_login
)
```

使用实例如下所示：
```python
class LoginSpider(scrapy.Spider):
    name = 'example.com'
    start_url = ['http://www.example.com/users/login.php']

    def parse(self, response, **kwargs):
        return scrapy.FormRequest.from_response(
            response,
            formdata={'username':'john','password':'secret'},
            callback=self.after_login
        )

    def after_login(self,response):
        if "authentication failed" in response.body:
            self.logger.error("Login failed")
            return

```

>#### 2. Response对象

Response就是页面请求后返回的内容。
Response的子类TextResponse可以在Response的基础上添加智能编码的功能。

### <a id="four"></a>4. 下载器中间件

下载器中间件是位于request/response处理的钩子框架。用于全局修改request和response，可以用于定制爬虫系统。

>1. 激活下载器中间件

在Settings.py中，设置DOWNLOADER_MIDDLEWARES的属性。将下载器添加到字典内（类路径，序号），并设定执行顺序。
如果要禁用组件，只要把值设置为None

>2. 编写中间件

```python
# 以产生User-Agent为例
import random
class RandomUserAgent(object):
    def __init__(self,agents):
        self.agents = agents

    @classmethod
    def from_crawler(cls,crawler):
        #这里用于创建一个实例
        #从Setting载入USER_AGENTS,返回一个实例
        return cls(crawler.settings.getlist('USER_AGENTS'))


    def process_request(self,request,spider):
        '''
        用于处理网页请求，当request经过这个组件时候，这个方法会被调用。

        :param spider:
        :return: None或者response或者request对象
        '''
        # 在process_request中设置User_Agent
        request.headers.setdefault('User-Agent',random.choice(self.agents))

    def process_response(self,request,response, spider):
        '''
        用于处理响应。返回Response则被其它中间件处理，返回request则重新执行中间链。
        :param request:
        :param response:
        :param spider:
        :return: response或者request对象
        '''
        pass

    def process_exception(self,request, exception,spider):
        '''
        用于处理异常。返回None则由其它中间件继续处理异常。
        返回Response则中间链的response方法被调用
        返回Request对象，则返回的request将被重新调用下载。
        :param request:
        :param exception:
        :param spider:
        :return: None或者response或者request对象
        '''
        pass

```

### <a id="five"></a>5. Spider中间件

```python
from urllib.request import Request

from w3lib.url import canonicalize_url

# spider中间件
class UrlCanonicalizerMiddleware(object):

    def process_spider_input(self,response,spider):
        '''
        response经过spider中间件时，该方法被调用，用于处理response
        :param response:
        :param spider:
        :return: None（Scrapy继续处理该response）或异常
        '''
        pass
    def process_spider_output(self,response,result,spider):
        '''
        处理Response返回result。result包含Request,Dict或Item的可迭代对象
        :param response:
        :param result:
        :param spider:
        :return:
        '''
        pass
    def process_spider_exception(self,response,spider):
        '''
        用于处理process_spider_input抛出的异常。
        :param response:
        :param spider:
        :return:
        '''
        pass
    def process_start_requests(self,start_requests,spider):
        '''
        以spider启动的request为参数。必须返回包含Reuest对象的可迭代对象。

        :param start_requests:
        :param spider:
        :return:
        '''
        pass
    def process_spider_output(self,response,result,spider):
        for r in result:
            if isinstance(r,Request):
                curl = canonicalize_url(r.url)
                if curl != r.url:
                    r = r.replace(url=curl)
            # yield类似于递归返回一样，返回之后，还会回到这里，继续执行剩下代码
            yield r

```

### <a id="six"></a>6. 扩展

扩展就是将自定义功能绑定到Scrapy中。

>1. 配置扩展

将扩展的类路径和序号值添加到在settings的EXTENSIONS中。扩展的实例化代码必须要在构造函数中执行。

扩展有三个状态，可用的，开启的，禁用的。如果要禁用一个扩展，只需要设置序号为None.

>2. 定制扩展

```python
# 扩展就是将自定义功能绑定到Scrapy中。
import logging

from scrapy import signals
from scrapy.exceptions import NotConfigured

logger = logging.getLogger(__name__)

class SpiderOpenCloseLogging(object):

    def __init__(self,item_count):
        self.item_count = item_count
        self.items_scraped = 0

    # 通过Crawler实例，我们可以访问到settings，signals，stats以便控制爬虫的行为。
    @classmethod
    def from_crawler(cls,crawler):
        # 首先检查配置
        if not crawler.settings.getbool('MYEXT_ENABLED'):
            raise NotConfigured
        #从setting中获取MYEXT_ITEMCOUNT的值
        item_count = crawler.settings.getint('MYEXT_ITEMCOUNT',1000)
        #初始化扩展实例
        ext = cls(item_count)
        # 将扩展的spider_opened,spider_closed和item_scraped连接到相应的信号处，进行除法。
        crawler.signals.connect(ext.spider_opened,signal=signals.spider_opened)

        crawler.signals.connect(ext.spider_closed,signal=signals.spider_closed)

        crawler.signals.connect(ext.items_scraped,signal=signals.item_scraped)
        #返回实例
        return ext

    def spider_opened(self,spider):
        logger.info('opened spider %s',spider.name)

    def spider_closed(self,spider):
        logger.info('closed spider %s',spider.name)

    def item_scraped(self,item,spider):
        self.items_scraped +=1
        if self.items_scraped % self.item_count == 0 :
            logger.info('scraped %s items',self.items_scraped)

```

spder的信号量。

![a1a](/images/spider_kz1.PNG)
![a1a](/images/spider_kz2.PNG)
![a1a](/images/spider_kz3.PNG)

### <a id="seven"></a>7. 反爬

反爬主要包含：基于验证码，基于Headers,基于用户行为，基于动态页面的反爬措施。
对于验证码的反爬，可根据具体验证码类型进行破解。对于Headers的反爬则根据需要访问的网址的User-Agent和Referer字段进行检测。
对于基于用户行为的反爬，通过构建Cookie池或IP池进行绕过，或对访问频率进行限制。对于动态页面的反爬，则通过selenium进行模拟发送获取数据。

>1. Scrapy设置下载延时和自动限速

>>1. 设置下载延时
>>> 在settings中设置DOWNLOAD_DELAY,例如DOWNLOAD_DELAY=2,进行设置。
>>2. 设置随机下载延时
>>> 设置RANDOMIZE_DOWNLOAD_DELAY=True
>>3. 设置自动限速扩展
>>>设置AUTOTHROTILE_ENABLED=True,AUTOTHROTILE_START_DELAY=初始下载时延，AUTOTHROTILE_MAX_DELAY=最高延迟.

>2. 代理VPN

可以了解一下Tor代理。