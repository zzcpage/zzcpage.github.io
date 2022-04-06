---

title: Python数据处理-阅读笔记三——数据分析
categories: 
    - Python数据处理
tags:
    - Python
    - 数据处理
---

agate是Python中的一个数据分析库，这里将使用agate对数据进行分析。
agate接受三个参数，表格数据，数据列标题，数据列类型。
所以创建一个agate需要提供以上三个参数。
例如：table = agate.Table(country_rows , titles, types)。其中country_rows就是数据，titles就是每列的标题，types就是每列的数据类型。

agate有如下几种数据类型：
```python
text_type = agate.Text()
number_type = agate.Number()
boolean_type = agate.Boolean()
data_type = agate.Date()
```
对于Excel，如果要获取列的数据类型，可以根据映射表进行获取，Excel的映射表为ctype_text。获取实例如下所示：
```python
types = []
for v in example_row:  # 获取每列元素的类型
    value_type = ctype_text[v.ctype]  # 根据映射表获取类型
    if value_type == 'text':
        types.append(text_type)
    elif value_type == 'number':
        types.append(number_type)
    elif value_type == 'xldate':
        types.append(data_type)
    else:  # 其它类型都以文本类型处理
        types.append(text_type)
```

#### agate的常用方法

```python
#agate的内置方法

#1. 获取表属性
print(table.column_names) #获取列名称

#2. 数据显示
table.print_table(max_columns=7)
table.print_json()
table.print_csv()

#3. 排序方法
# 显示根据列Total (%) 最高值的前十个数据
most_egregious = table.order_by('Total (%)',reverse=True).limit(10)
for r in most_egregious:
    print(r)

print('The max rate in Female')
most_females = table.order_by('Female',reverse=True).limit(10)
for r in most_females:
    print(r)

#4. 条件搜索
female_data = table.where(lambda r:r['Female'] is not None)#通过where条件搜索
most_females = female_data.order_by('Female',reverse=True).limit(10)
for r in most_females:
    print('{}: {}%'.format(r['Countries and areas'], r['Female']))

#查找,通过find方法,查找第一个匹配的数据
has_por = table.where(lambda r:r['Rural'] is not None)
first_match = has_por.find(lambda x:x['Rural']>50)
print(first_match['Countries and areas'])
# 5. 统计学方法
# 计算平均值的方法
#agate.Mean() 统计学方法和列名称来返回列的数学均值。

#has_por的作用（去除空列，计算剩下数据的平均值）
has_por = table.where(lambda r:r['Place of residence (%) Urban'] is not None)
print(has_por.aggregate(agate.Mean('Place of residence (%) Urban')))
#计算最大值
print(has_por.aggregate(agate.Max('Place of residence (%) Urban')))
#计算最小值
print(has_por.aggregate(agate.Min('Place of residence (%) Urban')))

#6. 计算新的排序列,计算排名
# 通过compute和agate.Rank()方法
#，compute 是一个非常好用的工具，它基于一个数据列（或多个数据列）来计算一个新的数据列。
#这里创建新的表格，表格包含Total Child Labor Rank和Total (%)两个字段，根据Total (%)列进行排序
ranked = table.compute([('Total Child Labor Rank',agate.Rank('Total (%)',reverse=True)),])
for row in ranked.order_by('Total (%)',reverse=True).limit(20).rows:
    print(str(row['Total (%)'])+" "+str(row['Total Child Labor Rank']))


#自定义排序方法
def reverse_percent(row):
    return 100-row['Total (%)']

ranked = table.compute([('Children not working (%)',agate.Formula(number_type,reverse_percent)),])

ranked = ranked.compute([('Total Child Labor Rank',agate.Rank('Children not working (%)')),])
for row in ranked.order_by('Total (%)',reverse=True).limit(20).rows:
    print(str(row['Total (%)'])+" "+str(row['Total Child Labor Rank']))

```

#### 联结

agate联结表的方法：join。join类似于SQL中的联结，包括左外联结，右外联结，内联结，全外连接。如果要联结B，并且保证不丢失A的数据：joined_table = A.join(B,'A-_COL','B_COL')。如果要使用内联结，只保留匹配项，则可以设置inner=True。



#### 识别数据的相关性

对于数据集相关性的分析，可以使用的工具库有agate,pandas,numpy,scipy等。

相关系数可以告诉我们数据是否关联，以及一个因子是否会影响另一个因子。
>皮尔森相关系数：皮尔森相关系数在-1到1之间波动，0意味着无相关性，-1和1意味着相关性很强。

对于导入：import agatestats的问题。
>pip install agate-stats进行下载

