---

title: Python数据处理-阅读笔记二——数据获取和存储
categories: 
    - Python数据处理
tags:
    - Python
    - 数据处理
---

数据处理的先决条件就是找到要研究的数据集。

数据集因该具备如下几个性质。

>1. 可信性

找到的数据集因该是可靠的，可信的。

>2. 真实性

找到的数据因该是真实的。

>3. 可读性

数据容易获取。

>4. 数据寿命

了解数据更新的频率。

#### 数据的获取方式

>1. 政府网站获取

如果需要分析研究某个城市或者某个国家的问题，可以通过政府网站寻找到相关的数据信息。例如：
```python
• 开放城市项目（http://www.opencitiesproject.org/）
• 开放尼泊尔（http://data.opennepal.net/）
• 中国国家统计局（http://www.stats.gov.cn/english/）
• 香港开放数据（https://opendatahk.com/）
• 印尼政府开放数据（http://data.go.id/
• 加拿大统计数据（http://www.rdc-cdr.ca/datasets-and-surveys）
• 加拿大开放数据（http://open.canada.ca/en）
```

>2. 组织数据或非政府组织数据

如果需要获取某领域的数据，可以查看相关领域的组织机构是否提供了相应的数据。例如气候变化数据，国际商贸数据和全球运输数据等，可以通过国际组织进行信息数据的获取。例如：
```python
• 联合国开放数据（http://data.un.org/）
• 联合国发展计划署数据（http://open.undp.org/）
• 开放知识基金会（https://okfn.org/）
• 世界银行数据（http://data.worldbank.org/）
• 维基解密（https://wikileaks.org/）
• 国际援助透明度数据集（http://www.iatiregistry.org/）
• DataHub（https://datahub.io/）
• 人口资料局（http://www.prb.org/DataFinder.aspx）
```

>3. 教育数据和大学数据

通过世界各地大学和研究生部获取数据集。例如
```python
• Lexis Nexis（http://www.lexisnexis.com/）
• 谷歌学术搜索（https://scholar.google.com/）
• 康奈尔大学 arXiv 项目（http://arxiv.org/）
• UCI 机器学习数据集（http://archive.ics.uci.edu/ml/）
• 通用数据集倡议（http://www.commondataset.org/）
```

>4. 医学数据和科学数据

通过一些医学研究部门和组织可以获取优质的数据资源。
```python
• 开放科学数据云（https://www.opensciencedatacloud.org/publicdata/）
• 开放科学目录（http://www.opensciencedirectory.net/）
• 世界卫生组织数据（http://www.who.int/gho/database/en/）
• Broad 研究所开放数据（http://www.broadinstitute.org/scientific-community/data）
• 人类连接组项目（神经通路映射）（http://www.humanconnectomeproject.org/）
• UNC 精神病基因组协会（http://www.med.unc.edu/pgc/）
• 社会科学数据集（http://3stages.org/idata/）
• CDC 医学数据（http://www.cdc.gov/nchs/fastats/）

```

>5. 众包数据和API

一些社交网络和论坛能够提供大量的数据记录。

```python
• Broad 研究所开放数据（http://www.broadinstitute.org/scientific-community/data）
• 人类连接组项目（神经通路映射）（http://www.humanconnectomeproject.org/）
• UNC 精神病基因组协会（http://www.med.unc.edu/pgc/）
• 社会科学数据集（http://3stages.org/idata/）
• CDC 医学数据（http://www.cdc.gov/nchs/fastats/）

```

#### 数据的存储

>1. 关系数据库

MySQL和PostgreSQL

>2. 非关系数据库

NoSQL和MongoDB

>3. 云存储

通过云服务器进行数据存储

>4. 其他数据存储

层次型数据格式(HDF)：HDF是基于文件的可扩展数据解决方案，可将大型数据库快速存储至文件系统(本地或其他位置)。

Hadoop:Hadoop是一个大数据分布式存储系统，可以跨集群存储并处理数据。