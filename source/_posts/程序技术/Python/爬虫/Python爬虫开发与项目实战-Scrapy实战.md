---
title: Python爬虫开发与项目实战-Scrapy实战
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

### 介绍

借助Scrapy进行知乎网站的爬取。

### 1. 创建一个Scrapy项目

scrapy startproject zhihuCrawl //创建Scrapy项目
cd zhihuCrawl 
scrapy genspider -t crawl zhihu.com zhihu.com  //这里是生成一个spider模板

### 2. 编写Item和spiders和parse方法

创建好项目后，就只需要根据需求编写相应的Item用于保存数据，编写spiders来处理相应的页面请求，以及各个页面之间的跳转。每次请求完成后的操作，通过callback方法指定处理方法用于处理请求。

### 3. 编写Pipeline数据存储

模板代码如下所示：
```python
class ZhihucrawlPipeline:

    def __init__(self,mongo_uri,mongo_db):
        self.mongo_uri = mongo_uri
        self.mongo_db = mongo_db

    @classmethod
    def from_crawler(cls,crawler):
        return cls(
            mongo_uri=crawler.settings.get('MONGO_URI'),
            mongo_db=crawler.settings.get('MONGO_DATABASE','zhihu')
        )

    def open_spider(self,spider):
        self.client = pymongo.MongoClient(self.mongo_uri)
        self.db = self.client[self.mongo_db]

    def close_spider(self,spier):
        self.client.close()


    # 处理item，用于数据保存
    def process_item(self, item, spider):
        self.db.UserInfo.insert(dict(item))
        return item

```