```python
#利用Numpy计算童工雇佣率和政府腐败指数之间的相关性
#这里通过皮尔森系数进行参考
rs = numpy.corrcoef([float(t) for t in cpi_and_cl.columns['Total (%)'].values()],
               [float(t) for t in cpi_and_cl.columns['CPI 2013 Score'].values()])[0,1]

# 由结果数据，可以得到两个系数之间的相关性
print(rs)

```

#### 识别离散数据

```python
#找出离群值
#找出离群值的方法：一是使用标准差，另一种是使用绝对中位差。
#agatestats.patch()
std_dev_outliers = cpi_and_cl.stdev_outliers(
    'Total (%)',deviations=3,reject=False
)
print(len(std_dev_outliers))
std_dev_outliers = cpi_and_cl.stdev_outliers(
    'Total (%)',deviations=10,reject=False
)

print(len(std_dev_outliers))

#使用平均绝对偏差检查Total(%)列数据的偏差
mad = cpi_and_cl.mad_outliers('Total (%)')
for r in mad.rows:
    print(r['Country / Territory'],r['Total (%)'])
```

#### 分组和聚合

分组和聚合主要是针对从属关系的数据。例如县市和省份的关系，国家和大洲的关系。通常通过分组和聚合对这些数据进行处理。

```python
# 创建分组
country_json = json.loads(open(r'G:\Temp\data-wrangling-master\data\chp9\earth-cleaned.json').read())
print(country_json)
country_dict = {}
for dict in country_json:
    country_dict[dict['name']] = dict['parent']

def get_country(county_row):
    return country_dict.get(county_row['Country / Territory'].lower())
# 创建新的列，continent，沿用相同的表名称

cpi_and_cl = cpi_and_cl.compute([('continent',agate.Formula(agate.Text(),get_country)),])
for r in cpi_and_cl.rows:
    print(r['continent'])
print('is eof')
# 这是检测结果为None的数据项
no_continent = cpi_and_cl.where(lambda x:x['continent'] is None)
for r in no_continent.rows:
    print(r['Country / Territory'])

# 分组代码
# 借助group_by进行分组,group_by返回一个字典，键是大洲名称，值是一个新表，包含所有州的行记录。
grp_by_cont = cpi_and_cl.group_by('continent')
print(grp_by_cont)
# 获取各个州的国家
for cont,table in grp_by_cont.items():
    print(cont,len(table))
#聚合,借助aggregate方法
agg = grp_by_cont.aggregate([
    ('cl_mean',agate.Mean('Total (%)')),
    ('cl_max',agate.Max('Total (%)')),
    ('cpi_median',agate.Median('CPI 2013 Score')),
    ('cpi_min',agate.Min('CPI 2013 Score'))
])
agg.print_table()
agg.print_bars('continent','cl_max')

```

#### 数据分析

数据探索和数据分析之间有哪些不同？当分析数据时，我们提出问题并且尝
试使用已有的数据回答这些问题。我们可能会对数据集进行组合和分组，以
构建一个统计可用的样本。而在数据探索中，我们只是想要研究数据集的一
些趋势和属性，不尝试去回答特定的问题或得出确定的结论。

>1. 分离和聚焦数据

将需要研究的数据对象从数据集中分离，对研究对象进行单独分析。

>2. 

#### 后记

agate-stat 库有一些有趣的统计方法我们还没有探索过。你可以通过 GitHub 跟踪最新的
发布和功能（https://github.com/onyxfish/agate-stats）。
除此之外，我们建议你继续探索 numpy。你可以使用 numpy 来计算百分位数值（https://docs.
scipy.org/doc/numpy-dev/reference/generated/numpy.percentile.html）。你同样可以更深入地
使用 scipy，使用 z 得分统计方法来确定离群值（https://docs.scipy.org/doc/scipy/reference/
generated/scipy.stats.mstats.zscore.html）。
如果你有时间敏感的数据，numpy 能够计算数据之间列与列的变化，从而探索随时间推移数
据的变化（https://docs.scipy.org/doc/numpy/reference/generated/numpy.diff.html）。agate 同样
可以计算时间相关数据中列的变化（http://agate.readthedocs.io/en/1.0.0/tutorial.html#computing-new-columns）。不要忘记在组成时间列的时候使用时间类型，因为这样做你能够在时间上做
一些有趣的分析（例如随时间变化的百分比变化或一系列时间变化的映射）。
如果你想要通过更多的统计方法来探索数据，可安装 latimes-calculate 库（http://latimes-calculate.readthedocs.io/en/latest/index.html）。这个库有很多计算方法，同样还有一些有趣的
地理数据分析工具。如果你获取了一些地理数据，这个库可以提供一些有价值的工具来帮
你更好地理解、映射和分析数据。
如果你想要更深入进行数理计算和分析，我们强烈推荐 Wes McKinney 的《利用 Python 进
行数据分析》一书。这本书介绍了一些更健壮的数据分析库，包括 pandas、numpy 和 scipy
系列的库等
