---
title: selenium+Chrome(79版本以上)反爬
categories:
    - 爬虫
tags:
    - Python
    - 爬虫
---

#### 简介

<font size=2>&nbsp;&nbsp;&nbsp;最近在学习爬虫的过程中碰到一个奇怪的现象，当我在正常的浏览器页面访问网站的时候，能够正常访问到网页的数据。然而，当我通过selenium进行爬取网页数据的时候，出现服务器异常的提醒。正常访问能够访问，而通过selenium访问的时候却报错，这是为什么呢？通过查阅相关资料，可以得出，碰上反爬虫了。</font>

>分析

<font size=2>为什么可以得出碰上反爬虫了呢？通常我们通过selenium进行爬取网页的时候，可以通过 window.navigator.webdriver检测是否使用了webdriver。我们可以试一下。在正常访问的网页中输入 window.navigator.webdriver，通常返回的是false或undifine，而当我们通过selenium访问浏览器的时候，在网页控制台输入 window.navigator.webdriver，会返回true。假如我是网页的设计者，我就会先在网页加载的时候写下这么一行代码 :
</font>
```javascript
    if (window.navigator.webdriver){
        alert("爬虫爬的好，牢饭吃到饱")
    }else{
        alert("正常页面")
    }
```
<font size=2>
所以，当我们爬取网页出现上述情况的时候，很大可能浏览器存在反selenium。下面介绍一下如何解决反爬，常见的反反爬方案包含：设置参数 excludeSwitches、mitmproxy 拦截过滤、cdp 命令。
</font>

### 2. 解决方案

&nbsp;&nbsp;&nbsp;&nbsp;要想解决上述的问题，我们可以让window.navigator.webdriver返回false即可。
```python
options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-automation'])
driver = webdriver.Chrome(executable_path='E:\codeEverment\python\chromedriver.exe', options=options) # chrome驱动 
                                 

script = "Object.defineProperty(navigator, 'webdriver', { get: () => undefined})"
    
driver.execute_cdp_cmd("Page.addScriptToEvaluateOnNewDocument", {"source": script})
```
要见检查是否避免浏览器对webdriver的检测，可以通过selenium访问[https://intoli.com/blog/not-possible-to-block-chrome-headless/chrome-headless-test.html](https://intoli.com/blog/not-possible-to-block-chrome-headless/chrome-headless-test.html),如果页面显示全绿，那么就表明避免成功，反之失败。
