---

title: Python数据处理-阅读笔记二——数据清洗之匹配与格式化
categories: 
    - Python数据处理
tags:
    - Python
    - 数据处理
---

#### 为什么要清洗数据

数据清洗可以让数据更容易存储，搜索和复用。通常我们获取到的数据可能会存在格式不一致的问题，由其是当数据来源于多个数据集的时候，需要对数据进行格式化处理。

#### 什么是数据清洗

数据清洗也就是修改数据使其满足新的标准化数据格式。

#### 数据清洗过程

>1. 简单的目视分析，观察文件的结构

观察数据字段，寻找不一致的地方。

>2. 数据格式化

将杂乱无章的数据转化为可读性较强的格式。

>3. 找出离群值和不良数据

1. 离群值：也就是指和数据集不一致的数据。

判断数据的有效性，通过过数据的来源来判断数据的偏差。对于获取的数据集，检查数据是否有不一致的地方？也就是说数据值里面是否存在错误。可以通过过遍历数据集，判断数据类型是否匹配，是否缺失数据。

离群值出现在个别的数据明显有别于数据集其他部分的时候。离群值会告诉我们数据的一部分情况。有时候，去掉它们会展现出一个明显的趋势；有些时候，离群值本身会透露出很多信息

>类型离群值（type outlier）：如果存在数据的类型不匹配，则出现了类型离群值。如果要判断数据集中的类型离群情况，则可以对对应的字段进行分析，然后统计各个类型出现的次数，用于分析比对。

2. 重复值：重复的原始数据

对于重复的数据一般需要删除。删除的方法可以考虑如下方法：
>1. 通过set集合进行去重

>2. 模糊匹配

通过模糊匹配进行寻找和合并重复值。脏数据可以是用户输入时粗心所造成的拼写和语法错误。
可以借助fuzzywuzzy库进行模糊匹配。

```python
# 提供模糊匹配
from fuzzywuzzy import fuzz
from fuzzywuzzy import process
my_records = [{
    'favorite_book': 'Grapes of Wrath',
    'favorite_movie': 'Free Willie',
    'favorite_show': 'Two Broke Girls',
}, {
    'favorite_book': 'The Grapes of Wrath',
    'favorite_movie': 'Free Williy',
    'favorite_show': '2 Broke Girls',
}]

# 通过fuzz模块的ratio函数，接受两个字符串作比较，返回两个字符串序列的相似程度(1-100)。
print(fuzz.ratio(my_records[0].get('favorite_book'), my_records[1].get('favorite_book')))
print(fuzz.ratio(my_records[0].get('favorite_movie'), my_records[1].get('favorite_movie')))
print(fuzz.ratio(my_records[0].get('favorite_show'), my_records[1].get('favorite_show')))

# 返回更加准确的匹配结果（根据子字符串进行匹配），建议采用第一种匹配方法
print(fuzz.partial_ratio(my_records[0].get('favorite_book'), my_records[1].get('favorite_book')))

# token_sort_ratio函数，匹配字符串时，不考虑单词顺序。
my_records = [{'favorite_food': 'cheeseburgers with bacon',
               'favorite_drink': 'wine, beer, and tequila',
               'favorite_dessert': 'cheese or cake',
               },
              {'favorite_food': 'burgers with cheese and bacon',
               'favorite_drink': 'beer, wine, and tequila',
               'favorite_dessert': 'cheese cake',
               }]

print(fuzz.token_sort_ratio(my_records[0].get('favorite_food'), my_records[1].get('favorite_food')))
print(fuzz.token_set_ratio(my_records[0].get('favorite_food'), my_records[1].get('favorite_food')))


#对于有限选项的数据的匹配,返回的是最有可能的选项列表
#最佳匹配，通过extractone选择
choice = ['Yes','No','Maybe','N/A']
print(process.extract('ya',choice,limit=2))
print(process.extract('ya',choice))
print(process.extract('nope',choice,limit=2))
print(process.extract('nope',choice))

```
>3. 正则匹配

\w:匹配任意字母和数字和下划线。\d:匹配数字。\s:匹配任意一个空格。
+:匹配一个或多个模式或字符。\.:匹配.字符。*:匹配零个或多个字符或模式。
|：匹配多个模式中的一个。[]或()：字符类和字符组。

```python
word = '\w+'
sentence = 'Here is my sentence'
print(re.findall(word,sentence)) #任意位置开始匹配
search_result = re.search(word,sentence) #任意位置开始匹配
print(search_result.group())
match_result = re.match(word,sentence) #从字符串开头匹配
print(match_result.group())
```