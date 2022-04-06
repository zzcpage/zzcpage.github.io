---

title: Python数据处理-阅读笔记一——数据处理介绍
categories: 
    - Python数据处理
tags:
    - Python
    - 数据处理
---

#### 数据处理

数据处理是指将杂乱的或未加工的数据源转换成有用的信息。先寻找原始数据源，并判断
其价值：这些数据集的数据质量有多好？它们与你的目标是否相关？能否找到更好的数
据源？在对数据进行解析与清洗后，数据集变得可用，这时你可以利用工具和方法（如
Python 脚本）来帮你分析数据，并以报告的形式展示结果。这样你可以将无人问津的数据
变得清晰可用。

#### 数据处理的作用

通过编程进行分析数据，得出结论，并将结论清楚的传达给别人。

#### 数据处理的流程

分析问题->收集数据，数据存储->数据清洗(研究，匹配，格式化，归一化和标准化，脚本化)->数据分析

![a1](/images/datawranling/数据清洗.PNG)


#### 常见格式化数据

1. CSV数据

CSV文件是指将数据列用逗号分隔的文件，文件的扩展名是.csv。
```python
# CSV数据
def csv():
    import csv
    #读取csv文件
    csvfile = open('data-text.csv','rb')
    reader = csv.reader(csvfile)
    # 以字典形式读取
    reader = csv.DictReader(csvfile)
    for row in reader:
        print(row)
    #保存csv文件
    header = ['colname','colname']
    content = [(1,2),(2,3)]
    with open('data-text.csv','w+') as f:
        csvwrite = csv.writer(f)
        csvwrite.writerow(header)
        csvwrite.writerows(content)
```
2. JSON数据

JSON数据是数据传输最常用的格式之一。
```python
# JSON数据
def JSON():
    # JSON数据读取
    json_data = open('data-text.json').read()
    data = json.loads(json_data)
    for item in data:
        print(item)
    #JSON数据保存
    datas = []
    with open('data-text.json','w+') as f:
        json.dump(datas,f)
```

3. XML数据

XML 是一种标记语言，也就是说，它具有包含格式化数据的文档结构。
XML 文档本质上只是格式特殊的数据文件。
```python
#XML数据
def XML():
    #读取XML文件
    from xml.etree import ElementTree as ET
    tree = ET.parse('data-text.xml')
    root = tree.getroot()
    data  = root.find('data')
    all_data = []
    for d in data:
        record = {}
        #获取标签属性
        print(d.attrib.keys()[0])
        print(d.attrib['key'])

```

4. Excel文件

Python解析Excel文件使用的是xlrd库(读取Excel文件),xlwt(向Excel文件写入，并设置格式)。xlutils(一组Excel高级操作工具，需要安装xlrd和xlwt)。

Excel文件的读取，首先要确定在哪个表(sheet)。然后根据表可以获取表内的数据。
>打开Excel文件
book = xlrd.open_workbook()
>获取工作表对象
sheet = book.sheet_by_name(表名称)
>获取数据
#获取工作表的总行数
print(sheet.nrows)
#遍历工作表的每一行
for i in range(sheet.nrows):
    print(sheet.row_values(i))
实例代码：
```python
#解析Excel文件
def Excel():
    import xlrd
    import xlwt
    import xlutils
    #读取xlsx数据
    book = xlrd.open_workbook('1.xlsx')
    #Excel工作簿可以有多个标签(tab)或工作表(sheet)
    #所以需要找到包含目标数据的工作表
    #获取工作表名称
    for sheet in book.sheets():
        print(sheet.name)
    #根据工作表名称，获取工作表对象
    sheet = book.sheet_by_name('Table 9 ')
    print(sheet)
    #获取工作表的总行数
    print(sheet.nrows)
    #遍历工作表的每一行
    for i in range(sheet.nrows):
        #print(sheet.row_values(i))
        row = sheet.row_values(i)
        #读取列数据
        for cell in row:
            print(cell)
```

5. PDF文件

通过PyPDF2模块进行读取pdf，但是只能够读取文本。
```python
def PDF():
    import PyPDF2
    #读取PDF
    pdffile = open('')
    pdfreader = PyPDF2.PdfFileReader(pdffile)
    print('页数'+str(pdfreader.pages))
    print(pdfreader.getPage(0))#获取第一页内容
    print(pdfreader.getPage(0).extractText())#获取第二页内容
    #写入PDF 
    pdfwriter = PyPDF2.PdfFileWriter()
    pdfwriter.addPage('adadad')
    with open('test.pdf','wb') as wf:
        pdfwriter.write(wf)

```

 