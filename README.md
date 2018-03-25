# crawl_demo
爬虫小demo


## 爬取前程无忧招聘信息并写入excel


#### 1.进入到前程无忧的官网，输入关键字“Python”，我们会得到下面的页面:
![](https://github.com/bjheweihua/crawl_demo/blob/master/51job/51j0b_1.png)  

#### 2.罗列相关信息"职位名"、"公司名"、"工作地点"、"薪资"、"发布时间"。
#### 把这些信息爬取下来！审查元素找到我们所需信息所在的标签，再写一个正则表达式把元素筛选出来就可以了！
![](https://github.com/bjheweihua/crawl_demo/blob/master/51job/51job_2.png)  

顺理成章得到这样一个正则表达式：
```
reg = re.compile(r'class="t1 ">.*? <a target="_blank" title="(.*?)".*? <span class="t2"><a target="_blank" title="(.*?)".*?<span class="t3">(.*?)</span>.*?<span class="t4">(.*?)</span>.*? <span class="t5">(.*?)</span>',re.S)
```


#### 3.代码实现：
```
# -*- coding:utf-8 -*-
import urllib
import xlwt  #用来创建excel文档并写入数据
import re
    
#获取原码
def get_content(page):
    url ='http://search.51job.com/list/000000,000000,0000,00,9,99,python,2,'+ str(page)+'.html'
    a = urllib.urlopen(url) #打开网址
    html = a.read().decode('gbk')#读取源代码并转为unicode
    return html

def get(html):
    reg = re.compile(r'class="t1 ">.*? <a target="_blank" title="(.*?)".*? <span class="t2"><a target="_blank" title="(.*?)".*?<span class="t3">(.*?)</span>.*?<span class="t4">(.*?)</span>.*? <span class="t5">(.*?)</span>',re.S)#匹配换行符
    items = re.findall(reg,html)
    return items

def excel_write(items, index):

    #爬取到的内容写入excel表格
     for item in items:#职位信息
         for i in range(0, 5):
             ws.write(index,i,item[i])#行，列，数据
         index += 1

newTable = "51job.xls"#表格名称
wb = xlwt.Workbook(encoding='utf-8')#创建excel文件，声明编码
ws = wb.add_sheet('sheet1')#创建表格
headData = ['招聘职位','公司','地址','薪资','日期']#表头部信息
for colnum in range(0, 5):
    ws.write(0, colnum, headData[colnum], xlwt.easyxf('font: bold on'))  # 行，列

for each in range(1,100): #请求100页数据
    index = (each-1)*50+1 #每页50条数据
    print index
    excel_write(get(get_content(each)),index)
wb.save(newTable)
print 'successed!'

```
